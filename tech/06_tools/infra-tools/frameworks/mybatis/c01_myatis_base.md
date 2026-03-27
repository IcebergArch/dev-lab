# mybatis

## why use mybatis

解决了以下几个通用的问题：
1. 链接管理
2. 方法调用转SQL
3. 缓存
4. 结果转对象

MyBatis 封装了连接管理、预编译 & 非预编译 SQL 执行、参数类型映射，以及缓存机制，屏蔽了 JDBC 细节，让开发者可以用 Mapper 方法直接操作数据库;


## 通过一次SQL执行理解mybatis运转过程

缓存KEY规则: Mapper ID + SQL + 参数 + Offset + Limit

对于：
Mapper: com.dev.mapper.UserMapper
SQL: select * from user where name = #{name} and age = #{age}

userMapper.getUser('Alice')

缓存KEY为："com.dev.mapper.UserMapper.getUser | select * from user where name = ? and age = ? | [Alice, 12] | offset=0 | limit=0"

1. 获取SqlSession (OR buildSqlSession)
2. 尝试从Mybatis缓存中获取结果
    2.1 检查sqlSession中是否有( key )
    2.2 检查Mapper缓存中是否有( key )
3. 执行sql
    Mybatis： 负责解析SQL，对于非预编译 ${name} 直接拼接最终SQL；对于预编译 #{name} 转换为预编译格式
    JDBC: 对于接收到的SQL转换为DB接收的格式
    MySQL: 对于接收到的语句，非预编译直接执行；预编译编译后执行
    返回结果
4. 解析结果
    Mybatis：转换为对象list
    更新缓存：key, 对象list
5. 如果方法结束，销毁Session
    如果是spring管理的，同一个事务是复用的，等事务结束销毁

注意：
- 缓存更新：mybatis 主要是做查询方面的优化，因此对于两个缓存而言，遇到insert update delete, commit 则会清空缓存（如果事务过程中有写后查，会频繁加载缓存）
- 一级缓存：默认情况下一级缓存是开启的，而且是不能关闭的。一级缓存最多缓存 1024 条 SQL。二级缓存是 mapper 级别的缓存不同的sqlsession 是可以共享的。
- 二级缓存：二级缓存的范围是 mapper 级别（mapper 同一个命名空间），mapper 以命名空间为单位创建缓存数据结构，结构是 map。mybatis 的二级缓存是通过 CacheExecutor 实现的。CacheExecutor 其实是 Executor 的代理对象。所有的查询操作，在CacheExecutor 中都会先匹配缓存中是否存在，不存在则查询数据库。
- Spring管理Session：
    @Transactional(propagation = Propagation.REQUIRED) => 同一个事务用一个Session; 
    @Transactional(propagation = Propagation.REQUIRES_NEW) 挂起外层事务，新建session

![alt text](../images/mybatis.png)