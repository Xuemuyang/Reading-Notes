# Redis

## 安装

主要有四种方式

- Docker
- Github源码
- brew等工具
- [Web Redis](https://try.redis.io/)

## 基础数据结构

- string
- list
- set
- hash
- zset

## 基本命令

```sh
SET server:name "fido" // 给server:name这个key指定value
GET server:name => "fido" // 获得server:name这个key的value
INCR
```

```sh
SET connections 10
INCR connections => 11 // increment 指定的key自动增加
INCR connections => 12
DEL connections // 删除指定的key关联的value
INCR connections => 1
```

Redis提供的INCR操作是一个 `atomic` 操作符，因为分布式考虑。

```sh
setnx connections 1 // SET if Not eXists 当不存在的时候才设置成功，成功return 1，失败return 0
```

Redis提供了 `EXPIRE` 和 `TTL` 命令来控制缓存

```sh
SET resource:lock "Redis Demo"
EXPIRE resource:lock 120
```

```sh
TTL resource:lock => 113
// after 113s
TTL resource:lock => -2
```

`TTL` 命令检查 key 的过期时间，`-1`不会过期， `-2`不存在 key

```sh
SET resource:lock "Redis Demo 1"
EXPIRE resource:lock 120
TTL resource:lock => 119
SET resource:lock "Redis Demo 2"
TTL resource:lock => -1
```

## list

- RPUSH // puts the new value at the end of the list
- LPUSH // puts the new value at the start of the list
- LLEN // returns the current length of the list
- LRANGE // gives a subset of the list. It takes the index of the first element you want to retrieve as its first parameter and the index of the last element you want to retrieve as its second parameter. A value of -1 for the second parameter means to retrieve elements until the end of the list
- LPOP // removes the first element from the list and returns it
- RPOP // removes the last element from the list and returns it

```sh
RPUSH friends "Alice"
RPUSH friends "Bob"
LPUSH friends "Sam"
LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
LRANGE friends 1 2 => 1) "Alice", 2) "Bob"

LLEN friends => 3
LPOP friends => "Sam"
RPOP friends => "Bob"
LLEN friends => 1
LRANGE friends 0 -1 => 1) "Alice"
```

## set

- SADD // adds the given value to the set
- SREM // removes the given value from the set
- SISMEMBER // tests if the given value is in the set. It returns 1 if the value is there and 0 if it is not
- SMEMBERS // returns a list of all the members of this set
- SUNION // combines two or more sets and returns the list of all elements

```sh
SADD superpowers "flight"
SADD superpowers "x-ray vision"
SADD superpowers "reflexes"
SREM superpowers "reflexes"
SISMEMBER superpowers "flight" => 1
SISMEMBER superpowers "reflexes" => 0
SMEMBERS superpowers => 1) "flight", 2) "x-ray vision"
SADD birdpowers "pecking"
SADD birdpowers "flight"
SUNION superpowers birdpowers => 1) "pecking", 2) "x-ray vision", 3) "flight"
```

## zset

zset 是 Sorted Sets

## hash

- HSET
- HGETALL
- HMSET
- HGET
