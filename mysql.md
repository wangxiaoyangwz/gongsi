# mysql 创建用户与授权
`create user 'uname'@'host' identified by 'password';`
username：你将创建的用户名
host：指定该`用户在哪个主机上可以登陆`，如果是本地用户可用localhost，如果想让该用户可以从`任意远程主机登陆这个mysql`，可以使用`通配符%`
```
+-----------+--------------+
| User      | Host         |
+-----------+--------------+
| cloudchef | %            |
| repluser  | 192.168.12.% |
| cloudchef | localhost    |
| exporter  | localhost    |
| mysql.sys | localhost    |
| root      | localhost    |
+-----------+--------------+
```

### 授权:
`grant [privileges] on [databasename].[tablename] to 'uname'@'host';`

说明:
privileges：用户的操作权限，如SELECT，INSERT，UPDATE等，如果要授予所的权限则使用ALL
databasename：数据库名
tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用*表示，如*.*


# MySQL主从复制(Master-Slave)实践
### 主从复制的功能
MySQL数据库自身提供的主从复制功能
1. 实现数据的多处自动备份
2. 实现数据库的拓展
### 主从复制的优点
1. 多个数据备份不仅可以加强`数据的安全性`，
2. 通过实现读写分离还能进一步提升`数据库的负载性能`。

### 多个数据库间`主从复制`与`读写分离的`模型
`一主多从的数据库体系`
多个`从服务器`采用`异步`的方式`更新主数据库`的变化
>异步:从服务器不需要知道主数据库什么时候更新，主数据库更新会调用函数使从服务器更新主数据库，完成后会返回结果通过回调给主数据库

业务服务器在执行写或者相关`修改数据库的操作是在主服务器上进行的`，`读操作则是在各从服务器上进行`

如果配置了多个从服务器或者多个主服务器又涉及到相应的`负载均衡问题`

### Mysql主从复制的实现原理
MySQL之间数据复制的基础是`二进制日志文件`（binary log file)

MySQL数据库一旦启用二进制日志后，其作为`master`，它的`数据库中所有操作`都会以`“事件”`的方式`记录在二进制日志中`
>事件：比如插入语句等

#### 其他数据库作为`slave`通过一个I/O线程
1. `I/O线程`与主服务器保持通信
2. `I/O线程`监控master的二进制日志文件的变化
3. `I/O线程`发现master二进制日志文件发生变化，则会把变化复制到自己的中继日志中
4. slave的一个`SQL线程`会把相关的“事件”执行到自己的数据库中，以此实现从数据库和主数据库的一致性，也就实现了主从复制。

### 实现MySQL主从复制需要进行的配置：
#### 主服务器：
1. 开启二进制日志
2. 配置唯一的server-id
3. 获得master二进制日志文件名及位置
4. 创建一个用于slave和master通信的`用户账号`

#### 从服务器：
1. 配置唯一的server-id
2. 使用`master分配的用户账号`读取master二进制日志
3. 启用slave服务

### 实现过程
#### 一、准备工作：
1.主从数据库版本最好一致

2.主从数据库内数据保持一致

主数据库：182.92.172.80 /linux

从数据库：123.57.44.85 /linux

#### 二、主数据库master修改：
1.修改mysql配置

找到`主数据库的配置文件`my.cnf(或者my.ini)，我的在`/etc/mysql/my.cnf`,在`[mysqld]`部分插入如下两行：
```
[mysqld]
log-bin=mysql-bin #开启二进制日志
server-id=1 #设置server-id
```

2.重启mysql，创建用于同步的用户账号

```
create user 'uname'@'host' 
```

参考：https://www.cnblogs.com/gl-developer/p/6170423.html










# mysql -u cloudchef -pc10udch3f -e "show slave status\G"
### 功能：show slave status命令可以显示`主从同步的状态`
判断Slave_IO_Running 和Slave_SQL_Running两个线程的状态
`Slave_IO线程`负责把`主库的bin日志`(Master_Log)内容，投递到`从库的中继日志`上(Relay_Log)。

`Slave_SQL线程`负责把`中继日志上的语句`在从库上执行一遍。

Yes表示正常，No表示异常

### Slave_IO_Running显示为No的原因
1. 网络问题
2. 权限问题，例如在`配置slave同步`时因为`slave访问master`没有权限导致的问题。
3. mater上的`binlog文件误删`或者其他问题导致的`master库突然停止而更新了binlog日志`,这时候slave可能出现`找不到binlog文件`的情况,解决方案是找到同步的点和binlog文件,重新 change master。

### Slave_SQL线程容易出错
1. 人为手动的在`从库插入一条数据`，造成`主从不一致`
两个线程的状态任然是正常的，等到`主库也插入一条同样的数据时`，通知从库做相同操作，从库会出现`主键重复`的错误。此时Slave_SQL_Running的状态会变为No，


### Last_SQL_Error，Last_SQL_Error_Timestamp会记录错误的原因和发生时间。
`Slave_SQL线程`会停止后续的SQL语句执行
>`Slave_SQL线程`负责把`中继日志上的语句`在从库上执行一遍。

### 想继续同步后面的SQL，忽略报错则要执行下面的命令:
```
# set global sql_slave_skip_counter=N以跳过命令
# N=1时，会连续跳过若干个event，直到当前所在的事务结束
# 当然如果N>1，则每跳过一个event都要N--,位置若刚好落在一个事务内部，则会跳过这整个事务;
set global sql_salve_skip_counter=1
start slave;


```

### 如何判断主从完全同步
```
Master_Log_File和Relay_Master_Log_File所指向的文件必须一致
Relay_Log_Pos和Exec_Master_Log_Pos的位置也要一致才行
Slave_SQL_Running_State：显示为wait 意思是中继日志的sql语句已经全部执行完毕 
```


参考：https://www.cnblogs.com/chinesern/p/7279495.html

