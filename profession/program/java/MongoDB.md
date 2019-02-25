# MongoDB
***
```java
package cn.nssas.eelantech.utils;

import com.mongodb.client.*;
import com.mongodb.client.result.UpdateResult;
import org.apache.log4j.Logger;
import org.bson.Document;
import org.bson.conversions.Bson;
import org.bson.types.ObjectId;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import static com.mongodb.client.model.Filters.eq;

/**
 * MongoDB工具类，只存在一个，使用单例模式实现，其方法使用示例在测试文档中有 MongoDBUtilTest
 * @author liu wei
 * @date 2019.1.29
 */
public class MongoDBUtil {
    private static final Logger logger = Logger.getLogger(MongoDBUtil.class);
    private MongoClient client = null;
    private MongoDatabase database = null;

    /**
     * 静态内部类单例模式实现
     */
    private static class MySingleHandler {
        private static MongoDBUtil instance = new MongoDBUtil();
    }

    public static MongoDBUtil getInstance(){
        return MySingleHandler.instance;
    }

    /**
     * 初始化，从配置文件中读取MongoDB地址、端口、数据库进行连接
     */
    private MongoDBUtil() {
        try {
            String host = (String) Holder.get("mongodb.host");
            String port = (String) Holder.get("mongodb.port");
            String url = "mongodb://" + host + ":" + port;
            client = MongoClients.create(url);
            database = client.getDatabase((String) Holder.get("mongodb.database"));
            logger.info("MongoDB connect successful!");
        }catch (Exception e) {
            System.out.println(e.getClass().getName() + ": " + e.getMessage());
        }
    }

    /**
     * 单条数据插入
     * @param data 数据
     * @param collectionName collection名
     */
    public void addOne(Map<String, Object> data, String collectionName) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.insertOne(new Document(data));
    }

    /**
     * 多条数据插入
     * @param maps 数据列表
     * @param collectionName 集合名
     */
    public void addMany(List<Map<String, Object>> maps, String collectionName) {
        List<Document> documents = new ArrayList<Document>();
        for(Map<String, Object> map : maps) {
            documents.add(new Document(map));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.insertMany(documents);
    }

    /**
     * 单条数据替换（更新）,不存在时可以强制插入
     * @param key 需要替换数据的字典中的key（对应MySQL表的字段名)
     * @param value key对应的值(对应MySQL表的字段值）
     * @param newMap 完整的新的数据
     * @param collectionName collection（表）名称
     * @param isInsert 如果数据库中不存在此数据，是否插入当条数据到数据库中
     */
    public void replaceOne(String key, Object value, Map<String, Object> newMap, String collectionName, boolean isInsert) {
        Bson filter = eq(key, value);
        String mongoIdKey = "_id";
        if (key.equals(mongoIdKey)) {
            filter = eq(key, new ObjectId((String) value));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        UpdateResult result = collection.replaceOne(filter, new Document(newMap));
        if(result.getMatchedCount() == 0 && isInsert) {
            MongoDBUtil.getInstance().addOne(newMap, collectionName);
        }
    }

    /**
     * 单条数据更新，多添加筛选更新，不存在时可以强制插入
     * @param condition 更新添加
     * @param map 完整的新的数据
     * @param collectionName collection（表）名称
     * @param isInsert 如果数据库中不存在此数据，是否插入当条数据到数据库中
     */
    public void replaceOne(Document condition, Map<String, Object> map, String collectionName, boolean isInsert) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        UpdateResult result = collection.replaceOne(condition, new Document(map));
        if(result.getMatchedCount() == 0 && isInsert) {
            MongoDBUtil.getInstance().addOne(map, collectionName);
        }
    }

    /**
     * 单条数据删除
     * @param key 需要数据数据的字典中的key（对应MySQL表的字段名)
     * @param value key对应的值(对应MySQL表的字段值）
     * @param collectionName collection（表）名称
     */
    public void deleteOne(String key, Object value, String collectionName) {
        Bson filter = eq(key, value);
        String mongoIdKey = "_id";
        if (key.equals(mongoIdKey)) {
            filter = eq(key, new ObjectId((String) value));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.deleteOne(filter);
    }

    /**
     * 单条数据删除(多筛选添加删除)
     * @param condition 删除筛选条件
     * @param collectionName collection（表）名称
     */
    public void deleteOne(Document condition, String collectionName) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.deleteOne(condition);
    }

    /**
     * 多条数据删除
     * @param key 需要数据数据的字典中的key（对应MySQL表的字段名)
     * @param value key对应的值(对应MySQL表的字段值）
     * @param collectionName collection（表）名称
     */
    public void deleteMany(String key, Object value, String collectionName) {
        Bson filter = eq(key, value);
        String mongoIdKey = "_id";
        if (key.equals(mongoIdKey)) {
            filter = eq(key, new ObjectId((String) value));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.deleteMany(filter);
    }

    /**
     * collection删除
     * @param collectionName 集合名
     */
    public void dropCollection(String collectionName) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.drop();
    }

    /**
     * 获取当前collection中文档的数量
     * @param collectionName 集合名
     * @param condition 筛选条件
     * @return 文档数量
     */
    public long count(String collectionName, Map<String, Object> condition) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        return collection.countDocuments(new Document(condition));
    }

    /**
     * 查询：自行构造丰富的多条件查询
     * @param condition 查询条件
     * @param collectionName collection（表）名称
     * @return 数据列表
     */
    public FindIterable<Document> query(Document condition, String collectionName) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        return collection.find(condition);
    }

    /**
     * 查询：单字段查询
     * @param key 字段名
     * @param value 字段值
     * @param collectionName collection（表）名称
     * @return 单条数据（如果查询出多个，也只返回一个）
     */
    public Document queryOne(String key, Object value, String collectionName) {
        Bson filter = eq(key, value);
        String mongoIdKey = "_id";
        if (key.equals(mongoIdKey)) {
            filter = eq(key, new ObjectId((String) value));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        return collection.find(filter).first();
    }

    /**
     * 查询：单字段
     * @param key 字段名
     * @param value 字段值
     * @param collectionName collection（表）名称
     * @return 查询到的所有数据
     */
    public FindIterable<Document> queryMany(String key, Object value, String collectionName) {
        Bson filter = eq(key, value);
        String mongoIdKey = "_id";
        if (key.equals(mongoIdKey)) {
            filter = eq(key, new ObjectId((String) value));
        }

        MongoCollection<Document> collection = database.getCollection(collectionName);
        return collection.find(filter);
    }

    /**
     * 建立索引
     * @param collectionName 集合表名
     * @param key 索引名
     * @param sort 升降序 1 升序 -1 降序
     */
    public void createIndex(String collectionName, String key, int sort) {
        MongoCollection<Document> collection = database.getCollection(collectionName);
        collection.createIndex(new Document(key, sort));
    }
}
```

```java
package utils;

import cn.nssas.eelantech.utils.MongoDBUtil;
import com.mongodb.client.FindIterable;
import org.bson.Document;
import org.junit.Test;

import java.util.*;

import static org.junit.Assert.assertEquals;

/**
 * MongoDBUtil 测试
 */
public class MongoDBUtilTest {

    @Test
    public void testDrop() {
        MongoDBUtil.getInstance().dropCollection("statisticsTest");
    }

    /**
     * 数据插入测试
     */
    @Test
    public void testAdd() {
        MongoDBUtil.getInstance().dropCollection("test");

        // 单条插入测试
        Map map = new HashMap() {{
            put("name", "test");
        }};
        MongoDBUtil.getInstance().addOne(map, "test");
        Document ret = MongoDBUtil.getInstance().queryOne("name", "test", "test");
        assertEquals(String.valueOf(ret.get("name")).equals("test"), true);

        // 多天插入测试
        List<Map<String, Object>> maps = new ArrayList<Map<String, Object>>();
        maps.add(new HashMap<String, Object>(16) {{
            put("name", "test2");
        }});
        maps.add(new HashMap<String, Object>(16) {{
            put("name", "test3");
        }});
        MongoDBUtil.getInstance().addMany(maps, "test");
        FindIterable<Document> rets = MongoDBUtil.getInstance().query(new Document(), "test");
        int count = 0;
        for (Document iret : rets) {
            count += 1;
        }
        assertEquals(3, count);

        MongoDBUtil.getInstance().dropCollection("test");
    }

    /**
     * 数据更新测试
     */
    @Test
    public void testReplaceSingleCondition() {
        MongoDBUtil.getInstance().dropCollection("test");

        Map map = new HashMap() {{
            put("name", "test3");
            put("value", 1);
        }};
        MongoDBUtil.getInstance().replaceOne("name", "test", map, "test", true);
        Document ret = MongoDBUtil.getInstance().queryOne("name", "test3", "test");
        assertEquals(1, ret.get("value"));

        MongoDBUtil.getInstance().dropCollection("test");

        MongoDBUtil.getInstance().addOne(map, "test");
        ret = MongoDBUtil.getInstance().queryOne("name", "test3", "test");
        assertEquals(1, ret.get("value"));

        map.put("value", 2);
        MongoDBUtil.getInstance().replaceOne("name", "test3", map, "test", false);
        ret = MongoDBUtil.getInstance().queryOne("name", "test3", "test");
        assertEquals(2, ret.get("value"));

        MongoDBUtil.getInstance().dropCollection("test");
    }

    @Test
    public void testReplaceMultiCondition() {
        MongoDBUtil.getInstance().dropCollection("test");
        Map map = new HashMap() {{
            put("name", "test3");
            put("type", 2);
            put("value", 1);
        }};
        MongoDBUtil.getInstance().replaceOne(new Document(map), map, "test", true);
        Document ret = MongoDBUtil.getInstance().queryOne("name", "test3", "test");
        System.out.println(ret.toString());
        assertEquals("test3", ret.get("name"));
        assertEquals(2, ret.get("type"));
        assertEquals(1, ret.get("value"));

        map.put("value", 2);
        Document condition = new Document("name", "test3").append("type", 2);
        MongoDBUtil.getInstance().replaceOne(condition, map, "test", false);
        ret = MongoDBUtil.getInstance().queryOne("name", "test3", "test");
        System.out.println(ret.toString());
        assertEquals("test3", ret.get("name"));
        assertEquals(2, ret.get("type"));
        assertEquals(2, ret.get("value"));
    }

    /**
     * 数据删除测试
     */
    @Test
    public void testDelete() {
        MongoDBUtil.getInstance().dropCollection("test");

        List<Map<String, Object>> maps = new ArrayList<Map<String, Object>>();
        maps.add(new HashMap() {{
            put("name", "test2");
        }});
        maps.add(new HashMap() {{
            put("name", "test3");
        }});
        MongoDBUtil.getInstance().addMany(maps, "test");

        MongoDBUtil.getInstance().deleteOne("name", "test2", "test");
        MongoDBUtil.getInstance().deleteMany("name", "test3", "test");
        FindIterable<Document> rets = MongoDBUtil.getInstance().queryMany("name", "test3", "test");
        int count = 0;
        for (Document iret : rets) {
            count += 1;
        }
        assertEquals(0, count);

        MongoDBUtil.getInstance().dropCollection("test");
    }

    /**
     * 数据查询测试
     */
    @Test
    public void testQuery() {
        MongoDBUtil.getInstance().dropCollection("test");

        Map map = new HashMap() {{
            put("name", "test");
            put("value", 3);
        }};
        MongoDBUtil.getInstance().addOne(map, "test");

        // 自行构造丰富条件查询，下面查询name等于test，value大于等于2小于5的数据
        Document condition = new Document("name", "test")
                .append("value", new Document("$gte", 2).append("$lt", 5));
        FindIterable<Document> rets = MongoDBUtil.getInstance().query(condition, "test");
        Document ret = rets.first();
        assertEquals(ret.get("value"), 3);

        // 或查询语句构造的两种方式
        Document query = new Document("$or", Arrays.asList(new Document("firstname", "abc"), new Document("firstname", "xyz")));
        query = new Document("firstname", new Document("$in", Arrays.asList("abc","xyz")));

        // 单字段单返回查询,查询name等于test的数据
        ret = MongoDBUtil.getInstance().queryOne("name", "test", "test");
        assertEquals(String.valueOf(ret.get("name")).equals("test"), true);

        // 单字段多返回查询，查询name等于test的数据
        MongoDBUtil.getInstance().addOne(map, "test");
        rets = MongoDBUtil.getInstance().queryMany("name", "test", "test");
        int count = 0;
        for (Document iret : rets) {
            count += 1;
        }
        assertEquals(2,count);

        MongoDBUtil.getInstance().deleteMany("name", "test", "test");
        rets = MongoDBUtil.getInstance().queryMany("name", "test", "test");
        count = 0;
        for (Document iret : rets) {
            count += 1;
        }
        assertEquals(0, count);

        MongoDBUtil.getInstance().dropCollection("test");
    }
}

```

## 参考链接
- [How to use “$or” operator using documents in mongodb using java](https://stackoverflow.com/questions/35982474/how-to-use-or-operator-using-documents-in-mongodb-using-java)
- [Create Indexes](http://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/indexes/)
- [Java实现对MongoDB的AND、OR和IN操作](https://blog.csdn.net/mydeman/article/details/6652387)
- [$ne不等于](http://cw.hubwiz.com/card/c/543b2f3cf86387171814c026/1/1/6/)