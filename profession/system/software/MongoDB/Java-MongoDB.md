# Java MongoDB
***
```java
package cn.nudt681.nssas.utils;

import com.alibaba.fastjson.JSON;
import com.mongodb.*;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Filters;
import org.bson.Document;

import java.util.*;

import static com.mongodb.client.model.Filters.and;
import static com.mongodb.client.model.Filters.eq;

public class MongoDBUtil {
    private static MongoClient mongoClient = null;
    private static MongoDatabase mongoDatabase = null;
    private static MongoCollection<Document> collection = null;

    synchronized public static void init() {
        if (mongoDatabase == null) {
            mongoClient = new MongoClient( "172.18.0.24" , 27017 );
            mongoDatabase = mongoClient.getDatabase("attack_packets");
            collection = mongoDatabase.getCollection("attack");
            System.out.println("Connect to database successfully");
        }
    }

    public static void selectCollection(String collectionName) {
        if (mongoDatabase == null) {
            init();
        }
        collection = mongoDatabase.getCollection(collectionName);
    }

    public static boolean insert(Map map) {
        try {
            String ip = (String) map.get("ip");
            Document document = collection.find(eq("ip", ip)).first();
            if(document == null) {
                collection.insertOne(new Document(map));
            }
            else {
                collection.updateOne(Filters.eq("ip", ip), new Document("$set",new Document(map)));
            }
        }catch(Exception e){
            System.err.println( e.getClass().getName() + ": " + e.getMessage() );
            return false;
        }
        return true;
    }

    public static Document query(String key, String value) {
        return collection.find(eq(key, value)).first();
    }

    public static List<Map> find(BasicDBObject queryObj, int detail, int limit) {
        if (mongoDatabase == null) {
            init();
        }

        List<Map> result = new ArrayList<>();

        FindIterable<Document> cursor = collection.find(queryObj);
        cursor.sort(new BasicDBObject("time", -1));
        if(!String.valueOf(limit).equals("-1")) {
            cursor.limit(limit);
        }

        for(Document ret : cursor) {
            Map data = JSON.parseObject(ret.toJson());
            System.out.println(data);

            if(detail != 0) {
                Map temp = new HashMap();
                temp.put("id", data.get("id"));
                temp.put("name", data.get("name"));
                temp.put("type", data.get("type"));
                temp.put("time", data.get("time"));
                temp.put("tag", data.get("tag"));
                temp.put("count", data.get("count"));
                temp.put("attack_list", data.get("attack_list"));
                result.add(temp);
            }
            else {
                result.add(data);
            }

        }
        return result;
    }

    public static void main(String[] args) {
//        BasicDBList condList = new BasicDBList();
//        BasicDBObject ageObj = new BasicDBObject("time",new BasicDBObject("$gt", 1542603240));
//        BasicDBObject sexObj = new BasicDBObject("type", 3);
//        condList.addAll((Collection<?>) ageObj);
//        condList.addAll((Collection<?>) sexObj);
//        BasicDBObject cond= new BasicDBObject();
//        cond.put("$and", condList);

        List<DBObject> cond = new ArrayList<DBObject>();
//        BasicDBObject ageObj = new BasicDBObject("time",new BasicDBObject("$gt", 1542603240));
        BasicDBObject sexObj = new BasicDBObject("type", 3);
//        cond.add(ageObj);
        cond.add(sexObj);
        BasicDBObject andQuery = new BasicDBObject();
        andQuery.put("$or", cond);
        System.out.println(andQuery.toString());

        MongoDBUtil.find(andQuery, 0, -1);
    }
}
```

## 参考链接
- [java MongoDB查询（一）简单查询](https://www.cnblogs.com/luoaz/p/4691639.html)
- [java MongoDB查询（二）复杂查询](https://www.cnblogs.com/luoaz/p/4692716.html)
- [java中mongodb的查询及排序](https://blog.csdn.net/aboycoder/article/details/48358365)
- [MongoDB Driver Tutorials](http://mongodb.github.io/mongo-java-driver/3.9/driver/tutorials/connect-to-mongodb/)
- [设计模式（二）单例模式的七种写法](https://blog.csdn.net/itachi85/article/details/50510124)
- [MongoDB查询操作限制返回字段的方法](https://blog.csdn.net/hqzxsc2006/article/details/47447997)
- [Java操作MongoDB数据库方法详解](https://www.jb51.net/article/133539.htm)
- [Java通过mongo-java-driver-3.0+分页查询mongodb详解](http://www.voidcn.com/article/p-kaoovgif-bqs.html)
- [MongoDB分页的Java实现和分页需求的思考](https://www.cnblogs.com/woshimrf/p/mongodb-pagenation-performance.html)