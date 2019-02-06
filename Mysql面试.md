# 第一章 课程介绍
+ 这个课程不是面试题典
+ 这个课程需要具有一定的MySQL基础和工作经验
+ 通过这个课程的学习可以具有解决问题的能力，而不仅仅是面试

# 第二章 MySQL版本类问题
+ 工作中用什么版本的MySQL？为什么用这个版本？
+ 如何决定数据库是否升级？如何升级？
+ 最新的版本是什么？有什么特性吸引你？

## 为什么选择某一版本的MySQL？
### 常见的发行版本
+ 官方MySQL
+ Percona MySQL
+ MariaDB

Percona 性能优于官方，但因为二次开发，版本低于官方

MariaDB 以官方5.5版本的源码开发的，不完全兼容官方的MySQL


#### 服务器特性
|MySQL|Percona Mysql|MariaDB|
|:----:|:----------:|:-----:|
|开源|开源|开源|
|支持分区表|支持分区表|支持分区表|
|InnoDB|XtraDb|XtraDb|
|企业版监控工具<br/>社区版不提供|percon Monitor|Monyog|

#### 高可用特性
|MySQL|Percona Mysql|MariaDB|
|:----:|:----------:|:-----:|
|基于日志点复制|基于日志点复制|基于日志点复制|
|基于Gtid复制|基于Gtid复制|基于Gtid复制<br/>但不兼容mysql|
|MGR|MGR & PXC|Galera Cluster|
|MySQL Router|Proxy SQL|MaxScale|

#### 安全特性
|MySQL|Percona Mysql|MariaDB|
|:----:|:----------:|:-----:|
|企业版防火墙|ProxySQL Firewall|MaxScale FireWall|
|企业版用户审计|审计日志|审计日志|
|用户密码生命周期|用户密码生命周期|-|
|sha256_password<br/>caching_sha2_password|sha256_password<br/>caching_sha2_password|ed25519<br/>sha256_password|

#### 开发管理
|MySQL|Percona Mysql|MariaDB|
|:----:|:----------:|:-----:|
|窗口函数（8.0）|窗口函数（8.0）|（10.2）|
|-|-|支持基于日志回滚|
|-|-|支持记在表中记录修改|
|Super read_only|Super read_only|-|

## 对MySQL升级前要考虑什么？
> 高版本可以做低版本的从库
>
> 低版本做从库可能会有很多问题

### 1.  升级可以给业务带来的益处
 > #### 1. 是否解决业务上的某一痛点
 >>例如:
 >>
 >>  5.6到5.7 主从延迟会降低
 >>
 >> MySQL8.0 对json数据只修改，修改的键值对。而以前的版本是整条json修改
 > #### 2. 是否解决运维上的某一痛点

### 2. 升级可能对业务带来的影响
> #### 1. 对原业务程序的支持是否有影响
>> 例如：
>>
>> JDBC是否支持新版本
> #### 2. 对原业务程序性能是否影响

### 3. 数据库升级方案的制定
> #### 1. 评估受影响的业务系统
> #### 2.升级的详细步骤
>> 1. 对待升级的数据库进行备份
>> 2. 升级Slave服务器的版本
>> 3. 手动进行主从切换
>> 4. 升级MASTER服务器的版本
>> 5. 升级完成后的业务检查
> #### 3. 升级后数据库环境检查
> #### 4. 升级后的业务检查


### 4. 升级失败的回滚方案
> #### 1. 升级失败回滚步骤
> #### 2. 回滚后数据库环境检查
> #### 3. 回滚后业务检查

## 最新版本及新特性

### MySQL 8.0 版本主要新特性

#### 服务器功能
>  所有元数据使用InnoDB，无frm文件
>
>  系统表用InnoDB存储并采用独立表空间
>
>  支持定义资源管理项目（目前仅支持CPU资源）
>
>  支持不可见索引和降序索引，支持直方图优化
>
>  支持窗口函数
>
>  支持在线修改全局参数持久化

#### 用户及安全
>  默认使用 caching_sha2_password认证插件
>
>  行政支持定义角色（role），相同角色权限相同，8.0以前的版本需要给每个用户授权，没有角色字段
>
>  新增密码记录功能，限制重复使用密码

#### InnoDB 功能
> DDL语句支持院子操作
>
> 支持在线修改UNDO表空间
>
> 新增管理视图用于监控InnoDB表状态
>
> 新增 innodb_dedicated_server 配置项



# 第三章 用户管理类问题

## 如何在给定场景下为某用户授权？
### 1. 如何定义MySQL数据库账号？
用户名@可访问控制列表
> 1. %:所有外部主机可访问
> 2. 192.168.1.%:192.168.1.xxx 网段可访问
> 3. localhost:本地可访问DB


### 2. MySQL常用的用户权限

|  | 语句 | 说明 |
|-----|:----------------:|:--------------------:|
|Admin|Create User|建立新的用户的权限|
|Admin|Grant option|为其他用户授权的权限|
|Admin|Super|管理服务器的权限|
|DDL|Create|新建数据库，表的权限|
|DDL|Alter|修改表结构的权限|
|DDL|Drop|删除数据库和表的权限|
|DDL|Index|建立和删除索引的权限|
|DML(DML是比较常用的)|Select|查询表中数据的权限|
|DML(DML是比较常用的)|Insert|向表插入数据的权限|
|DML(DML是比较常用的)|Update|更新表中数据的权限|
|DML(DML是比较常用的)|Delete|删除表中数据的权限|
|DML(DML是比较常用的)|Execute|执行存储过程的权限|


### 如何为用户授权

+ 遵循最小权限原则
+ 使用Grant命令对用户授权
  + 授权
   ```sql
  grant select,insert,update,delete on db.tb to user@ip;
  ```
  + 回收
  ```sql
  revoke delete on db.tb from user@ip;
  ```

## 如何保证数据库账号的安全？

### 用户数据库管理流程规范
1. 最小权限原则
2. 密码强度策略
3. 密码过期原则
4. 限制历史密码重用原则（8.0）

```sql
create user test@'localhost' identified by '123456' password history 1;
```

## 如何迁移数据库账号

### 解决思路
![解决思路](http://image.laihongji.com/1.png)

### 到处用户建立及授权语句
```sql
pt-show-grants u=root,p=123456,h=localhost
```

```sql
CREATE USER IF NOT EXIST 'dba_test'@'10.102.13.%';
ALTER USER 'dba_test'@'10.102.13.%' IDENTIFIED WITH 'mysql_native_password' AS
'*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' REQUIRE NONE PASSWORD EXPIRE DEFALUT ACCOUNT UNLOCK;
GRANT USAGE ON *.* TO 'dba_test'@'10.102.13.%';
```

## 知识点汇总
+ 如何定义MySQL数据库账号？
+ MySQL常用的用户权限
+ 如何为用户授权？
+ 数据库用户管理流程规范
+ 密码管理策略

# 第四章 服务器配置类常见问题

+ 请分析一个 Group By 语句的异常原因
+ 如何比较系统运行配置和配置文件中的配置是否一致？
+ 举几个MySQL中的关键性能参数

## 分析一个 Group By 语句的异常原因

### 表片段

|product_id|category_id|warehouse_id|count|
|:-:|:-:|:-:|:-:|
|2030|9|1|10|
|2030|9|2|15|
|2030|9|3|20|
|2040|8|1|30|
|2040|8|2|20|

### 查询语句
```sql
select product_id,warehouse_id,sum(count) as cnt from stock Gourp By product_id;
```

###  查询结果
查出来却是全为仓库id为1的的商品

|product_id|warehouse_id|cnt|
|:-:|:-:|:-:|
|2030|1|45|
|2040|1|50|

因为group by 只有product_id,但是查询的字段 有product_id和warehouse_id

### SQL_MODE
+ 配置MySQL处理SQL的方式
+ set [session/global/<font color="red">persist</font>] sql_mode = 'xxxxxx'
+ [mysqld] sql_mode=xxxxxx

#### 常用的SQL SQL_MODE
|SQL_MODE|说明|
|:-:|:-:|
|ONLY_FULL_GROUP_BY|对于 GROUP BY 聚合操作，如果出现在 SELECT 中的列、HAVING 或者 ORDER BY 子句的非聚合列，没有在 GROUP BY 中出现，那么这个SQL语法检查报错|
|ANSI_QUOTES|禁止用和双引号来引用字符串|
|REAL_AS_FLOAT|Real作为float的同义词|
|PIPES_AS_CONCAT|将&#124;&#124;视为字符串的连接操作符而非 或 运算符 |
|STRICT_TRANS_TABLES<br/>/STRICT_ALL_TABLES|在事务存储引起/所有存储引擎上启用严格模式出现，那么这个SQL语法检查报错。|
|ERROR_FOR_DIVISION_BY_ZERO|不允许0为除数|
|NO_AUTO_CREATE_USER|在用户不存在时不允许grant语句自动建立用|
|NO_ZERO_IN_DATE<br/>NO_ZERO_DATE|日期数据内/日期数据不能含0|
|NO_ENGINE_SUBSTITUTION|当制定的存储引擎不可用时报错。|

## 比较系统运行配置和配置文件中的配置

+ 使用 set 命令配置动态参数
+ 使用 pt-config-diff 工具比较配置文件

### 使用 set 命令配置动态参数
+ set [session | @@session.] system_var_name = expr
+ set [global | @@global.] system_var_name = expr
+ set [persist | @@persist.] system_var_name = expr

### 使用 pt-config-diff 工具比较配置文件
+ pt-config-diff u=root,p=,h=localhost /etc/my.cnf

## 举几个MySQL中的关键性能参数

### 常用的性能参数

#### 服务器配置参数
|参数|说明|
|:-:|:-:|
|max_connections|设置MySQL允许访问的最大连接数量|
|interactive_timeout|设置交互连接的timeout时间|
|wait_timeout|设置非交互连接的timeout时间|
|max_allowed_packet|控制MySQL可以接受的数据包大小（主从库最好设置的一样）|
|sync_binlog|表示每写多少次缓冲会向磁盘同步一次binlog|
|sort_buffer_size|设置每个会话使用的排序缓存区大小|
|join_buffer_size|设置每个会话所使用的连接缓冲的大小|
|read_buffer_size|指定了当对一个MYISAM进行表扫描时说分配的读缓存池的大小|
|read_rnd_buffer_size|设置控制索引缓冲区的大小|
|binlog_cache_size|设置每个会话用于缓存末提交的事务缓存大小|

#### 存储引擎参数
|参数|说明|
|:-:|:-:|
|innodb_flush_log_at_trx_commit|0:每秒进行一次重做日志的磁盘刷新操作<br/>1:每次事务提交都会刷新事务日志到磁盘中<br/>2:每次事务提交写入系统缓存每秒向磁盘刷新一次|
|innodb_buffer_pool_size|设置Innodb缓冲池的大小，应为系统可用内存大的75%|
|innodb_buffer_pool_instances|Innodb缓冲池的实例个数，每个实例的大小为总缓冲池大小/实例个数。|
|innodb_file_per_table|设置每个表独立使用一个表空间文件|

# 第五章 MySQL日志类问题

+ 常用的MySQL日志有哪些？我们在什么情况下使用这些日志?
+ 如何通过日志类审计用户活动？
+ MySQL常用的日志类型
+ 各种日志的配置和使用场景

## MySQL常用的日志类型
|日志名称|作用|
|:-:|:-:|
|错误日志（error_log）|记录mysql在启动、运行或停止时出现的问题|
|常规日志（general_log）|记录所有发向MySQL的请求|
|慢查询日志(slow_query_log)|记录符合条件的查询|
|二进制日志(binary_log)|记录全部有效的数据修改日志|
|中继日志（relay_log）|用于主从复制，临时存储从主库同步的二进制日志|

## 第五章未完待续


# 第六章 存储引擎类问题
+ 说一说你连接的MySQL存储引擎及其使用场景
+ 在什么情况下Innodb无法在线修改表结构？
+ 在无法进行在线修改表结构的情况下，要如何操作？

### 常见问题
+ InnoDB 是如何实现事务的？
+ InnoDB 读操作是否会阻塞写操作？

## 常用的MySQL存储引擎及使用场景
### 知识点
+ MySQL常用的存储引擎
+ 各种常见存储引擎的使用场景

### MySQL常用的存储引擎
|引擎名称|事务|说明|
|:-:|:-:|:-:|
|MYISAM|N|MySQL5.6之前的默认引擎，最常用的非事务型存储引擎|
|CSV|N|以CSV格式存储的非事务型存储引擎|
|Archive|N|只允许查询和新增数据而不允许修改的非事务型存储引擎（日志）|
|Memory|N|是一种易失性非事务型存储引擎|
|INNODB|Y|最常用的事务型存储引擎|
|NDB|Y|MySQL集群所使用的内存型事务存储引擎|

### MyISAM的特点
1. 非事务型的存储引擎
2. 以堆表方式存储
3. 使用表级锁
4. 支持Btree索引，空间索引，全文索引
#### MyISAM使用场景
 + 读操作远远大于写操作的场景
 + 不需要使用事务的场景

### CSV引擎的特点
1. 非事务型存储引擎
2. 数据以CSV格式存储
3. 所有列都不能为NULL
4. 不支持索引(不适合频繁的查询和更新)
#### CSV引擎的适用场景
 + 作为数据交换的中间表使用

### Archive引擎的特点
1. 非事务型存储引擎
2. 表数据使用zlib压缩（比myisam和innodb节约存储空间）
3. 只支持Insert和Select
4. 只允许在自增ID上建立索引
#### Archive引擎的适用场景
 + 日志和数据采集类应用
 + 数据归档存储

### Memory引擎的特点
1. 非事务型存储引擎
2. 数据保存在内存中
3. 所有字段长度固定
4. 支持 Btree 和 Hash 索引
#### Memory引擎的适用场景
 + 用于缓存字典映射表
 + 缓存周期性分析数据（当然可以用redis）

### InnoDB引擎的特点
1. 事务型存储引擎支持ACID
2. 数据按主键聚集存储
3. 支持行级锁及MVCC
4. 支持 Btree 和自适应 Hash 索引（由Innodb本身生成的索引，不需要人为干预）
5. 支持全文和空间索引（5.6之后全文，5.7之后空间）
#### InnoDB引擎的使用场景
 + 大多数OLTP场景

### NDB引擎的特点（出名，但不常用, 集群用的[这里指的集群是NDB集群]）
1. 事务型存储引擎
2. 使用前要从磁盘数据存储在内存中（对服务器内存大小的要求随数据量增大）得不到广泛使用的重要原因
3. 支持行级锁
4. 支持高可用集群
5. 支持 Ttree 索引(其他的是Btree)
#### NDB引擎的使用场景
 + 需要数据完全同步的高可用场景

## 在什么情况下INNODB无法在线修改表
### 知识点
+ InnoDB不支持在线修改表结构的场景
+ 如何更安全的在线修改表结构

### Innodb 不支持在线修改表结构的场景
|操作|语法|
|:------|:---------|
|加全文索引|CREATE FULLTEXT INDEX name ON table(column);|
|加空间索引|ALTER TABLE geom ADD SPATIAL INDEX(g)|
|删除主键|ALTER TABLE tbl_name DROP PRIMARAY KEY|
|增加自增列|ALTER TABLE t ADD COLUMN id INT AUTO_INCREMENT NOT NULL PRIMARY KEY|
|修改列类型|ALTER TABLE tbl_name CHANGE c1 c1 NEW_TYPE|
|修改字符集|ALTER TABLE tbl_name CHARACTER SET = charset_name|

### 在线DDL存在的问题
+ 有部分语句不支持在线DDL
+ 长时间的DDL操作会引起严重的主从延迟
+ 无法对DDL操作进行资源限制

### 如何跟安全的执行DDL
+ pt-online-schema-change [OPTIONS] DSN (工具)

## INNODB是如何实现事务？
### 知识点
+ 什么是事务？
+ Redo log 和 undo Log 在事务实现中的作用

### 什么是事务？
|特征|说明|
|:---:|:--:|
|原子性(<font color="red">A</font>)|一个事务中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节|
|一致性(<font color="red">C</font>)|在事务开始之前和事务结束以后，数据库的完整性没有被破坏|
|隔离性(<font color="red">I</font>)|事务的隔离性要求每个读写事务的对象与其他事务的操作对象能相互分离，即该事务提交前对其它事务都不可见。|
|持久性(<font color="red">D</font>)|事务一旦提交了，其结果就是永久性的，就算发生了宕机等事故，数据库也能将数据恢复。|

### 事务的实现方式
|特征|INNODB实现方式|
|:---:|:--:|
|原子性(<font color="red">A</font>)|回滚日志(Undo log):用于记录数据修改前的状态|
|一致性(<font color="red">C</font>)|重作日志(Redo log):用于记录数据修改后的状态|
|隔离性(<font color="red">I</font>)|锁:用于资源隔离，分为共享锁和排它锁|
|持久性(<font color="red">D</font>)|重作日志(Redo log)+回滚日志(Undo log)|

> A账户向B账户汇500元
![A账户向B账户汇500元](http://image.laihongji.com/2.png)

## INNODB读是否会阻塞写？
### 读写应该相互塞吗？
+ 查询需要对资源加共享锁(S)
+ 数据修改需要对资源加排它锁
+ 写操作不会阻塞读

||排它锁|共享锁|
|::|::|::|
|排它锁|不兼容|不兼容|
|共享锁|不兼容|兼容|

### 知识点
+ InnoDB MVCC 的实现方式

### MVCC(多版本的并发控制)
![MVCC原理](http://image.laihongji.com/3.png)

## 知识点总结
+ MySQL常见的存储引擎及使用场景
+ InnoDB在线DDL的限制及如何安全的执行DDL操作
+ InnoDB事务和MVCC的实现方式

# 后面章节未完待续
