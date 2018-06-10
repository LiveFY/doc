## 字符串操作接口ValueOperations

RedisTemplate中字符串操作的接口**ValueOperations**,默认实现子类**DefaultValueOperations**

![DefaultValueOperations类](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fs6fcsgudij30ew05s0vd.jpg)



```
public interface ValueOperations<K, V> {
    /**
     * 设置数据
     * set key value
     * @param key
     * @param value
     */
    void set(K key, V value);

    /**
     * 设置数据，存活时间控制
     * setex key time value
     * @param key
     * @param value
     * @param timeout
     * @param unit
     */
    void set(K key, V value, long timeout, TimeUnit unit);

    /**
     * 如果key不存在，则设置（不覆盖设置）
     * setnx key value
     * @param key
     * @param value
     * @return
     */
    Boolean setIfAbsent(K key, V value);

    /**
     * 设置多组的数据信息
     * mset key value key value ...
     * @param map
     */
    void multiSet(Map<? extends K, ? extends V> map);


    /**
     * 设置多组的数据信息（不覆盖设置，如果有一个key是重复的，则内容都不会设置）
     * mset key value key value ...
     * @param map
     * @return
     */
    Boolean multiSetIfAbsent(Map<? extends K, ? extends V> map);


    /**
     * 获取数据
     * get key
     * @param key
     * @return
     */
    V get(Object key);


    /**
     * 给指定的key设置一个新值，返回原来的vaLue
     * getset key value
     * @param key
     * @param value
     * @return
     */
    V getAndSet(K key, V value);


    /**
     *返回所有指定的key的value
     * mget key key ...
     * @param keys
     * @return
     */
    List<V> multiGet(Collection<K> keys);


    /**
     *对储存在key中的value进行指定的加法操作
     * incrby key 指定的步长
     * @param key
     * @param delta
     * @return
     */
    Long increment(K key, long delta);


    /**
     * 对储存在key中的value进行指定的加法操作（增加一个浮点数）
     *  INCRBYFLOAT key 浮点数步长
     * @param key
     * @param delta
     * @return
     */
    Double increment(K key, double delta);


    /**
     * 给指定key追加内容
     * append key value
     * @param key
     * @param value
     * @return
     */
    Integer append(K key, String value);


    /**
     * 获取指定范围的内容
     * GETRANGE key start end
     * @param key
     * @param start
     * @param end
     * @return
     */
    String get(K key, long start, long end);


    /**
     *覆盖key对应的string的一部分，从指定的offset处开始，覆盖value的长度
     * SETRANGE key offset value
     * @param key
     * @param value
     * @param offset
     */
    void set(K key, V value, long offset);


    /**
     * 得到指定key内容的长度
     * STRLEN key
     * @param key
     * @return
     */
    Long size(K key);


    /**
     *设置或者清空key的value(字符串)在offset处的bit值.
     *  SETBIT key offset value
     *  value:0 则清空  value:1则设置
     * @param key
     * @param offset
     * @param value
     * @return
     */
    Boolean setBit(K key, long offset, boolean value);

    /**
     *返回key对应的string在offset处的bit值
     * GETBIT key offset
     * @param key
     * @param offset
     * @return
     */
    Boolean getBit(K key, long offset);


    /**
     * 获取指定redis操作的接口
     * @return
     */
    RedisOperations<K, V> getOperations();

}
```

> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。