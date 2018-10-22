*Redis是一个字典结构的存储服务器,一个Redis实例提供了多个用来存储数据的字典,每个字典都理解成一个独立的数据库,首先Redis不支持自定义数据库的名字，每个数据库都以编号命名，开发者必须自己记录哪些数据库存储了哪些数据。另外Redis也不支持为每个数据库设置不同的访问密码，所以一个客户端要么可以访问全部数据库，要么连一个数据库也没有权限访问。最重要的一点是多个数据库之间并不是完全隔离的，比如FLUSHALL命令可以清空一个Redis实例中所有数据库中的数据。综上所述，这些数据库更像是一种命名空间，而不适宜存储不同应用程序的数据。比如可以使用0号数据库存储某个应用生产环境中的数据，使用1号数据库存储测试环境中的数据，但不适宜使用0号数据库存储A应用的数据而使用1号数据库B应用的数据，不同的应用应该使用不同的Redis实例存储数据*
[TOC]
#### 数据类型
- string
  > 可包含jpg图片或者序列化的对象
  > SET name "value"  | GET NAME 包含任何数据,
  - 方法
    1.set get
    2.

- hash
  > 键值对的集合，适合存储对象
  - 方法
    1.hget hset hdel hgetall

- List
  - 方法
    1.lpop lpush lrange lset rpop

- set
  - 方法
    1.sadd scard sdiff smember smove spop

- sorted set
  - 方法
    1。zadd zcard zcount zrange zscore
