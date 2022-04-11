# 环境

 ```shell
docker run -dp 127.0.0.1:6379:6379 \
-e TZ=Asia/Shanghai \
-v /home/data/redis/data:/data \
--name redis redis

docker exec -it redis redis-cli
 ```

# 单线程 + 多路IO复用

select poll epoll

# connection management

[commands](https://redis.io/commands/?group=connection)

```redis
auth [username] password
client ***
echo message
hello [protover [AUTH username password] [SETNAME clientname]]
ping [message]
quit
reset
select index
```

# server management

[commands](https://redis.io/commands/?group=server)

```redis
acl ***
bgrewriteaof
bgsave [SCHEDULE]
comman ***
config ***
dbsize
failover [TO host port [FORCE]] [ABORT] [TIMEOUT milliseconds]
flushall [ASYNC|SYNC]
flushdb [ASYNC|SYNC]
info [section]
lastsave
latency ***
lolwut [version version]
memory ***
module ***
monitor
psync replicationid offset
replconf ...options...
replicaof host port
restore-asking key arg arg ...options...
role
save
shutdown [NOSAVE|SAVE]
slowlog subcommand [argument]
swapdb index1 index2
sync
time
```

# generic

[generic](https://redis.io/commands/?group=generic)

```redis
copy source destination [DB destination-db] [REPLACE]
del key [key ...]
dump key
exists key [key ...]
expire key seconds
expireat key timestamp
keys pattern
migrate host port key| destination-db timeout [COPY] [REPLACE] [AUTH password] [AUTH2 username password] [KEYS key]
move key db
object subcommand [arguments [arguments ...]]
persist key
pexpire key milliseconds
pttl key
randomkey
rename key newkey
renamenx key newkey
restore key ttl serialized-value [REPLACE] [ABSTTL] [IDLETIME seconds] [FREQ frequency]
scan cursor [MATCH pattern] [COUNT count] [TYPE type]
sort key [BY pattern] [LIMIT offset count] [GET pattern [GET pattern ...]] [ASC|DESC] [ALPHA] [STORE destination]
touch key [key ...]
ttl key
type key
unlink key [key ...]
wait numreplicas timeout
```

# [Replication](https://redis.io/docs/manual/replication/)

主从复制，主机可读取可写，从机只能读取

- 读写分离
- 容灾快速恢复

```redis
replicaof host port
info replication
role
```

# [High availability with Redis Sentinel](https://redis.io/docs/manual/sentinel/)

高可用性，redis哨兵为不使用redis集群时提供高可用性

- Monitor
- Notification
- Automatic failover

[sentinel.conf](https://github.com/redis/redis/blob/unstable/sentinel.conf)

```shell
redis-sentinel sentinel.conf
```

[spring data redis sentinel](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#redis:sentinel)

```properties
spring.redis.sentinel.master=
spring.redis.sentinel.nodes=
spring.redis.sentinel.password=
```

```java
public class Configuration {
    /**
     * Jedis
     */
    @Bean
    public RedisConnectionFactory jedisConnectionFactory() {
        RedisSentinelConfiguration sentinelConfig = new RedisSentinelConfiguration()
                .master("mymaster")
                .sentinel("127.0.0.1", 26379)
                .sentinel("127.0.0.1", 26380);
        return new JedisConnectionFactory(sentinelConfig);
    }

    /**
     * Lettuce
     */
    @Bean
    public RedisConnectionFactory lettuceConnectionFactory() {
        RedisSentinelConfiguration sentinelConfig = new RedisSentinelConfiguration()
                .master("mymaster")
                .sentinel("127.0.0.1", 26379)
                .sentinel("127.0.0.1", 26380);
        return new LettuceConnectionFactory(sentinelConfig);
    }
}
```

# [Cluster](https://redis.io/docs/reference/cluster-spec/)

对redis实现水平扩容

[commands](https://redis.io/commands/?group=cluster)

```redis
asking
cluster ***
readonly
readwrite
```

```shell
redis-cli --cluster create --cluster-replicas ***
```

[spring data redis cluster](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#cluster)

```properties
spring.redis.cluster.nodes=
```

# [Persistence](https://redis.io/docs/manual/persistence/)

# [Transactions](https://redis.io/docs/manual/transactions/)

```redis
discard
exec
multi
unwatch
watch key [key ...]
```

# redis data types

## [Strings](https://redis.io/docs/manual/data-types/#strings)

String类型是二进制安全的，最大512M数据

[commands](https://redis.io/commands/?group=string)

```redis
append key value
decr key
get key
getdel key
getex key [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|PERSIST]
getrange key start end
getset key value
incr key
incrby key increment
incrbyfloat key increment
mget key [key ...]
mset key value [key value ...]
msetnx key value [key value ...]
psetex key milliseconds value
set key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] [GET]
setex key seconds value
setnx key value
setrange key offset value
strlen key
substr key arg arg
```

## [Lists](https://redis.io/docs/manual/data-types/#lists)

双向string链表，最多2^32 - 1个元素，查询头或尾、从头或尾插入元素为 O(1)

[commands](https://redis.io/commands/?group=list)

```redis
lindex key index
linsert key BEFORE|AFTER pivot element
llen key
lmove source destination LEFT|RIGHT LEFT|RIGHT
lpop key [count]
lpos key element [RANK rank] [COUNT num-matches] [MAXLEN len]
lpush key element [element ...]
lpushx key element [element ...]
lrange key start stop
lrem key count element
lset key index element
ltrim key start stop
rpop key [count]
rpoplpush source destination
rpush key element [element ...]
rpushx key element [element ...]
```

## [Sets](https://redis.io/docs/manual/data-types/#sets)

无序的string集合，底层是value为null的hash，最多2^32 - 1个元素，查询、插入、检查元素存在为 O(1)

[commands](https://redis.io/commands/?group=set)

```redis
sadd key member [member ...]
scard key
sdiff [key ...]
sdiffstore destination key [key ...]
sinter key [key ...]
sinterstore destination key [key ...]
sismember key member
smembers key
smismember key member [member ...]
smove source destination member
spop key [count]
srandmember key [count]
srem key member [member ...]
sscan key cursor [MATCH pattern] [COUNT count]
sunion key [key ...]
sunionstore key [key ...]
```

## [Hashes](https://redis.io/docs/manual/data-types/#hashes)

string类型的field-value映射表，最多2^32 - 1个映射关系

[commands](https://redis.io/commands/?group=hash)

```redis
hdel key field [field ...]
hexists key field
hget key field
hgetall key
hincrby key field increment
hincrbyfloat key field increment
hkeys key
hlen key
hmget key field [field ...]
hmset key field value [field value ...]
hrandfield key [count [WITHVALUES]]
hscan key cursor [MATCH pattern] [COUNT count]
hset key field value [field value ...]
hsetnx key field value
hstrlen key field
hvals key
```

## [Sorted Sets](https://redis.io/docs/manual/data-types/#sorted-sets)

每个元素都关联一个score，按score排序

[commands](https://redis.io/commands/?group=sorted-set)

```redis
zadd key [NX|XX] [GT|LT] [CH] [INCR] score member [score member ...]
zcard key
zcount key min max
zdiff numkeys key [key ...] [WITHSCORES]
zdiffstore destination numkeys key [key ...]
zincrby key increment member
zinter numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX] [WITHSCORES]
zinterstore destination numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX]
zlexcount key min max
zmscore key member [member ...]
zpopmax key [count]
zpopmin key [count]
zrandmember key [count [WITHSCORES]]
zrange key min max [BYSCORE|BYLEX] [REV] [LIMIT offset count] [WITHSCORES]
zrangebylex key min max [LIMIT offset count]
zrangebyscore key min max [WITHSCORES] [LIMIT offset count]
zrangestore dst src min max [BYSCORE|BYLEX] [REV] [LIMIT offset count]
zrank key member
zrem key member [member ...]
zremrangebylex key min max
zremrangebyrank key start stop
zremrangebyscore key min max
zrevrange key start stop [WITHSCORES]
zrevrangebylex key max min [LIMIT offset count]
zrevrangebyscore key max min [WITHSCORES] [LIMIT offset count]
zrevrank key member
zscan key cursor [MATCH pattern] [COUNT count]
zscore key member
zunion numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX] [WITHSCORES]
zunionstore destination numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX]
```

## [Bitmaps](https://redis.io/docs/manual/data-types/#bitmaps-and-hyperloglogs)

位图

## [HyperLogLogs](https://redis.io/docs/manual/data-types/data-types-tutorial/#hyperloglogs)

估计集合中的基数(不相同元素的个数)

## [Streams](https://redis.io/docs/manual/data-types/#streams)

## [Geospatial indexes](https://redis.io/docs/manual/data-types/#geospatial-indexes)