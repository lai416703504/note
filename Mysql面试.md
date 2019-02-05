# 第三章 用户管理类问题


## MySQL常用的用户权限

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


## 如何为用户授权

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
![图片] http://baidu.com

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
