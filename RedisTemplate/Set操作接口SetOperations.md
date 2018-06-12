## Set操作接口SetOperations

RedisTemplate中Set操作接口SetOperations,默认实现子类DefaultSetOperations

![image](http://wx3.sinaimg.cn/mw690/0060lm7Tly1fs8ot0db65j30cd04ptag.jpg)

```
public interface SetOperations<K, V> {

    /**
     * 添加一个或多个元素到集合里
     * SADD key member [member ...]
     * @param key must not be {@literal null}.
     * @param values
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sadd">Redis Documentation: SADD</a>
     */
    @Nullable
    Long add(K key, V... values);

    /**
     * 从集合里删除一个或多个元素
     * SREM key member [member ...]
     * @param key must not be {@literal null}.
     * @param values
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/srem">Redis Documentation: SREM</a>
     */
    @Nullable
    Long remove(K key, Object... values);

    /**
     * 删除并获取一个集合里面的一个元素
     * SPOP key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/spop">Redis Documentation: SPOP</a>
     */
    @Nullable
    V pop(K key);

    /**
     * 删除并获取集合里面的count个元素
     * SPOP key [count]
     * @param key must not be {@literal null}.
     * @param count number of random members to pop from the set.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/spop">Redis Documentation: SPOP</a>
     * @since 2.0
     */
    @Nullable
    List<V> pop(K key, long count);

    /**
     * 移动集合里面的一个元素到另一集合
     * SMOVE source destination member
     * @param key must not be {@literal null}.
     * @param value
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/smove">Redis Documentation: SMOVE</a>
     */
    @Nullable
    Boolean move(K key, V value, K destKey);

    /**
     * 获取集合里面的元素数量
     * SCARD key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/scard">Redis Documentation: SCARD</a>
     */
    @Nullable
    Long size(K key);

    /**
     * 判断一个值是集合中的一个成员
     * SISMEMBER key member
     * @param key must not be {@literal null}.
     * @param o
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sismember">Redis Documentation: SISMEMBER</a>
     */
    @Nullable
    Boolean isMember(K key, Object o);

    /**
     * 获得两个集合的交集
     * SINTER key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sinter">Redis Documentation: SINTER</a>
     */
    @Nullable
    Set<V> intersect(K key, K otherKey);

    /**
     * 获得多个集合的交集
     * SINTER key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sinter">Redis Documentation: SINTER</a>
     */
    @Nullable
    Set<V> intersect(K key, Collection<K> otherKeys);

    /**
     * 获取两个集合的交集，并存储在另一个结果集中
     * SINTERSTORE destination key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sinterstore">Redis Documentation: SINTERSTORE</a>
     */
    @Nullable
    Long intersectAndStore(K key, K otherKey, K destKey);

    /**
     * 获取多个集合的交集，并存储在另一个结果集中
     * SINTERSTORE destination key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sinterstore">Redis Documentation: SINTERSTORE</a>
     */
    @Nullable
    Long intersectAndStore(K key, Collection<K> otherKeys, K destKey);

    /**
     * 返回给定集合中的并集的所有成员
     * SUNION key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sunion">Redis Documentation: SUNION</a>
     */
    @Nullable
    Set<V> union(K key, K otherKey);

    /**
     * 返回多个给定集合并集中的所有成员
     * SUNION key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sunion">Redis Documentation: SUNION</a>
     */
    @Nullable
    Set<V> union(K key, Collection<K> otherKeys);

    /**
     * 将两个集合并集中的所有成员存储在另一个集合中
     * SUNIONSTORE destination key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sunionstore">Redis Documentation: SUNIONSTORE</a>
     */
    @Nullable
    Long unionAndStore(K key, K otherKey, K destKey);

    /**
     * 将多个集合中的所有成员储在另一个集合中
     * SUNIONSTORE destination key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sunionstore">Redis Documentation: SUNIONSTORE</a>
     */
    @Nullable
    Long unionAndStore(K key, Collection<K> otherKeys, K destKey);

    /**
     * 返回一个集合与给定集合的差集的元素.
     * SDIFF key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sdiff">Redis Documentation: SDIFF</a>
     */
    @Nullable
    Set<V> difference(K key, K otherKey);

    /**
     * 返回一个集合与多个集合的差集的元素
     * SDIFF key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sdiff">Redis Documentation: SDIFF</a>
     */
    @Nullable
    Set<V> difference(K key, Collection<K> otherKeys);

    /**
     * 返回一个集合与另一个集合的差集的元素，并保存在另一个集合中
     * SDIFF key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sdiffstore">Redis Documentation: SDIFFSTORE</a>
     */
    @Nullable
    Long differenceAndStore(K key, K otherKey, K destKey);

    /**
     * 返回一个集合与多个集合的差集，并保存在集合中
     * SDIFF key [key ...]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/sdiffstore">Redis Documentation: SDIFFSTORE</a>
     */
    @Nullable
    Long differenceAndStore(K key, Collection<K> otherKeys, K destKey);

    /**
     * 获取集合中的所有元素
     * SMEMBERS key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/smembers">Redis Documentation: SMEMBERS</a>
     */
    @Nullable
    Set<V> members(K key);

    /**
     * 从集合中随机获取一个元素
     * SRANDMEMBER key [count]
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/srandmember">Redis Documentation: SRANDMEMBER</a>
     */
    V randomMember(K key);

    /**
     * 从集合中随机获取若干个元素，不能重复
     * SRANDMEMBER key [count]
     * @param key must not be {@literal null}.
     * @param count nr of members to return
     * @return empty {@link Set} if {@code key} does not exist.
     * @throws IllegalArgumentException if count is negative.
     * @see <a href="http://redis.io/commands/srandmember">Redis Documentation: SRANDMEMBER</a>
     */
    @Nullable
    Set<V> distinctRandomMembers(K key, long count);

    /**
     * 从集合中随机获取若干个元素，可以重复
     * SRANDMEMBER key [count]
     * @param key must not be {@literal null}.
     * @param count nr of members to return.
     * @return empty {@link List} if {@code key} does not exist or {@literal null} when used in pipeline / transaction.
     * @throws IllegalArgumentException if count is negative.
     * @see <a href="http://redis.io/commands/srandmember">Redis Documentation: SRANDMEMBER</a>
     */
    @Nullable
    List<V> randomMembers(K key, long count);

    /**
     * 迭代输出集合中的元素，防止资源泄露
     *
     * @param key
     * @param options
     * @return
     * @since 1.4
     */
    Cursor<V> scan(K key, ScanOptions options);

    /**
     * 获取Redis基本操作的接口
     * @return
     */
    RedisOperations<K, V> getOperations();
}
```


> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。

