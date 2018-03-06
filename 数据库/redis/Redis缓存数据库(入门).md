# Redis缓存数据库(入门)

### Redis简介

Redis 是完全开源免费的，遵守BSD协议，是一个高性能的key-value数据库。

​Redis 与其他 key - value 缓存产品有以下三个特点：

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。


- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。


### Redis优势

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
- 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
- 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

```
	原子操作：假设有两个操作A和B，如果从执行A的线程来看，当另一个线程执行B时，要么将B全部执行完，要么完全不执行B，那么A和B对彼此来说就是原子的。
	原子操作是指，对于访问同一个状态的所有操作（包括该操作本身来说），这个操作是一个以原子方式执行的操作。
```

### Redis与其他key-value存储有什么不同？

- Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
- Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，因为数据量不能大于硬件内存。在内存数据库方面的另一个优点是，相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。



### Redis安装

#### Window下安装

下载地址https://github.com/MSOpenTech/redis/releases

下载安装包之后解压。打开cmd窗口，切换到redis目录下

```
G:\redis>redis-server.exe redis.windows.conf
```

这个时候另启动一个cmd窗口，原来的不要关闭，不然无法访问服务端。

```
G:\redis>redis-cli.exe -h 127.0.0.1 -p 6379
```

然后执行

```
127.0.0.1:6379> ping
```

如果返回的是

```
127.0.0.1:6379> ping
PONG
```

这样就表示连接成功

#### Linux下安装

下载地址：[http://redis.io/download](http://redis.io/download)，下载最新文档版本。

```shell
$ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
$ tar xzf redis-2.8.17.tar.gz
$ cd redis-2.8.17
$ make
```

​	make完后 redis-2.8.17目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下：

下面启动redis服务.

```
$ cd src
$ ./redis-server
```

​	注意这种方式启动redis 使用的是默认配置。也可以通过启动参数告诉redis使用指定配置文件使用下面命令启动。

```shell
$ cd src
$ ./redis-server redis.conf
```

启动redis服务进程后，就可以使用测试客户端程序redis-cli和redis服务交互了。 比如：

```shell
$ cd src
$ ./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```



### Redis数据类型

#### String(字符串)

​	string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象。string类型是Redis最基本的数据类型，一个键最大能存储512MB。

```shell
redis 127.0.0.1:6379> SET name "yyw"
OK
redis 127.0.0.1:6379> GET name
"yyw"	
```

#### Hash(哈希)

​	Redis hash 是一个键值(key=>value)对集合。Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。每个 hash 可以存储 2^32 -1 键值对（40多亿）。

```
redis> HMSET myhash field1 "Hello" field2 "World"
"OK"
redis> HGET myhash field1
"Hello"
redis> HGET myhash field2
"World"
```

#### List（列表）

​	Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。列表最多可存储 2^32 - 1 元素 (4294967295, 每个列表可存储40多亿)。

```shell
redis 127.0.0.1:6379> lpush runoob redis
(integer) 1
redis 127.0.0.1:6379> lpush runoob mongodb
(integer) 2
redis 127.0.0.1:6379> lpush runoob rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```

#### Set(集合)

​	Redis的Set是string类型的无序集合。集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。集合中最大的成员数为 2^32 - 1(4294967295, 每个集合可存储40多亿个成员)。

```shell
redis 127.0.0.1:6379> sadd runoob redis
(integer) 1
redis 127.0.0.1:6379> sadd runoob mongodb
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers runoob

1) "rabitmq"
2) "mongodb"
3) "redis"
```

**注意：**以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。

#### zset(sorted set：有序集合)

​	Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

```shell
redis 127.0.0.1:6379> zadd runoob 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> ZRANGEBYSCORE runoob 0 1000

1) "redis"
2) "mongodb"
3) "rabitmq"
```

以上命令是一些基础命令。更多命令参考[https://redis.io/commands](https://redis.io/commands)



