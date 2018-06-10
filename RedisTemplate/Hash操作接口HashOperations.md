## Hash操作接口HashOperations

RedisTemplate中Hash操作接口**HashOperations**,默认实现子类**DefaultHashOperations**

![DefaultHashOperations](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fs6gy5si52j30h1061whq.jpg)

```
public interface HashOperations<H, HK, HV> {

    /**
     * 从 key 指定的哈希集中移除指定的域
     * HDEL key field [field ...]
     *
     * @param key
     * @param hashKeys
     * @return
     */
    Long delete(H key, Object... hashKeys);

    /**
     * 判断指定的成员是否存在
     * HEXISTS key field
     * @param key
     * @param hashKey
     * @return
     */
    Boolean hasKey(H key, Object hashKey);

    /**
     * 获取指定key的信息
     * HGET key field
     * @param key
     * @param hashKey
     * @return
     */
    HV get(H key, Object hashKey);

    /**
     * 获取指定的一组key的信息
     * HMGET key field [field ...]
     * @param key
     * @param hashKeys
     * @return
     */
    List<HV> multiGet(H key, Collection<HK> hashKeys);

    /**
     * 增加key指定的哈希集中指定字段的数值
     * HINCRBY key field increment
     * @param key
     * @param hashKey
     * @param delta
     * @return
     */
    Long increment(H key, HK hashKey, long delta);

    /**
     * 增加key指定的哈希集中指定字段的数值（浮点数）
     * HINCRBYFLOAT key field increment
     * @param key
     * @param hashKey
     * @param delta
     * @return
     */
    Double increment(H key, HK hashKey, double delta);

    /**
     * 返回 key 指定的哈希集中所有字段的名字
     * HKEYS key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     */
    Set<HK> keys(H key);

    /**
     * 返回 key 指定的哈希集包含的字段的数量
     * HLEN key
     * @param key
     * @return
     */
    Long size(H key);

    /**
     * 设置 key 指定的哈希集中指定字段的值
     * HMSET key field value [field value ...]
     * @param key
     * @param m
     */
    void putAll(H key, Map<? extends HK, ? extends HV> m);

    /**
     * 设置key指定的哈希集中单个字段的值
     * HSET key field value
     * @param key
     * @param hashKey
     * @param value
     */
    void put(H key, HK hashKey, HV value);

    /**
     * 只在key指定的哈希集中不存在指定的字段时，设置字段的值
     * HSETNX key field value
     * @param key
     * @param hashKey
     * @param value
     * @return
     */
    Boolean putIfAbsent(H key, HK hashKey, HV value);

    /**
     * 返回key指定哈希集中所有的字段
     * HKEYS key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     */
    List<HV> values(H key);

    /**
     * 返回 key 指定的哈希集中所有的字段和值
     * HGETALL key
     * @param key
     * @return
     */
    Map<HK, HV> entries(H key);

    /**
     * 增量迭代输出
     * HSCAN key cursor [MATCH pattern] [COUNT count]
     * @param key
     * @param options
     * @return
     * @since
     */
    Cursor<Map.Entry<HK, HV>> scan(H key, ScanOptions options);

    /**
     * 获取redis指定的操作接口
     */
    RedisOperations<H, ?> getOperations();
}
```

> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。
