# Java Redis
***
## Maven 工程配置
```java
<!-- jedis redis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.8.1</version>
</dependency>
```

## 示例代码
```java
package util;

import org.apache.log4j.Logger;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/***
 * redis 操作工具类
 */
public class JedisUtil {
    private static final Logger logger = Logger.getLogger(JedisUtil.class);
    private static JedisPoolConfig jedisPoolConfig = null;
    private static JedisPool pool = null;

    /***
     * 初始化Redis配置连接
     */
    public static void init() {
        logger.info("Connecting redis");
        jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(30);
        String redisHost = (String) Holder.get("redis.host");
        int redisPort = Integer.valueOf((String) Holder.get("redis.port"));
        pool = new JedisPool(jedisPoolConfig, redisHost, redisPort);
        logger.info("Connected redis");
    }

    /***
     * 字符串设置
     * @param key
     * @param value
     */
    public static void setString(String key, String value) {
        if(pool == null) {
            init();
        }
        Jedis jedis = pool.getResource();
        jedis.set(key, value);
        jedis.close();
    }

    /***
     * 字符串获取
     * @param key
     * @return
     */
    public static String getString(String key) {
        if(pool == null) {
            init();
        }
        Jedis jedis = pool.getResource();
        String result = jedis.get(key);
        jedis.close();
        return result;
    }

    /***
     * 删除特定键
     * @param key
     */
    public static void delete(String key) {
        if(pool == null) {
            init();
        }
        Jedis jedis = pool.getResource();
        jedis.del(key);
        jedis.close();
    }

    /***
     * 设置特定键过期时间
     * @param key
     * @param seconds
     */
    public static void expire(String key, int seconds) {
        if(pool == null) {
            init();
        }
        Jedis jedis = pool.getResource();
        jedis.expire(key, seconds);
        jedis.close();
    }

    public static void main(String[] args) {
        String result = JedisUtil.getString("test");
        System.out.println(result);
        if(result == null) {
            System.out.println(result);
        }
    }
}
```

## 参考链接
- [Jedis使用教程完整版](https://www.jianshu.com/p/a1038eed6d44)
- [jedis:连接池(JedisPool)使用示例](https://blog.csdn.net/10km/article/details/77852075)
- [jedis api](http://www.javadoc.io/doc/redis.clients/jedis/2.9.0)
- [Intro to Jedis – the Java Redis Client Library](https://www.baeldung.com/jedis-java-redis-client-library)
- [Jedis如何设置key过期时间？](https://www.oschina.net/question/2830476_2241402)