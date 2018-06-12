## List操作接口ListOperations

RedisTemplate中List操作接口ListOperations,默认实现子类DefaultListOperations

![image](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fs7i8f5ttnj30dd05476b.jpg)



```
public interface ListOperations<K, V> {

    /**
     * 从列表中获得指定元素
     * LRANGE key start stop
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lrange">Redis Documentation: LRANGE</a>
     */
    @Nullable
    List<V> range(K key, long start, long end);

    /**
     * 取出指定范围内的元素
     * LTRIM key start stop
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @see <a href="http://redis.io/commands/ltrim">Redis Documentation: LTRIM</a>
     */
    void trim(K key, long start, long end);

    /**
     * 获得List队列的长度
     * LLEN key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/llen">Redis Documentation: LLEN</a>
     */
    @Nullable
    Long size(K key);

    /**
     * 将指定的值从坐标插入
     * LPUSH key value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lpush">Redis Documentation: LPUSH</a>
     */
    @Nullable
    Long leftPush(K key, V value);

    /**
     * 将指定的值从左边插入
     * LPUSH key value [value ...]
     * @param key must not be {@literal null}.
     * @param values
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lpush">Redis Documentation: LPUSH</a>
     */
    @Nullable
    Long leftPushAll(K key, V... values);

    /**
     * 将指定的值从左边插入
     * LPUSH key value [value ...]
     * @param key must not be {@literal null}.
     * @param values must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @since 1.5
     * @see <a href="http://redis.io/commands/lpush">Redis Documentation: LPUSH</a>
     */
    @Nullable
    Long leftPushAll(K key, Collection<V> values);

    /**
     * 当key存在时从左边插入一个值
     * LPUSHX key value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lpushx">Redis Documentation: LPUSHX</a>
     */
    @Nullable
    Long leftPushIfPresent(K key, V value);

    /**
     * 在指定的内容的左边加上内容
     * LPUSH key pivot value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lpush">Redis Documentation: LPUSH</a>
     */
    @Nullable
    Long leftPush(K key, V pivot, V value);

    /**
     * 从右边加入数据
     * RPUSH key value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/rpush">Redis Documentation: RPUSH</a>
     */
    @Nullable
    Long rightPush(K key, V value);

    /**
     * 从右边加入数据
     * RPUSH key value [value ...]
     * @param key must not be {@literal null}.
     * @param values
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/rpush">Redis Documentation: RPUSH</a>
     */
    @Nullable
    Long rightPushAll(K key, V... values);

    /**
     * 从右边加入数据
     * RPUSH key value [value ...]
     * @param key must not be {@literal null}.
     * @param values
     * @return {@literal null} when used in pipeline / transaction.
     * @since 1.5
     * @see <a href="http://redis.io/commands/rpush">Redis Documentation: RPUSH</a>
     */
    @Nullable
    Long rightPushAll(K key, Collection<V> values);

    /**
     * 在list的key存在时从右边加入数据
     * RPUSHX key value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/rpushx">Redis Documentation: RPUSHX</a>
     */
    @Nullable
    Long rightPushIfPresent(K key, V value);

    /**
     * 在list的key的指定value的右边加入数据 .
     * RPUSH key pivot value
     * @param key must not be {@literal null}.
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lpush">Redis Documentation: RPUSH</a>
     */
    @Nullable
    Long rightPush(K key, V pivot, V value);

    /**
     * 给指定位置加入一个元素
     * LSET key index value
     * @param key must not be {@literal null}.
     * @param index
     * @param value
     * @see <a href="http://redis.io/commands/lset">Redis Documentation: LSET</a>
     */
    void set(K key, long index, V value);

    /**
     * 从列表中删除出现count次的value的元素
     * LREM key count value
     * @param key must not be {@literal null}.
     * @param count
     * @param value
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lrem">Redis Documentation: LREM</a>
     */
    @Nullable
    Long remove(K key, long count, Object value);

    /**
     * 通过索引获取一个元素
     * LINDEX key index
     * @param key must not be {@literal null}.
     * @param index
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/lindex">Redis Documentation: LINDEX</a>
     */
    @Nullable
    V index(K key, long index);

    /**
     * 移除并返回key对象的左边第一个元素
     * LPOP key
     * @param key must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/lpop">Redis Documentation: LPOP</a>
     */
    @Nullable
    V leftPop(K key);

    /**
     *  删除并获得该列表中的第一个元素，或阻塞直到第一个可用
     * BLPOP key [key ...] timeout
     * @param key must not be {@literal null}.
     * @param timeout
     * @param unit must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/blpop">Redis Documentation: BLPOP</a>
     */
    @Nullable
    V leftPop(K key, long timeout, TimeUnit unit);

    /**
     * 从右边移除第一个元素
     * RPOP key
     * @param key must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/rpop">Redis Documentation: RPOP</a>
     */
    @Nullable
    V rightPop(K key);

    /**
     * 删除并获得最后一个元素，或阻塞直到有一个可用
     * BRPOP key [key ...] timeout
     * @param key must not be {@literal null}.
     * @param timeout
     * @param unit must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/brpop">Redis Documentation: BRPOP</a>
     */
    @Nullable
    V rightPop(K key, long timeout, TimeUnit unit);

    /**
     * 删除列表的最后一个元素，将其追加到另一个列表
     * RPOPLPUSH source destination
     * @param sourceKey must not be {@literal null}.
     * @param destinationKey must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/rpoplpush">Redis Documentation: RPOPLPUSH</a>
     */
    @Nullable
    V rightPopAndLeftPush(K sourceKey, K destinationKey);

    /**
     * 弹出列表的最后一个元素，将其追加到另一个列表  或者阻塞直到一个可用的出现将其追加到另一个列表
     * <b>Blocks connection</b> until element available or {@code timeout} reached.
     *
     * @param sourceKey must not be {@literal null}.
     * @param destinationKey must not be {@literal null}.
     * @param timeout
     * @param unit must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/brpoplpush">Redis Documentation: BRPOPLPUSH</a>
     */
    @Nullable
    V rightPopAndLeftPush(K sourceKey, K destinationKey, long timeout, TimeUnit unit);

    /**
     * 获取指定redis操作的接口
     * @return
     */
    RedisOperations<K, V> getOperations();
}
```


> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。
