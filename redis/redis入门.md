#     <center>**Redis详解** </center>
## 一、  Redis介绍
    Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、一个高性能的key-value数据库。并提供多种语言的API。说到Key-Value数据库NoSQL数据库可以想到MongoDB。
    和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。
    这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，Redis支持各种不同方式的排序。
    与memcached一样，为了保证效率，数据都是缓存在内存中。
    区别是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

## 二、 Redis的安装与配置
由于windows下安装比较的简单，我们这里着重介绍linux下安装，并且是在不能连接外网情况下安装。
>1. 安装redis首先要确保有对应的环境，本文环境为centos7,先gcc -v查看系统是否安装了gcc，没有的话需要在linux系统上离线安装gcc,这里介绍两种方式：1）、亲测可用，参考博文：https://blog.csdn.net/qq_28198181/article/details/82978830  2）、在linux终端中打开centos的光驱，进入centos这个目录
进入centos目录后
[......  centos]# 依次执行下列命令，必须都要执行，其实就是把gcc所有的依赖手动添加到操作系统中，这些rpm文件也可以从网上下载得到
rpm-ivh cpp-4.1.2-48.el5.i386.rpm
rpm-ivh kernel-headers-2.6.18-194.el5.i386.rpm
rpm-ivh glibc-headers-2.5-49.i386.rpm
rpm-ivh glibc-devel-2.5-24.i386.rpm
rpm-ivh libgomp-4.4.0-6.el5.i386.rpm
rpm-ivh gcc-4.1.2-48.el5.i386.rpm
执行完毕后gcc -v查看是否安装成功。
>2. 从官网下载redis安装包，我这里使用的是redis-5.0.3.tar.gz，然后放入到linux的/opt目录，可以自己新建文件夹放入，执行解压命令tar -zxvf redis-5.0.3.tar.gz，解压完成后出现redis-5.0.3文件夹，进入这个目录执行make命令，环境没有报错的话接着执行make install命令
>3. redis安装后默认的启动目录在/usr/local/bin，先执行redis-server 配置文件位置，再执行redis-cli -p 端口号(默认6379)打开redis客户端

## 三、 Redis的基本数据类型
1.数据类型

| 类型 | 特点 | 使用场景  |
| ------------ | ------------ | -------- |
| String       | 普通的k-v存储结构      |     普适     |
| List        | 双向链表       | 轻量级消息队列   |
| Hash       | 类似于HashMap         | 存储用户信息,k为id,v为具体信息 |
| Set        | 无序，数据不重复          |   获取共同好友  |
| ZSet       | 有序且不重复      |  时消息队列,带权重的队列  |  
| HyperLogLog           |                  |                         |
| Geo	           |                  |                         |
| Pub/Sub           |                  |                         |

2.共有操作命令

|    命令      |         说明                |          例子         |
|------------- |----------------------------|-----------------------|
|     keys     |     取出所有Key             |           keys *             |
|     keys     |     支持正则表达式           |     keys "prefix*"           |
|     type     |     查看数据类型             |     type key                 |
|     rename   |     重命名key               |   rename key newname          |
|     append   |     追加value               |    append key value           |
|     exists   |     判断key是否存在          |    exists key                |
|     expire   |     设置key的过期时间(秒)     |   expire key seconds         |
|     pexpire  |     设置key的国企时间（毫秒）  |   pexpire keu milliseconds   |
|     expireat |     以 UNIX 时间戳(unix timestamp)格式设置 key 的过期时间（秒） |    EXPIREAT key 1293840000 |
|     pwxpireat|     以 UNIX 时间戳(unix timestamp)格式设置 key 的过期时间（毫秒）|  pwxpireat key 1293840000000 |
|     persist  |     取消失效             |      persist  key    |
|     dbsize   |     数据库键值的数量      |      dbsize          |
|     flushdb  |     清空单个数据库           |       flushdb         |
|     flushall |     清空所有的数据库         |       flushall         |
|     info     |     查看数据库服务信息       |       info            |     

3.string数据类型,最简单的k-v类型，k或v都可以为字符串或者数字

|    命令       |           说明                 |         例子               |
|--------------|------------------------------|------------------------------|
|    set       |   插入键值, 存在则覆盖         |    set key value            |
|    get       |   查看value                   |    get key                  |
|    setnx     |   key存在则什么都不做,不存在与set相同 |  setnx k v           |
|    mset      |   批量插入键值, 存在则覆盖    |  mset k1 v1 k2 v2  Kn Vn     |
|    mget      |   批量查看键值               |   mget k1 k2 Kn             |
|   msetnx     | mset和setnx的组合效果,如果任意key存在,则全部都不设值| msetnx k1 v1 k2 v2|
|   getset     | 取值并设置新值,和set的不同在于返回旧值 |  getset k  v  |
|   setex      | 在set基础上设置过期时间,使用ttl查看剩余时间 | setex key seconds value |
|   psetex     | 同setex,时间单位是毫秒,使用pttl查看剩余时间 | psetex key milliseconds value |
|   setrange   | 从offset处开始用value替换,offset大于总长度则用0补齐 ,不存在则新加 | setrange k offset value |
|  getrange    | 获取指定区间内的内容,下标可以为负数，-1表示结尾 |  getrange key start end  |
|    del       |  删除键值对     |  del k       |
|    strlen    |   获取value长度      |  strlen k    |
|    append    | 给key的字符串追加value,返回新的字符串长度,不存在则新加 | append k v   |
|    incr      | 递增,key不存在则value置为1,value为非数组类型则报错 | incr k   |
|    decr      | 递减             |  decr k |
|    incrby    | 能指定步长的递增  | incrby k n  |
|    decrby    | 能指定步长的递减  |  decrby k n |
|   incrbyfloat| 指定步长的递增，n可以为浮点数     |  ncrbyfloat k n |  
|   getbit     |字符串类型是以二进制形式存储,bit操作就是对这个二进制进行的操作| getbit key offset |
|   setbit     |字符串类型是以二进制形式存储,bit操作就是对这个二进制进行的操作| setbit key offset |
|   bitcount   |统计二进制存储中1的个数 | bitcount key |
|   bitop      |对二进制进行与或非，异或运算 | bitop [and/or/not/xor] destkey key1 [key2] [keyn]   

4.list数据类型，基于双向循环链表实现，栈元素可以重复

|   命令       |          说明               |     例子                      |
|--------------|----------------------------|-------------------------------|
|  *push       |       入栈               |  向列表左侧加元素：lpush key value...valueN <br>  向列表右侧加元素：rpush key value...valueN |
|  *pop        |       出栈               |  列表左侧移除第一个元素：lpop key  <br> 列表右侧移除第一个元素：rpop key |
|  b*pop       |     阻塞式弹出     | 列表左侧移除第一个元素：blpop key  <br> 列表右侧移除第一个元素：brpop key |
|  lrange      | 查询指定key的value区间元素 | lrange key start stop |
|  lindex      | 获取指定下标位置的元素  |  lindex key index   |
|  llen        | 元素的个数(获得list的长度)   |  llen key  |
|  lrem        | 从list中删除count个value,返回删除的个数,count:0全删除,<0从后开始删 | lrem key count value |
|  lset        | 设置指定位置的值  | lset key index value |
|  ltrim       | 截取指定范围内元素,其余的删除,返回ok成功 | ltrim key start end |
|  rpoplpush   | 从第1个list右边弹出后,从左边放入新的list） | rpoplpush listkey newlistkey |  

3.hash数据类型，类似于java的hashamp，是一个string类型的field适合用于存储对象

|   命令           |              说明           |              例子            |
|------------------|----------------------------|------------------------------|
|   hset          | 给一个字段赋值             | hset key field value      |
|   hget          | 查看字段的值   |   hget key field |
|   hmset         | 批量赋值       |   hmset key field value field1 value1  |
|   hmget         | 批量查看       | hmget key field field1 field2 |
|   hsetnx        | 存在则不作操作  | hsetnx key field value       |
|   hlen          | 获得field个数   | hlen key             |
|   hdel          | 删除键值对      |  hdel key field field1 field2  |
|   hgetall       | 取所有的字段名和值(key-value) | hgetall key             |
|   hincrby       | 增加field的value  | hincrby key field increment  |
|   hexists       | 判断字段是否存在   | hexists key field            |
|   hkeys         | 获得key的所有字段  | hkeys key                 |
|   hvals         | 获得key的所有value | hvals key                 |

4.set数据结构，无序, 不可重复,支持并集,交集和差集运算. 底层基于hashtable实现

|   命令           |          说明                 |     例子                   |
|------------------|-------------------------------|---------------------------|
|  sadd            |   增加集合元素                |   sadd key sadd key value1 [value2 ...] |
|  srem            |   删除集合元素                |  srem key value1 [value2...]   |
|  smembers        |   遍历集合元素                |  smembers key          |
|  sismember       |   判断值是否存在              |  sismember key value    |
|  scard           |   集合元素个数                | scard key              |
|  srandmember     |   随机返回count个value        | srandmember key count    |
|  spop            |   和srandmember相似,但会删除返回的 | spop key count       |
|  smove           |   跨集合移动元素               |  smove key1 key2 value   |
|  sunion          |   并集                       |  sunion key1  key2        |
|  sinter          |   交集                       |  sinter key1  key2   |
|  sdiff           |   差集                       |  sdiff key1 key2       |
|  sunionstore     |   保存并集到destkey          |  sunionstore destkey key1 keys2 |
|  sinterstore     |   保存交集到destkey          |  sinterstore destkey key1 keys2 |
|  sdiffstore      |   保存差集到destkey          |   sdiffstore destkey key1 key2  |

5.zset数据类型，有序set,相比于set增加了权重参数score,也是实现有序的关键,默认以score升序.zset的每个元素都会关联一个double类型的分数。

|   命令           |    说明                       |     例子                           |
|------------------|--------------------------------|----------------------------------|
|   zadd           |    集合添加元素              |  zadd key score member |
|   zrange    | 查看指定范围内元素 | zrange key m n withscores |
|zrangebyscore |	按照scores顺序输出元素 |	zrangebyscore key m n withscores|
|zrevrange |	类似于zrange,但是逆序 |	zrevrange key m n |
|zrem |	删除集合中某元素 |	zrem key member |
|zremrangebyrank |	删除指定区间内所有元素(索引顺序) |	zremrangebyrank key m n|
|zremrangebyscore |	删除指定区间内所有元素(权重顺序) |	zremrangebyscore key m n|
|zincrby |	修改元素的score,若元素不存在则添加 |	zincrby key score member|
|zrank |	查看元素索引 |	zrank key member |
|zrevrank |	查看逆序索引 |	zrevrank key member|
|zcount |	指定区间内元素的数量 |	zcount key m n|
|zcard |	元素的个数 |	zcard key|
|zscore |	查看元素的权重 |	zscore key member|

6.HyperLogLog是用来做基数统计的算法，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的，并且很小.可应用于分析每天访问站点的IP, 粗略估算访问数量等场景

|  命令       |        说明            |            例子            |
|-------------|-----------------------|----------------------------|
|  pfadd      |  Pfadd 命令将所有元素参数添加到 HyperLogLog 数据结构中 | PFADD key element [element ...] |
|  Pfcount    |  Pfcount 命令返回给定 HyperLogLog 的基数估算值 | PFCOUNT key [key ...] |
|  pfmerge    |   将多个 HyperLogLog 合并为一个  |  pfmerge destkey sourcekey [sourcekey ...] |

7.Geo数据类型很适合应用于移动端基于位置的服务,如外卖应用中, 用户同时有家和公司两个送餐地址, 应用会自动根据当前位置确定用户想要送达的地址.

```properties
# geoadd key longitude latitude member [longitude latitude member ...]:将指定的地理空间位置(纬度、经度、名称)添加到指定的key中
geoadd user 116.111 39.111 home
geoadd user 126.111 39.111 office
# geodist key member1 member2 [unit]:计算2点间距离
geodist user home office m
# geopos key member [member ...]:返回经纬度
geopos user home
# geohash key member [member ...]:返回Geohash 表示
geohash user home
# georadius key longitude latitude radius m|km|ft|mi:
georadius user 121.111 39.111 1000 km withcoord
# 同georadius
# georadiusbymember key member radius m|km|ft|mi
georadiusbymember user home 100 km
```
8.Pub/Sub(发布/订阅消息通信模式),在消费者下线的情况下,生产的消息会丢失,得使用专业的消息队列如rabbitmq等.

| 命令 | 	说明	 | 例 |
|-------|-----------|------------|
| subscribe | 订阅channel | 	subscribe foo |
| unsubscribe | 退订channel | 	unsubscribe [channel [channel ...]] |
| publish | 发布消息到channel | 	publish foo "foobar" |
| psubscribe | 支持通配符的订阅 | 	psubscribe pattern [pattern ...] |
| punsubscribe | 支持通配符的退订 | 	punsubscribe [pattern [pattern ...]]  | |
| pubsub | 查看订阅与发布状态 | 	pubsub subcommand [argument [argument ...]] |
