---
layout: post
title:  "mysqldump备份与还原"
date:   2020-02-21 13:17:02
categories: 
   - mysql
tags:
   - mysql
---

#一. 备份类型
> 
逻辑备份：把数据导出到一个文档中（.sql/.txt）
适合场景：中小型数据库
> 
物理备份：保存/usr/local/mysql/data数据库文件目录
适合场景：大型数据库
> 
在线热备（冗余）：需要有多台机器，主备模式，集群模式

#二. 逻辑备份（mysqldump）
> 表级别备份
    ```
    mysqldump [OPTIONS] database [tables]
    ```
>
库级别备份
    ```
    mysqldump [OPTIONS] --databases DB1 [DB2 DB3...]
    ```
>     
全库级别备份
    ```
    mysqldump [OPTIONS] --all-databases [OPTIONS]
    ```

1. 表级别备份 
> 
语法：mysqldump -p密码 数据库名称 表名称 > /tmp/mysqlbak/it_student.sql
案例：把db_itcast中的it_student表进行逻辑备份导出文件放入/tmp/mysqlbak目录下
第一步：创建目录
mkdir /tmp/mysqlbak
第二步：备份数据表it_student
[root@localhost ~] # mysqldump -p123 db_itcast it_student > /tmp/mysqlbak/it_student.sql

2. 表级别恢复 
>  
方法一：
第一步：登录MySQL
mysql -uroot -p
第二步：选择数据库（因为这个表属于某一个数据库）
mysql > use db_itcast;
第三步：使用source 导出sql文件路径+名称
mysql > source /tmp/mysqlbak/it_student.sql
>
方法二：
    ```
    mysql -u root -p 数据库名称 </tmp/mysqlbak/it_student.sql
    ```

3. 库级别备份 
> 
退出mysql，使用mysqldump进行库级别备份   语法：mysqldump -p123 --databases 数据库名称1 [数据库名称2] > /tmp/mysqlbak/db_itcast.sql
>    
特别说明：
如果计算机中有多个MySQL实例，备份一定要指定备份那个MySQL
mysqldump -S /tmp/mysql31.sock
案例：把db_itcast数据库进行备份操作
mysqldump --databases db_itcast > /tmp/mysqlbak/db_itcast.sql -p
Enter password:

4. 库级别恢复 
> 
方法一：
第一步：登录MySQL
    ```
    mysql -uroot -p
    ```
第二步：删除原来的数据库
    ```
    mysql > drop database 数据库名称;
    ```
第三步：使用source命令恢复文件
    ```
    mysql > source /tmp/mysqlbak/db_itcast.sql
    ```
> 
方法二：
    ```
    mysql -u root -p </tmp/mysqlbak/db_itcast.sql
    ```

5. 全库级别备份
> 
基本语法：# mysqldump --all-databases [options] > /tmp/mysqlbak/all.sql
常用参数：
--flush-logs, -F 开始备份前刷新日志
--flush-privileges 备份包含mysql数据库时刷新授权表
--lock-all-tables, -x MyISAM一致性，服务可用性（针对所有库所有表）
--lock-tables, -l 备份前锁表（针对要备份的库）
--single-transaction 适用InnoDB引擎，保证一致性，服务可用性
--master-data=2  表示将二进制日志位置和文件名写入到备份文件并在dump文件中注释掉这一行；
--master-data=1  表示将二进制日志位置和文件名写入到备份文件,在dump文件中不注释这一行；
--master-data参数其他说明：
1）恢复时会执行，默认是1
2）需要RELOAD privilege并必须打开二进制文件
3）这个选项会自动打开--lock-all-tables，关闭--lock-tables
> 
--single-transaction ：保持数据的一致性与完整性
--master-data ：开启使用二进制文件，默认关闭的
开启二进制文件：如果/usr/local/mysql目录下存在my.cnf文件，则直接修改。如果没有my.cnf文件，自己创建一个即可。
    ```
    vim my.cnf
    og-bin = /usr/local/mysql/data/mybinlog
    :wq
    service mysql restart
    ```
> 
案例：对MySQL进行全库备份(开启二进制文件)
mysqldump --all-databases --single-transaction --master-data > /tmp/mysqlbak/all.sql -p

6. 全库级别恢复
    ```
    # 退出MySQL
    # exit
    # 使用mysql命令进行恢复
    # mysql < /tmp/mysqlbak/all.sql -p
    ```

#三. mysqldump+二进制日志实现增量备份

1. 特殊说明
> mysqldump默认情况下不支持增量备份的，其提供了表级、库级、全库级备份
如果想实现增量备份必须结合二进制日志文件

2. 说说什么是增量备份
> 增量备份 = 全量备份 + 后期增加的数据（要备份的核心）=== 差异备份

3. 二进制日志文件（类似日常生活中的日记）
    > 
    1. 错误日志 ：把mysql启动、运行、关闭过程中产生的异常写入到错误日志中$hostname.err
    localhost.localdomain.err
    在my.cnf配置文件中，可以通过如下形式进行定义：
    log-error = /usr/local/mysql/mysql.err
    但是默认情况下，要特别注意：
    第一种情况：如果定义了错误日志，但是没有这个文件，会报错，会导致数据库无法启动。
    第二种情况：mysql.err没有数据写入，原因：权限问题 chown mysql.mysql mysql.err

    2. 一般查询日志（数据查询过程中产生的信息，了解）
    当客户端连接或断开时，服务器会将信息写入该日志，并记录从客户端收到的每一条SQL语句。当您怀疑客     户端的错误并想知道客户端发送给mysqld的确切信息时，一般查询日志可能非常有用。
    默认情况下，一般查询日志是==被禁用==的。 如果要开启，可以使用以下参数：
    general_log[={0|1} 0表示禁用，1表示开启.
    默认情况下，系统会在数据目录下创建以host_name.log命名的一般查询日志。如果要自己指定，可以使用     以下参数：
    指定一般查询日志的路径及文件名
    general_log_file=/path/file.log
    该参数用于定义general log和slow log的输出目标
    log-output=[value,...]
    value=[TABLE|FILE|NONE],默认值为FILE
    TABLE表示将日志记录到表中，general_log表或者slow_log表中
    FILE表示将日志记录的文本文件中
    NONE表示不记录到表或者文件
    注意：
    如果log-output=NONE，则即使启用了日志，也不会写入条目。
    如果log-output不等于NONE,但是没有启用日志也不会写入条目。

    3. 二进制日志（binary log，非常非常重要），
    我们对数据库的任何更改（增加、修改、删除）都会写入到二进制日志文件中。
    在my.cnf配置文件中，可以通过如下形式进行定义：
    log-bin = /usr/local/mysql/data/mybinlog
    一般情况下，二进制文件没有后缀
    还有一点要特别注意：二进制日志没有办法直接通过cat命令查看，使用mysqlbinlog命令进行查看
    
4. 具体增量备份步骤
    > 
    1. 首先要做全量备份
    前提：先开启二进制文件（my.cnf）
    log-bin = /usr/local/mysql/data/mybinlog
    全量备份
    mysqldump --all-databases --single-transaction --flush-logs --master-data=2 > /tmp/mysqlbak/all.sql -p
    问题：如果以上语句报错，mysqldump: Got error: 1105: Unknown error when doing refresh
    解决方案：代表mysql.err没有权限，chown mysql.mysql /usr/local/mysql/mysql.err

    2. 对数据库进行增删改操作
    at 120 按时间
    delete from it_student where id=5;
    insert into it_student values (null,'廖星辰',25,'男','9999.00','yunwei');
    insert into it_student values (null,'上官婉儿',19,'女','6666.00','ui');
    
    3. 对二进制文件进行备份
    show master status
    mysql> show master status;
    +-----------------+----------+--------------+------------------+-------------------+
    | File | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
    +-----------------+----------+--------------+------------------+-------------------+
    | mybinlog.000003 | 980 | | | |
    +-----------------+----------+--------------+------------------+-------------------+
    1 row in set (0.00 sec)
    方法一：备份03这个二进制日志文件
    cp /usr/local/mysql/data/mybinlog.000003 /tmp/mysqlbak/
    方法二：把03这个二进制日志文件导出成sql文件
    mysqlbinlog mybinlog.000003
    从以上命令中我们可以看到备份点:# at 209
    mysqlbinlog --start-position=209 mybinlog.000003 > /tmp/mysqlbak/mybinlog.sql
    
    4. 模拟故障
    drop database myblog;
    use db_itcast;
    drop table it_student;
    
    5. 数据恢复
    第一步：先进行全量恢复
    mysql < /tmp/mysqlbak/all.sql -p
    第二步：恢复增量备份的数据
    方案一：直接source 增量的sql文件
    mysql > source /tmp/mysqlbak/mybinlog.sql
    方案二：恢复二进制文件
    mysqlbinlog --start-position=209 /tmp/mysqlbak/mybinlog.000003 |mysql -p
    Enter password:123
    
    6. 测试验证
    select * from db_itcast.it_student;