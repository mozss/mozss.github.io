---
title: JDBC驱动
tags:
  - mysql
categories:
  - code
abbrlink: 62876
date: 2019-03-01 03:16:20
---

<!--more-->

## 驱动
Mysql的驱动jar, 默认有两个功能, 主从分离和HA. 
如果你只是需要一个主从分离, failover功能, 不要sharding, 一个驱动就够,也就不需要引入中间层.

有个Replication协议, 在5.1.x版本之后,增加了这些功能, 以用来支持"multi-host"集群拓扑的访问范式. 这个功能是在驱动层实现的.

jdbc连接如下
```java
jdbc:mysql://127.0.0.1:3306/test?characterEncoding=UTF-8
```
通过协议改造后的jdbc连接如下
```java
jdbc:mysql:replication://127.0.0.1:3306,127.0.0.1:3307,127.0.0.1:3308/test?useUnicode=true&characterEncoding=UTF-8&autoReconnect=false&loadBalanceStrategy=random
```
协议后的第一个连接, 表示主库Master
后面一堆连接,表示从库Slave, 当然可以有多个
当你把Master的连接也放在后面的一堆里面, 那么它也拥有"读库"的属性了
后面有一堆参数, 来控制这所有连接, 到底要如何相处

## 代码层
对于Spring来说, 就可以使用Transaction注解来控制这个属性, 一个事务不可能跨两个连接, 所以是读还是写, 由最高层决定.
```java
public interface UserManager{
	public UserDO get(int id);
	public void insert(UserDo user);
	public void update(int id);
}
```
```java
@Component
public class UserManagerImpl implements UserManager{
	@Autowired
    private UserDao userDao;
    //...
    @Transactional(readOnly = false, propagation=Propagation.REQUIRED)
    public void insert(UserDO user){
		this.userDao.insert(user);
    }
}
```
## 参数
[![ppnHKbD.png](https://s1.ax1x.com/2023/03/10/ppnHKbD.png)](https://imgse.com/i/ppnHKbD)

- readFromMasterWhenNoSlaves当所有的salve死掉后, 此参数用来控制主库师傅参与读
- loadBanlanceStrategy策略用来指定从库的轮询规则. 有轮询, 也有权重, 也可用指定具体策略实现. 当你维护或迁移某个实例时, 先置空流量, 这回非常有用. 或许, 你会给某个DB一个预热的可能.
- allowMasterDownConnections如果主机宕机,当连接池获取新的连接时会失败.
- retriesAllDown 当所有的hosts都无法连接时重试的最大次数（依次循环重试）,默认为120.
- autoReconnect 实例既然有下线、就有上线。上线以后要能够继续服务，此参数用来控制断线情况下自动重连而不抛出异常.
