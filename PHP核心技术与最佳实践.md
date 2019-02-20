## 第四章 PHP网络技术及应用

### 4.3 Socket 进程通信机制及应用
#### 4.3.1 进程相关概念

##### 1. 端口
   + TCP与UDP是完全独立的两个模块，各自端口也独立
   + TCP与UDP结构中端口的地址都是16bit，有0~65535个端口号
   + 小于256定义为常用端口
   + 常见的端口有FTP的21端口,HTTP的80端口，SMTP（***简单邮件传输服务***）25端口
##### 2.地址
##### 3.连接
两个进程间的通信链路，连接表现为一些缓冲区和一组协议的机制。 

### 4.8 Session
修改SESSION存储方式的函数
``` php
function session_set_save_handler (SessionHandlerInterface $session_handler, $register_shutdown = true) {}

//sessionhandler
//实现了 SessionHandlerInterface， SessionIdInterface 和/或 SessionUpdateTimestampHandlerInterface 接口的对象， 例如 SessionHandler。 自 PHP 5.4 之后可以使用。

//register_shutdown
//将函数 session_write_close() 注册为 register_shutdown_function() 函数。

```

### 5.4 MySQL 的高级应用
#### 5.4.1 MySQL自增长序列
> MySQL一个表只能有一个自增长
##### 下列情况可能需要使用序列（不一定是主键）
+ 业务复杂，需要定制和控制主键时（自增主键只能是按数字递增的，但是序列可以随心所欲地变化，比如按照年月日生成主键）；
+ 希望手工维护自增长，方便数据迁移时；
+ 当事务跨多表，期望事务可靠性时；
+ 需要一个业务上有意义的主键时，比如单据号（若只是一个流水号，那么用自增长更方便）；
+ 主键很明确地需要和其他表关联时；
+ 期望主键是唯一的，不需要重复利用时；