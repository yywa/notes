# Java使用Redis

​	保证java环境正常，以及redis服务的启动。

​	下载驱动包jedis.jar。

```java
package test0306;

import redis.clients.jedis.Jedis;

import java.util.Iterator;
import java.util.List;
import java.util.Set;

public class RedisJava {
    public static void main(String[] args) {
        //连接本地的redis服务器
        Jedis jedis = new Jedis("localhost");
        System.out.println("连接成功");
        //查看服务是否运行
        System.out.println("服务正在运行: " + jedis.ping());

        //设置 redis 字符串数据
        jedis.set("name", "yyw");
        // 获取存储的数据并输出
        System.out.println("redis 存储的字符串为: " + jedis.get("name"));
        //存储数据到列表中
        jedis.lpush("list", "redis");
        jedis.lpush("list", "mysql");
        jedis.lpush("list", "oracle");
        // 获取存储的数据并输出
        List<String> list = jedis.lrange("list", 0, 2);
        for (int i = 0; i < list.size(); i++) {
            System.out.println("列表项为: " + list.get(i));
        }
        // 获取所有key并输出
        Set<String> keys = jedis.keys("*");
        Iterator<String> it = keys.iterator();
        while (it.hasNext()) {
            String key = it.next();
            System.out.println(key);
        }
      	//释放资源
        jedis.close();
    }
}

```

```
连接成功
服务正在运行: PONG
redis 存储的字符串为: yyw
列表项为: oracle
列表项为: mysql
列表项为: redis
name
list
yyw
```

