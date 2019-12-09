# rsync
***
rsync -rP --rsh=ssh /home/oracle/TestDB/ oracle@192.168.1.173:/home/oracle/TestDB
rsync -av -e ssh --exclude='*.new' ~/virt/ root@centos7:/tmp
rsync -rvz -e 'ssh -p Port' --progress --remove-sent-files ./localpath root@remotehost:2345/remotepath

–exclude-from ‘/home/backup/exclude.txt
	- uploads 
	- download/softs/

## 参考链接
- [Linux rsync实现断点续传](https://www.bbsmax.com/A/ke5jVnxg5r/)
- [如何在使用 scp 命令时递归地排除文件](https://linux.cn/article-9292-1.html)
- [使用 rsync 同步的时候，可否指定 ssh 的端口号？](https://segmentfault.com/q/1010000002405966)
- [rsync命令排除文件和文件夹exclude](https://blog.csdn.net/love_xsq/article/details/77445953)