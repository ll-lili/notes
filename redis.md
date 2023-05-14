#### Redis(windows安装)

* https://github.com/tporadowski/redis/releases
* 打开一个 **cmd** 窗口 使用 cd 命令切换目录到 **C:\redis** 运行：redis-server.exe redis.windows.conf
* 另启一个 cmd 窗口： redis-cli.exe -h 127.0.0.1 -p 6379

#### Redis(mac安装)

```shell
# 安装redis https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/
brew install redis

# 启动redis, control + c 关闭
redis-server 

# 启动redis(后台启动)
brew services start redis

# 关闭redis
brew services stop redis

# 查看reids状态
brew services info redis

# 链接redis
redis-cli
redis-cli -h 127.0.0.1 -p 6379 -a 密码

# 链接后查看redis server信息
info server

# 查看是否需要密码
CONFIG GET requirepass
# 设置密码
CONFIG SET requirepass 123456
```

#### Linux下修改redis配置文件

```shell
# 安装vim
sudo apt-get install vim

#修改配置文件
sudo vim /etc/redis/reids.conf
# 添加 bind * 让所有客户端链接redis 
# protectd-mode yes 开启安全模式
# port 6379 端口 
# requirepass 123456 开启密码
```



#### Redis客户端操作

```shell
# 查看存储的key值
127.0.0.1:6379> keys *
(empty array)
# set key value 添加键值对, redis默认有16个数据库0-15，无法修改数据库名，默认是0号数据库
127.0.0.1:6379> set k1 v1
OK
# 切换数据库 select index
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]>
# move key db 移动数据到其他数据库
127.0.0.1:6379> move k1 2
(integer) 1
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> keys *
1) "k1"
127.0.0.1:6379[2]> get k1
"v1"
# flushall 清空所有数据库的数据
flushall
# flushdb 清空当前数据库的数据
flushdb
```

#### Redis数据类型操作

* 字符串strings

  ```shell
  # 设置单个值
  SET key value
  # 设置多个值
  MSET k1 v1 k2 v2
  # 设置并返回之前的值
  GETSET k1 v11
  # 获取多个值
  MGET k1 k2
  # 获取某个key的长度
  STRLEN k1
  # 获取存储值的类型
  TYPE k1
  # 删除key
  DEL k1 k2
  # 重命名
  RENAME k1 kk1
  ```

* 散列hashes

  ```shell
  # 设置hash
  HSET user username lisi age 20
  # 查看hash的key
  HKEYS user
  # 获取hash的长度
  HLEN user
  # 获取hash的值
  HGET user username
  # 获取hash多个值
  HMGET user username age
  # 获取整个hash keuy value
  HGETALL user
  # 删除hash的key
  HDEL user username
  # 删除整个hash
  DEL user
  ```

* 列表lists

  ```shell
  # 向列表添加元素:将一个新元素添加到列表的头部(RPUSH 添加到尾部)
  LPUSH list 12 23 34
  # 获取指定范围的元素
  LRANGE 0 -1
  # 向指定元素之前插入元素
  LINSERT list before 12 22
  # 获取指定位置的元素
  LINDEX list 2
  # 获取列表的长度
  LLEN list
  # 获取并移除第一个元素
  LPOP list
  # 获取并移除最后一个元素
  RPOP list
  ```

* 集合sets

  ```shell
  # 向集合中添加成员
  SADD s1 11 22 33
  # 查看集合中成员
  SMEMBERS s1
  # 获取集合中成员数
  SCARD s1
  # 随机获取一个或多个成员成员
  SRANDMEMBER s1
  # 删除某个成员
  SREM s1 33 22
  # 随机删除某个成员
  SPOP s1 
  ```

* 有序集合sorted sets

  ```shell
  # 添加一个有序集合
  ZADD z1 60 u1 80 u2 59 u3 90 u4 76 u5 99 u6
  # 返回给定范围内的排序成员，低到高,ZREVRANGE反向排序
  ZRANGE 3 6 # 第7到第4名的排序
  # 获取指定元素的排名,低到高从0开始,ZREVRANK反向排序
  ZTANK z1 u6
  # 获取成员数
  ZCARD z1
  # 修改成员的值，在原来基础上增加指定增量
  ZINCRBY z1 2 u1
  # 删除集合中成员
  ZREM z1 u3
  ```

  



#### nodejs链接redis

```javascript
// 安装redis依赖 npm install redis （"redis": "^2.8.0"）
const redis = require('redis')

// 创建客户端
const redisClient = redis.createClient(6379, '127.0.0.1')

redisClient.on('error', err => {
    console.log(err)
})

// 测试
redisClient.set('username', 'zhangsan', redis.print)
redisClient.get('username', (err, val) => {
    if (err) {
        console.log(err)
        return
    }
    console.log(val)
    // 退出
    redisClient.quit()
})

// "redis": "^4.6.6"
;(async function () {
  const redis = require('redis')

  const redisClient = redis.createClient({
    url: 'redis://127.0.0.1:6379' // redis[s]://[[username][:password]@][host][:port][/db-number]
  })

  redisClient.on('error', (err) => {
    console.log(err)
  })
  await redisClient.connect()

  await redisClient.set('username', 'lisi')
  const val = await redisClient.get('username')
  console.log(val)
  await redisClient.disconnect()
})()

// https://github.com/luin/ioredis npm install ioredis

const Redis = require("ioredis")

const redis = new Redis()
redis.set("mykey", "value")

redis.get("mykey", (err, result) => {
  if (err) {
    console.error(err);
  } else {
    console.log(result); // Prints "value"
  }
});

redis.get("mykey").then((result) => {
  console.log(result); // Prints "value"
})
```