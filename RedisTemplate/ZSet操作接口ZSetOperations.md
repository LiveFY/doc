## ZSet操作接口ZSetOperations

RedisTemplate中ZSet操作接口ZSetOperations,默认实现子类
![image](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fs8q9k1w3lj30d5055q4y.jpg)


```
public interface ZSetOperations<K, V> {

    /**
     * Typed ZSet tuple.
     */
    interface TypedTuple<V> extends Comparable<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> {

        @Nullable
        V getValue();

        @Nullable
        Double getScore();
    }

    /**
     * 添加一个数据并进行打分
     * ZADD key [NX|XX] [CH] [INCR] score member [score member ...]
     * @param key must not be {@literal null}.
     * @param score the score.
     * @param value the value.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zadd">Redis Documentation: ZADD</a>
     */
    @Nullable
    Boolean add(K key, V value, double score);

    /**
     * 以Set集合的形式添加一个数据，如果它已存在，则更新它的分数
     * ZADD key [NX|XX] [CH] [INCR] score member [score member ...]
     * @param key must not be {@literal null}.
     * @param tuples must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zadd">Redis Documentation: ZADD</a>
     */
    @Nullable
    Long add(K key, Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> tuples);

    /**
     * 从排序的集合中删除一个或多个成员
     * ZREM key member [member ...]
     * @param key must not be {@literal null}.
     * @param values must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrem">Redis Documentation: ZREM</a>
     */
    @Nullable
    Long remove(K key, Object... values);

    /**
     * 为某一个成员加分
     * ZINCRBY key increment member
     * @param key must not be {@literal null}.
     * @param delta
     * @param value the value.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zincrby">Redis Documentation: ZINCRBY</a>
     */
    @Nullable
    Double incrementScore(K key, V value, double delta);

    /**
     * 返回成员在有序集中的索引
     * ZRANK key member
     * @param key must not be {@literal null}.
     * @param o the value.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrank">Redis Documentation: ZRANK</a>
     */
    @Nullable
    Long rank(K key, Object o);

    /**
     * 返回有序集key中成员member的排名，其中有序集成员按score值从大到小排列
     *  ZREVRANK key member
     * @param key must not be {@literal null}.
     * @param o the value.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrank">Redis Documentation: ZREVRANK</a>
     */
    @Nullable
    Long reverseRank(K key, Object o);

    /**
     * 获取指定索引内的数据
     * ZRANGE key start stop [WITHSCORES]
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrange">Redis Documentation: ZRANGE</a>
     */
    @Nullable
    Set<V> range(K key, long start, long end);

    /**
     * 获取指定索引内的数据
     * ZRANGE key start stop [WITHSCORES]
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrange">Redis Documentation: ZRANGE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> rangeWithScores(K key, long start, long end);

    /**
     * 返回指定分数区间内的成员，分数由低到高排序
     * ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrangebyscore">Redis Documentation: ZRANGEBYSCORE</a>
     */
    @Nullable
    Set<V> rangeByScore(K key, double min, double max);

    /**
     * 返回指定分数区间内的成员，分数由低到高排序
     * ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrangebyscore">Redis Documentation: ZRANGEBYSCORE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> rangeByScoreWithScores(K key, double min, double max);

    /**
     * 返回指定分数区间内的成员，同时设置一个起始点，以及个数
     * ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @param offset
     * @param count
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrangebyscore">Redis Documentation: ZRANGEBYSCORE</a>
     */
    @Nullable
    Set<V> rangeByScore(K key, double min, double max, long offset, long count);

    /**
     * 返回指定分数区间内的成员，同时设置一个起始点，以及个数
     * ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
     * @param key
     * @param min
     * @param max
     * @param offset
     * @param count
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrangebyscore">Redis Documentation: ZRANGEBYSCORE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> rangeByScoreWithScores(K key, double min, double max, long offset, long count);

    /**
     * 返回指定范围内的成员，通过索引，从高到低
     * ZREVRANGE key start stop [WITHSCORES]
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrange">Redis Documentation: ZREVRANGE</a>
     */
    @Nullable
    Set<V> reverseRange(K key, long start, long end);

    /**
     * 返回指定范围内的成员，通过索引，从高到低
     * ZREVRANGE key start stop [WITHSCORES]
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrange">Redis Documentation: ZREVRANGE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> reverseRangeWithScores(K key, long start, long end);

    /**
     * 返回指定分数范围内的成员，分数由高到低排序。
     * ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrange">Redis Documentation: ZREVRANGE</a>
     */
    @Nullable
    Set<V> reverseRangeByScore(K key, double min, double max);

    /**
     * 返回指定分数范围内的成员，分数由高到低排序。
     * ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrangebyscore">Redis Documentation: ZREVRANGEBYSCORE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> reverseRangeByScoreWithScores(K key, double min, double max);

    /**
     * 返回指定分数范围内的成员，并设置一个开始点，以及要返回的个数
     * ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @param offset
     * @param count
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrangebyscore">Redis Documentation: ZREVRANGEBYSCORE</a>
     */
    @Nullable
    Set<V> reverseRangeByScore(K key, double min, double max, long offset, long count);

    /**
     * 返回指定分数范围内的成员，并设置一个开始点，以及要返回的个数
     * ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
     *
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @param offset
     * @param count
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zrevrangebyscore">Redis Documentation: ZREVRANGEBYSCORE</a>
     */
    @Nullable
    Set<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> reverseRangeByScoreWithScores(K key, double min, double max, long offset, long count);

    /**
     * 返回分数范围内的成员数量
     * ZCOUNT key min max
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zcount">Redis Documentation: ZCOUNT</a>
     */
    @Nullable
    Long count(K key, double min, double max);

    /**
     * 获取一个排序的集合中的成员数量
     * ZCARD key
     * @see #zCard(Object)
     * @param key
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zcard">Redis Documentation: ZCARD</a>
     */
    @Nullable
    Long size(K key);

    /**
     * 返回一个排序集合中的成员数量
     * ZCARD key
     * @param key must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @since 1.3
     * @see <a href="http://redis.io/commands/zcard">Redis Documentation: ZCARD</a>
     */
    @Nullable
    Long zCard(K key);

    /**
     * 获取成员在排序设置中相关的比分
     * ZSCORE key member
     * @param key must not be {@literal null}.
     * @param o the value.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zscore">Redis Documentation: ZSCORE</a>
     */
    @Nullable
    Double score(K key, Object o);

    /**
     * 移除在指定区间内的成员
     * ZREMRANGEBYRANK key start stop
     * @param key must not be {@literal null}.
     * @param start
     * @param end
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zremrangebyrank">Redis Documentation: ZREMRANGEBYRANK</a>
     */
    @Nullable
    Long removeRange(K key, long start, long end);

    /**
     * 移除在指定分数内的成员
     * ZREMRANGEBYSCORE key min max
     * @param key must not be {@literal null}.
     * @param min
     * @param max
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zremrangebyscore">Redis Documentation: ZREMRANGEBYSCORE</a>
     */
    @Nullable
    Long removeRangeByScore(K key, double min, double max);

    /**
     * 获取两个集合的并集将其放到另一个集合中
     * ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight] [SUM|MIN|MAX]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zunionstore">Redis Documentation: ZUNIONSTORE</a>
     */
    @Nullable
    Long unionAndStore(K key, K otherKey, K destKey);

    /**
     * 获取多个集合的并集将其放到另一个集合中
     * ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight] [SUM|MIN|MAX]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zunionstore">Redis Documentation: ZUNIONSTORE</a>
     */
    @Nullable
    Long unionAndStore(K key, Collection<K> otherKeys, K destKey);

    /**
     * 获取两个集合的交集并将其放到另一个集合中
     * ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight] [SUM|MIN|MAX]
     * @param key must not be {@literal null}.
     * @param otherKey must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zinterstore">Redis Documentation: ZINTERSTORE</a>
     */
    @Nullable
    Long intersectAndStore(K key, K otherKey, K destKey);

    /**
     * 获取多个集合的交集并将其放到另一个集合中
     * ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight] [SUM|MIN|MAX]
     * @param key must not be {@literal null}.
     * @param otherKeys must not be {@literal null}.
     * @param destKey must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zinterstore">Redis Documentation: ZINTERSTORE</a>
     */
    @Nullable
    Long intersectAndStore(K key, Collection<K> otherKeys, K destKey);

    /**
     * 迭代输出其中的元素，并在完成后关闭，以避免资源泄露
     * ZSCAN key cursor [MATCH pattern] [COUNT count]
     * @param key
     * @param options
     * @return {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/zscan">Redis Documentation: ZSCAN</a>
     * @since 1.4
     */
    Cursor<org.springframework.data.redis.core.ZSetOperations.TypedTuple<V>> scan(K key, ScanOptions options);

    /**
     * 返回指定成员区间的成员，按字典正序排列，分数必须相同
     * ZRANGEBYLEX key min max [LIMIT offset count]
     * @param key must not be {@literal null}.
     * @param range must not be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @since 1.7
     * @see <a href="http://redis.io/commands/zrangebylex">Redis Documentation: ZRANGEBYLEX</a>
     */
    @Nullable
    Set<V> rangeByLex(K key, RedisZSetCommands.Range range);

    /**
     * 返回指定成员区间的成员，按字典正序排列，分数必须相同
     * ZRANGEBYLEX key min max [LIMIT offset count]
     * @param key must not be {@literal null}
     * @param range must not be {@literal null}.
     * @param limit can be {@literal null}.
     * @return {@literal null} when used in pipeline / transaction.
     * @since 1.7
     * @see <a href="http://redis.io/commands/zrangebylex">Redis Documentation: ZRANGEBYLEX</a>
     */
    @Nullable
    Set<V> rangeByLex(K key, RedisZSetCommands.Range range, RedisZSetCommands.Limit limit);

    /**
     * 获取redis操作接口
     */
    RedisOperations<K, V> getOperations();
}
```


> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。