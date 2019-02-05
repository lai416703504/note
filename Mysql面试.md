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
![解决思路](https://raw.githubusercontent.com/lai416703504/note/master/%E8%BF%81%E7%A7%BB%E6%95%B0%E6%8D%AE%E5%BA%93%E8%B4%A6%E5%8F%B7%E8%A7%A3%E5%86%B3%E6%80%9D%E8%B7%AF.png?token=Af_AkGndk-xaHKX2ePGNz0filHhjKfb1ks5cWYSpwA%3D%3D)

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
