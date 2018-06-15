## Geo操作接口GeoOperations


RedisTemplate中ZSet操作接口GeoOperations,默认实现子类DefaultGeoOperations
![image](http://wx3.sinaimg.cn/mw690/0060lm7Tly1fsc9fqxytbj30cg05r40q.jpg)

```
public interface GeoOperations<K, M> {

    /**
     * 添加一个地理位置
     * GEOADD key longitude latitude member
     * @param key must not be {@literal null}.
     * @param point must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     */
    @Nullable
    Long add(K key, Point point, M member);

    /**
     * 添加一个地理位置
     * GEOADD key longitude latitude member
     * @param key must not be {@literal null}.
     * @param point must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     * @deprecated since 2.0, use {@link #add(Object, Point, Object)}.
     */
    @Deprecated
    @Nullable
    default Long geoAdd(K key, Point point, M member) {
        return add(key, point, member);
    }

    /**
     * 添加一个或多个地理位置
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param location must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     */
    @Nullable
    Long add(K key, RedisGeoCommands.GeoLocation<M> location);

    /**
     * 添加一个或多个地理位置
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param location must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     * @deprecated since 2.0, use {@link #add(Object, RedisGeoCommands.GeoLocation)}.
     */
    @Deprecated
    @Nullable
    default Long geoAdd(K key, RedisGeoCommands.GeoLocation<M> location) {
        return add(key, location);
    }

    /**
     * 添加一个或多个地理位置，地理位置信息以map形式存储
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param memberCoordinateMap must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     */
    @Nullable
    Long add(K key, Map<M, Point> memberCoordinateMap);

    /**
     * 添加一个或多个地理位置，地理位置信息以map形式存储
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param memberCoordinateMap must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     * @deprecated since 2.0, use {@link #add(Object, Map)}.
     */
    @Deprecated
    @Nullable
    default Long geoAdd(K key, Map<M, Point> memberCoordinateMap) {
        return add(key, memberCoordinateMap);
    }

    /**
     * 添加一个或多个地理位置
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param locations must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     */
    @Nullable
    Long add(K key, Iterable<RedisGeoCommands.GeoLocation<M>> locations);

    /**
     * 添加一个或多个地理位置
     * GEOADD key longitude latitude member [longitude latitude member ...]
     * @param key must not be {@literal null}.
     * @param locations must not be {@literal null}.
     * @return Number of elements added. {@literal null} when used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geoadd">Redis Documentation: GEOADD</a>
     * @deprecated since 2.0, use {@link #add(Object, Iterable)}.
     */
    @Deprecated
    @Nullable
    default Long geoAdd(K key, Iterable<RedisGeoCommands.GeoLocation<M>> locations) {
        return add(key, locations);
    }

    /**
     * 返回两个地理位置之间的距离，使用默认米
     * GEODIST key member1 member2
     * @param key must not be {@literal null}.
     * @param member1 must not be {@literal null}.
     * @param member2 must not be {@literal null}.
     * @return can be {@literal null}.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geodist">Redis Documentation: GEODIST</a>
     */
    @Nullable
    Distance distance(K key, M member1, M member2);

    /**
     * 返回两个地理位置之间的距离，使用默认米
     * GEODIST key member1 member2 [unit]
     * @param key must not be {@literal null}.
     * @param member1 must not be {@literal null}.
     * @param member2 must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/geodist">Redis Documentation: GEODIST</a>
     * @deprecated since 2.0, use {@link #distance(Object, Object, Object)}.
     */
    @Deprecated
    @Nullable
    default Distance geoDist(K key, M member1, M member2) {
        return distance(key, member1, member2);
    }

    /**
     * 返回两个地理位置之间的距离，可以自定义单位
     * GEODIST key member1 member2 [unit]
     * @param key must not be {@literal null}.
     * @param member1 must not be {@literal null}.
     * @param member2 must not be {@literal null}.
     * @param metric must not be {@literal null}.
     * @return can be {@literal null}.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geodist">Redis Documentation: GEODIST</a>
     */
    @Nullable
    Distance distance(K key, M member1, M member2, Metric metric);

    /**
     * 返回两个距离位置之间的距离，可以自定单位
     * GEODIST key member1 member2 [unit]
     * @param key must not be {@literal null}.
     * @param member1 must not be {@literal null}.
     * @param member2 must not be {@literal null}.
     * @param metric must not be {@literal null}.
     * @return can be {@literal null}.
     * @see <a href="http://redis.io/commands/geodist">Redis Documentation: GEODIST</a>
     * @deprecated since 2.0, use {@link #distance(Object, Object, Object, Metric)}.
     */
    @Deprecated
    @Nullable
    default Distance geoDist(K key, M member1, M member2, Metric metric) {
        return distance(key, member1, member2, metric);
    }

    /**
     * 返回一个或多个位置元素的 Geohash 表示。
     * GEOHASH key member [member ...]
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geohash">Redis Documentation: GEOHASH</a>
     */
    @Nullable
    List<String> hash(K key, M... members);

    /**
     * 返回一个或多个位置元素的 Geohash 表示。
     * GEOHASH key member [member ...]
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geohash">Redis Documentation: GEOHASH</a>
     * @deprecated since 2.0, use {@link #hash(Object, Object[])}.
     */
    @Deprecated
    @Nullable
    default List<String> geoHash(K key, M... members) {
        return hash(key, members);
    }

    /**
     * 返回地理空间的经纬度
     * GEOPOS key member [member ...]
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/geopos">Redis Documentation: GEOPOS</a>
     */
    @Nullable
    List<Point> position(K key, M... members);

    /**
     * 返回地理空间的经纬度
     * GEOPOS key member [member ...]
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/geopos">Redis Documentation: GEOPOS</a>
     * @deprecated since 2.0, use {@link #position(Object, Object[])}.
     */
    @Deprecated
    @Nullable
    default List<Point> geoPos(K key, M... members) {
        return position(key, members);
    }

    /**
     * 查询指定半径内的所有地理空间内元素的集合
     * GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param within must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/georadius">Redis Documentation: GEORADIUS</a>
     */
    @Nullable
    GeoResults<RedisGeoCommands.GeoLocation<M>> radius(K key, Circle within);

    /**
     * 查询指定半径内所有地理空间内元素的集合
     * GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param within must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/georadius">Redis Documentation: GEORADIUS</a>
     * @deprecated since 2.0, use {@link #radius(Object, Circle)}.
     */
    @Deprecated
    @Nullable
    default GeoResults<RedisGeoCommands.GeoLocation<M>> geoRadius(K key, Circle within) {
        return radius(key, within);
    }

    /**
     * 查询指定半径内所有地理空间内元素的集合
     * GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param within must not be {@literal null}.
     * @param args must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/georadius">Redis Documentation: GEORADIUS</a>
     */
    @Nullable
    GeoResults<RedisGeoCommands.GeoLocation<M>> radius(K key, Circle within, RedisGeoCommands.GeoRadiusCommandArgs args);

    /**
     * 查询指定半径内所有地理空间内元素的集合
     * GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param within must not be {@literal null}.
     * @param args must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/georadius">Redis Documentation: GEORADIUS</a>
     * @deprecated since 2.0, use {@link #radius(Object, Circle, RedisGeoCommands.GeoRadiusCommandArgs)}.
     */
    @Deprecated
    @Nullable
    default GeoResults<RedisGeoCommands.GeoLocation<M>> geoRadius(K key, Circle within, RedisGeoCommands.GeoRadiusCommandArgs args) {
        return radius(key, within, args);
    }

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param radius
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     */
    @Nullable
    GeoResults<RedisGeoCommands.GeoLocation<M>> radius(K key, M member, double radius);

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     *
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param radius
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     * @deprecated since 2.0, use {@link #radius(Object, Object, double)}.
     */
    @Deprecated
    @Nullable
    default GeoResults<RedisGeoCommands.GeoLocation<M>> geoRadiusByMember(K key, M member, double radius) {
        return radius(key, member, radius);
    }

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param distance must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     */
    @Nullable
    GeoResults<RedisGeoCommands.GeoLocation<M>> radius(K key, M member, Distance distance);

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     *
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param distance must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     * @deprecated since 2.0, use {@link #radius(Object, Object, Distance)}.
     */
    @Deprecated
    @Nullable
    default GeoResults<RedisGeoCommands.GeoLocation<M>> geoRadiusByMember(K key, M member, Distance distance) {
        return radius(key, member, distance);
    }

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     *
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param distance must not be {@literal null}.
     * @param args must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @since 2.0
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     */
    @Nullable
    GeoResults<RedisGeoCommands.GeoLocation<M>> radius(K key, M member, Distance distance, RedisGeoCommands.GeoRadiusCommandArgs args);

    /**
     * 查询指定半径内匹配到的最大距离一个地理空间元素
     * GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
     *
     * @param key must not be {@literal null}.
     * @param member must not be {@literal null}.
     * @param distance must not be {@literal null}.
     * @param args must not be {@literal null}.
     * @return never {@literal null} unless used in pipeline / transaction.
     * @see <a href="http://redis.io/commands/georadiusbymember">Redis Documentation: GEORADIUSBYMEMBER</a>
     * @deprecated since 2.0, use {@link #radius(Object, Object, Distance, RedisGeoCommands.GeoRadiusCommandArgs)}.
     */
    @Deprecated
    @Nullable
    default GeoResults<RedisGeoCommands.GeoLocation<M>> geoRadiusByMember(K key, M member, Distance distance, RedisGeoCommands.GeoRadiusCommandArgs args) {
        return radius(key, member, distance, args);
    }

    /**
     * 移除一个或多个空间位置
     *
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return Number of elements removed. {@literal null} when used in pipeline / transaction.
     * @since 2.0
     */
    @Nullable
    Long remove(K key, M... members);

    /**
     * 移除一个或多个空间位置
     *
     * @param key must not be {@literal null}.
     * @param members must not be {@literal null}.
     * @return Number of elements removed. {@literal null} when used in pipeline / transaction.
     * @deprecated since 2.0, use {@link #remove(Object, Object[])}.
     */
    @Deprecated
    @Nullable
    default Long geoRemove(K key, M... members) {
        return remove(key, members);
    }
}
```


> 以上内容部分命令和解释来自于[Redis中文官网](http://www.redis.cn/)，感谢该网站提供的命令支持。