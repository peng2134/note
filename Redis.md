# Redis

Redis 是一个高性能的key-value的数据库

存储形式：键值对



安装教程

然后在Ubuntu上操作,源码安装redis
1.下载
wget https://download.redis.io/releases/redis-6.0.9.tar.gz
2.解压
tar xzf redis-6.0.9.tar.gz
3.移动到你要安装的目录,我这里安装到了/user/local下
sudo mv ./redis-6.0.9 /usr/local/redis
4.进入你移动的目录
cd /usr/local/redis
5.编译redis
sudo make
6.测试编译是否成功(这一步时间会比较长,测试耗时5分钟左右)
sudo make test
7.安装
sudo make install



# 数据类型

![image-20240814203246001](E:\Code\笔记\笔记图片\image-20240814203246001.png)

# 应用

图片验证码





# 增删改查

设置

```redis
set key value
```

设置过期时间

```redis
-- 创建时设置
setex key second value

-- 创建后设置
expire age seconds

-- 查看有效期
ttl key
```

设置多个

```redis
mset key1 value1 key2 value2
```

获取

```redis
get key

get name

mget key1 key2
```



## 对键操作



```redis
-- 查看键
keys *

-- 判断键是否存在

exists key

-- 看数据类型

type key

-- 删除
del key value
```

## hash操作

```redis
都以h开头
-- 设置值
hset key field value 

hmset key field value 

示例：
hset person name zxp

-- 获取值
hget key field
hget key field1 field2

-- 获取键
hkeys key

-- 获取值
hvals key

-- 删除
hdel key field1 field2
```



## 列表操作

```redis
--左操作 以l开头

lpush key value1 value2

lrange key start stop

lrem key count(删除的个数，大于0从左侧开始删除，小于0从右侧开始删除) value

--右操作 以r开头
rpush key value1 value2


```

## 集合

```redis
-- 无需，唯一，不可修改,string

--- 以s开头

-- 添加
sadd key member1 member2
-- 获取
smembers key
-- 删除
srem key value
```

## 有序集合

```redis
通过权重对元素的大小进行排序

-- 添加
zadd key score1 member1 score2 member2
-- 获取
zrange key start stop
--删除
zrem key member1 member2
```

