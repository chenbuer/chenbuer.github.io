---
title: Java操作Redis
date: 2012-02-28 14:03:20
categories: JAVA
tags: JAVA
---
### 一、利用jedis客户端直接操作reids

[基础操作参考](http://www.cnblogs.com/edisonfeng/p/3571870.html)

1.1 概念： 切片池 & 非切片池
关于切片池和非切片池的区别，  一般项目基本都使用非切片池；切片池主要用于分布式项目，可以设置主从Redis库。 
<!--more-->
1.2 获取redis客户端
```java
public class RedisClient {

    private Jedis jedis;// 非切片客户端连接

    private JedisPool jedisPool; //非切片连接池

    private ShardedJedis shardedJedis; //切片客户端链接

    private ShardedJedisPool shardedJedisPool;// 切片链接池

    //初始化非切片池
    private void initialPool(){
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxActive(20);
        config.setMaxIdle(5);
        config.setMaxWait(10001);
        config.setTestOnBorrow(false);

        jedisPool = new JedisPool(config, "127.0.0.1", 6379);
    }

    // 初始化切片池
    private void initialShardedPool(){
        // 配置
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxActive(20);
        config.setMaxIdle(5);
        config.setMaxWait(10001);
        config.setTestOnBorrow(false);
        // slave
        List<JedisShardInfo> shards = new ArrayList<JedisShardInfo>();
        shards.add(new JedisShardInfo("127.0.0.1", 6379, "master"));

        // const
        shardedJedisPool = new ShardedJedisPool(config, shards);
    }

    public RedisClient(){
        initialPool();
        initialShardedPool();
        shardedJedis = shardedJedisPool.getResource();
        jedis = jedisPool.getResource();
    }

    public Jedis getJedis() {
        return jedis;
    }

    public void setJedis(Jedis jedis) {
        this.jedis = jedis;
    }

    public ShardedJedis getShardedJedis() {
        return shardedJedis;
    }

    public void setShardedJedis(ShardedJedis shardedJedis) {
        this.shardedJedis = shardedJedis;
    }
}
```
1.3 key操作和String操作
```java
public class RedisTest {

    private RedisClient redisClient = new RedisClient();
    private Jedis jedis;
    private ShardedJedis shardedJedis;

    @Before
    public void getCli(){
        jedis = redisClient.getJedis();
        shardedJedis = redisClient.getShardedJedis();
    }

    @Test
    public void KeyOperate(){
        System.out.println("===key===");

        // 清空数据
        System.out.println("清空库中所有数据：" + jedis.flushDB());

        // 判断key是否存在
        System.out.println("判断key999键是否存在：" + shardedJedis.exists("key999"));
        System.out.println("新增key001及其对应值：" + shardedJedis.set("key001", "value001"));
        System.out.println("判断key001键是否存在：" + shardedJedis.exists("key001"));

        // 输出系统中所有key
        System.out.println("新增key002键值：" + shardedJedis.set("key002", "value002"));
        System.out.println("系统中所有键如下：");
        Set<String> keys = jedis.keys("*");
        Iterator<String> it = keys.iterator();
        while (it.hasNext()) {
            String key = it.next();
            System.out.println(key);
        }

        // 删除某个key，若key不存在，忽略该命令
        System.out.println("系统中删除key002：" + jedis.del("key002"));
        System.out.println("判断key002是否存在：" + shardedJedis.exists("key002"));

        // 设置key001的过期时间
        System.out.println("设置key001的过期时间为5秒" + jedis.expire("key001", 5));
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 查看某个key的剩余时间，单位【秒】
        System.out.println("查看key001的剩余生存时间："+jedis.ttl("key001"));
        // 移除某个key的生存时间
        System.out.println("移除key001的生存时间："+jedis.persist("key001"));
        System.out.println("查看key001的剩余生存时间："+jedis.ttl("key001"));

        // 查看key所存储的值的类型
        System.out.println("查看key所存储的值的类型" + jedis.type("key001"));

        /*
         * 一些其他方法：1、修改键名：jedis.rename("key6", "key0");
         *             2、将当前db的key移动到给定的db当中：jedis.move("foo", 1)
         */
    }

    @Test
    public void StringOperate(){
        System.out.println("====String Operate====");
        // 清空数据
        System.out.println("清空所有数据："+jedis.flushDB());

        System.out.println("=====增加=====");
        jedis.set("key001", "value001");
        jedis.set("key002", "value002");
        jedis.set("key003", "value003");
        System.out.println("已新增的3个键值如下：");
        System.out.println(jedis.get("key001"));
        System.out.println(jedis.get("key002"));
        System.out.println(jedis.get("key003"));

        System.out.println("=====删除=====");
        System.out.println("删除key003对应的键值：" + jedis.del("key003"));
        System.out.println("获取key003对应的值：" + jedis.get("key003"));

        System.out.println("=======修改======");
        // 1.直接修改
        System.out.println("直接覆盖key001数据"+jedis.set("key001", "value001-update"));
        System.out.println("获取key001对应的值：" + jedis.get("key001"));
        // 2.append
        System.out.println("在key002后面追加"+jedis.append("key002","-append2"));
        System.out.println("获取key002对应的值：" + jedis.get("key002"));

    }
}
```

### 二、PubSub机制
[参考链接](http://blog.csdn.net/lihao21/article/details/48370687)

### 三、从缓存中处理请求的两种方式：time&count
首先：两种模式下，时间阈值（假设10s） 和 count阈值（100） 都是有用的：
- 时间模式：若5s时已经add了100个，再等5s到10s后处理；若到了10s，还没add到100个，也处理
- cout模式（适合较多数据的时候）：若5s时add到了100，直接处理；若到了10s，还没add到100个，也处理

```java
if (MODE_TIME.equals(mode)) {
    do {
        if (buffer.size() < countThreshold) {
            C item = pool.outPool();
            if (item != null) {
                buffer.add(item);
            }
        }
    } while (System.currentTimeMillis() < end);
} else if (MODE_COUNT.equals(mode)) {
    do {
        C item = pool.outPool();
        if (item != null) {
            buffer.add(item);
        }
    } while (System.currentTimeMillis() < end && buffer.size() < countThreshold);
    pool.releaseLock();
}
```

