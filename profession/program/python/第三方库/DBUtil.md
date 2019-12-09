# DBUtil
***
```
import pymysql
from DBUtils.PooledDB import PooledDB
pool = PooledDB(pymysql,50,host='localhost',user='root',passwd='pwd',db='myDB',port=3306 charset="utf8") #5为连接池里的最少连接数
conn = pool.connection() #以后每次需要数据库连接就是用connection（）函数获取连接就好了
cur=conn.cursor()
SQL="select * from table1"
r=cur.execute(SQL)
r=cur.fetchall()
cur.close()
conn.close()
```

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import MySQLdb
import threading
import time
from DBUtils.PooledDB import PooledDB

'''
建立连接池，返回连接池地址
'''
def dbpool(ip,port,username,password,dbname,char_set='utf8'):
    connKwargs = {'host': ip, 'port': port, 'user': username, 'passwd': password, 'db': dbname,'charset': char_set}
    pool = PooledDB(MySQLdb, mincached=10, maxcached=10, maxshared=10, maxconnections=10, **connKwargs)
    return pool
'''
从连接池中取出一个连接，执行SQL
num：用于统计总的影响行数
'''
num=0
def dbconnect(db_pool):
    global num
    conn = db_pool.connection()
    cur = conn.cursor()
    try:
        cur.execute("select * from table_name")
        lock.acquire()
        num += cur.rowcount
        lock.release()
    except Exception as e:
        print e
    finally:
        cur.close()
        conn.close()

if __name__ == '__main__':
	'''
	lock：生成全局锁，用于执行语句中，被影响行数的统计值加锁使用，每次只允许一个线程修改被锁变量
	'''
    lock = threading.Lock()
    st = time.time()
    db_pool = dbpool(ip='127.0.0.1',port = 1234,username='root',password='1234',dbname ='try',char_set='utf8')
    '''
    同时连接MySQL执行的线程数要小于等于前面PooledDB中设置的maxconnections，如果大于这个量，会报异常：TooManyConnections。
    设置每次只跑10个线程，跑完后再循环。
    '''
    thread_list = []
    for i in range(100):
        t = threading.Thread(target=dbconnect,args=(db_pool,))
        thread_list.append(t)
    while len(thread_list)!=0:
        if len(thread_list)>10:
            thread_list_length = 10
        else:
            thread_list_length = len(thread_list)
        sub_thread_list = []
        for n in range(thread_list_length):
            sub_thread_list.append(thread_list[0])
            thread_list.remove(thread_list[0])
        for i in sub_thread_list:
            i.start()
        for j in sub_thread_list:
            j.join()
    et = time.time()
    print et - st,num
```

## 参考链接
- [Python-DBUtils 使用mysql数据库连接池提升效率](http://www.vuln.cn/8627)
- [连接MySQL，以及多线程、多进程连接MySQL续](http://blog.51cto.com/tryagaintry/1981817)
- [python3 实现mysql数据库连接池](https://www.cnblogs.com/renfanzi/p/7656142.html)