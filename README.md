# MySQL


1. MySQL 安装

1-1）Linux 使用RPM 包安装
    RPM 包，官网下载

    检测是否安装 MySQL 
    rpm -qa | grep mysql

    删除已安装的mysql
    rpm -e mysql
    rpm -e --nodeps mysql 强制删除

    CenTos7 下使用yum 命令安装 MySQL
    官网下载 yum
    wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
    rpm -ivh mysql-community-release-el7-5.noarch.rpm
    yum update
    yum insntall mysql-server

    权限设置
    chown mysql:mysql -R /var/bin/mysql

    初始化MySQL
    mysqld --initialize

    启动MySQL
    systemctl start mysqld

    查看MySQL 运行状态
    systemctl status mysqld

    注意：如果是第一次启动 mysql 服务，mysql 服务器首先会进行初始化的配置
        可以使用 MariaDB 代替，MariaDB 数据库管理系统是 MySQL 的一个分支
        yum install mariadb-server mariadb 

        mariadb数据库的相关命令
        systemctl start mariadb      #启动MariaDB
        systemctl stop mariadb       #停止MariaDB
        systemctl restart mariadb    #重启MariaDB
        systemctl enable mariadb     #设置开机启动


    验证 MySQL 安装
    使用 mysqladmin 工具
    [root@host]# mysqladmin --version


    使用 MySQL Client 执行SQL命令
    [root@host]# mysql
    mysql> SHOW DATABASES;

    Mysql安装后需要做的
    创建root用户的密码
    [root@host]# mysqladmin -u root password "new_password";

    连接到Mysql服务器
    [root@host]# mysql -u root -p

1-2）Windows 安装MySQL
    官网可下载最新版本zip 包，解压到指定目录如 C:\web\mysql-8.0.11
    配置下 MySQL 的配置文件
    打开C:\web\mysql-8.0.11，在该文件夹下创建 my.ini 配置文件，编辑 my.ini 配置
    [mysql]
    # 设置mysql客户端默认字符集
    default-character-set=utf8

    [mysqld]
    # 设置3306端口
    port = 3306

    # 设置mysql的安装目录
    basedir=C:\\web\\mysql-8.0.11

    # 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
    # datadir=C:\\web\\sqldata

    # 允许最大连接数
    max_connections=20

    # 服务端使用的字符集默认为8比特编码的latin1字符集
    character-set-server=utf8

    # 创建新表时将使用的默认存储引擎
    default-storage-engine=INNODB

    接下来启动 MySQL 数据库
    cmd 以管理员身份打开，切换的指定目录
    cd C:\web\mysql-8.0.11\bin

    初始化数据库
    mysqld --initialize --console
    执行完成后，会输出 root 用户的初始默认密码

    安装命令
    mysqld install

    启动
    net start mysql

    注意: 在 5.7 需要初始化 data 目录
        cd C:\web\mysql-8.0.11\bin 
        mysqld --initialize-insecure 
        初始化后再运行 net start mysql 即可启动 mysql

    登录 MySQL(公司)
    mysql -h 主机名 -u 用户名 -p
    
    登陆本机mysql
    mysql -uroot -p


2. MySQL 管理

2-1）Windows 系统下，启动及关闭 MySQL 服务器
    启动
    cd c:/mysql/bin
    mysqld --console

    关闭
    mysqladmin -uroot shutdown


2-2）Linux 系统下
    检查MySQL服务器是否启动
    ps -ef | grep mysqld

    启动
    root@host# cd /usr/bin
    ./mysqld_safe &

    关闭
    ./mysqladmin -u root -p shutdown
    Enter password: ******


2-3）MySQL 用户设置
    添加用户名为guest，密码为guest123
    并授权用户可进行 SELECT, INSERT 和 UPDATE操作权限

    root@host# mysql -u root -p
    Enter password:*******
    mysql> use mysql;
    Database changed

    mysql> INSERT INTO user 
            (host, user, password, 
            select_priv, insert_priv, update_priv) 
            VALUES ('localhost', 'guest', 
            PASSWORD('guest123'), 'Y', 'Y', 'Y');
    
    mysql> FLUSH PRIVILEGES;
    mysql> SELECT host, user, password FROM user WHERE user = 'guest';

    方法二通过SQL的 GRANT 命令，给指定数据库XXX添加用户 zara ，密码为 zara123 

    mysql> GRANT SELECT, INSERT, UPDATE,DELETE,CREATE,DROP
    ->     ON XXX
    ->     TO 'zara'@'localhost'
    ->     IDENTIFIED BY 'zara123';  识别确认

    /etc/my.cnf 文件配置
    可以指定不同的错误日志文件存放的目录


2-4）管理MySQL的命令
    USE 数据库名 
    SHOW DATABASES
    SHOW TABLES
    SHOW COLUMNS FROM 数据表
    SHOW INDEX FROM 数据表
    # Mysql数据库管理系统的性能及统计信息，\G，查询结果按列打印
    SHOW TABLE STATUS LIKE [FROM db_name] [LIKE 'pattern'] \G

3. MySQL 数据类型
    大致分三类：数值、日期/时间和字符串(字符)类型
    数值： int bigint float double(双精度浮点数值) decimal(小数值) 
    时间日期： date time year datetime timestamp
    字符类型： char varchar text blob longlob longtext tinytext



4. 创建数据表
    表名，表字段名， 定义每个表字段
    create table if not exists `python_tb1`(
        `python_id` int unsigned auto_increment,
        `python_title` varchar(100) not null,
        `python_author` varchar(20) not null,
        `submission_date` date,
        primary key(`python_id`)
    )ENGINE=InnoDB default charset=utf8;


    通过命令提示符创建表
    root@host# mysql -u root -p
    Enter password:*******
    mysql> use xxx;
    Database changed
    mysql> CREATE TABLE python_tbl(
    -> python_id INT NOT NULL AUTO_INCREMENT,
    -> python_title VARCHAR(100) NOT NULL,
    -> python_author VARCHAR(40) NOT NULL,
    -> submission_date DATE,
    -> PRIMARY KEY ( python_id )
    -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;

    删表
     mysql> drop table python_tbl

    插入数据（多条）
    如果所有的列都要添加数据，可以不写字段即列名
    Insert into python_tb1(name, sex, age, time)
    value
    ("lee","man", "18", NOW())
    ("zhang","women", "18", NOW());

    查看
    mysql> select * from python_tbl;

    WHERE 子句
    可以使用 and or < > !=  等指定数据条件

    UPDATE 查询
    需要修改或更新 MySQL 中的数据，可以和 where 结合使用
    mysql> update python_tb1 set python_title='PYTHON'
        -> where python_id=5;

    将更新 python_id 为 3 的python_title 字段值的 "C++"替换为 "Python"
    update python_tb1 set python_title= replace(python_title,'C++','Python') where python_id=3;

    将所有人的年龄增加 1:
    update python_tb1 set age=age+1;


    DELETE 语句
    delete from 表名称 where 删除条件
    delete from students where id=3;

    1、delete 和 truncate 仅仅删除表数据，drop 连表数据和表结构一起删除，打个比方，delete 是单杀，truncate 是团灭，drop 是把电脑摔了。
    2、delete 是 DML 语句，操作完以后如果没有不想提交事务还可以回滚，truncate 和 drop 是 DDL 语句，操作完马上生效，不能回滚，打个比方，delete 是发微信说分手，后悔还可以撤回，truncate 和 drop 是直接扇耳光说滚，不能反悔。
    3、执行的速度上，drop>truncate>delete，打个比方，drop 是神舟火箭，truncate 是和谐号动车，delete 是自行车。

    
    LIKE 子句
    ike 匹配/模糊匹配，会与 % 和 _ 结合使用
    '%a'     以a结尾的数据
    'a%'     以a开头的数据
    '%a%'    含有a的数据
    '_a_'    三位且中间字母是a的
    '_a'     两位且结尾字母是a的
    'a_'     两位且开头字母是a的


    UNION 操作符
    连接两个以上的 SELECT 语句的结果组合到一个结果集合中
    个 SELECT 语句会删除重复的数据
    SELECT expression1, expression2, ... expression_n
    FROM tables
    [WHERE conditions]
    UNION [ALL | DISTINCT]
    SELECT expression1, expression2, ... expression_n
    FROM tables
    [WHERE conditions];

    从 "Websites" 和 "apps" 表中选取所有的country（列出重复值）
    select country from Websites
    union all  查询不同值，不使用all
    select country from apps
    order by country;


    排序
    order by (asc/desc)

    GROUP BY 语句
    根据一个或多个列对结果集进行分组,可以使用 COUNT, SUM, AVG等函数
    select name, count(*) from table_1
    group by name;

    WITH ROLLUP 可以实现在分组统计数据基础上再进行相同的统计（SUM,AVG,COUNT…）
    select name, sum(singin) as singin_count from table_1
    group by name with rollup;
    可以使用 coalesce 来设置一个可以取代 NUll 的名称
    select coalesce(a,b,c);
    如果a==null,则选择b；如果b==null,则选择c；如果a!=null,则选择a；如果a b c 都为null 
    
    如果名字为空我们使用总数代替
    select coalesce(name, '总数'), sum(singin) as singin_count from table_1
    group by name with rollup;

    去重：distinct 和 group by 
    分组后的条件使用 HAVING 来限定，WHERE 是对原始数据进行条件限制
    几个关键字的使用顺序为 where 、group by 、having、order by 
    select name, sum(*) from table_1 
    where id=1
    group by name
    having sum(*) > 5
    order by sum(*) desc;



5. Mysql 多表连接的使用
    inner join   获取两表中公有部分，即字段匹配关系的记录
    left join    获取左表所有数据，右表中与之共有部分
    right join   获取右表所有数据，左表中与之公有部分

    select a.id, a.count from table_a 
    inner join table_b 
    on
    a.name=b.name

    等价于
    select a.id, a.count from
    table_a, table_b
    where
    a.name=b.name

    NULL 值处理
    使用 IS NULL  IS NOT NULL  <=>比较操作符处理，返回值True或false
    create table python_t1(
        python_name varchar(40) NOT NUll,
        python_count INT
    );

    select * from python_t1;

    查找python_t1 列是否为NULL
    select * from python_t1 
    where
    python_count IS NULL;
    select * from python_t1 
    where
    python_count IS NOT NULL;


    正则表达式
    ^   $   []  [^...]   p1|p2|p3  *  +  {n,m}
    查找name字段中以'st'为开头的所有数据
    select name from python_t1
    where
    name REGEXP '^st';

    查找name字段中以'k'为结尾的所有数据
    select name from python_t1
    where
    name REGEXP 'k$';    

    查找name字段中包含'ma'字符串的所有数据
    select name from python_t1
    where
    name REGEXP 'ma';    

    查找name字段中以元音字符开头或以'k'字符串结尾的所有数据
    select name from python_t1
    where
    name REGEXP '^[aeiou]|k$';   


6. 事务
    在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务
    事务用来管理 insert,update,delete 语句
    事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行

    事务是必须满足4个条件（ACID）原子性 一致性 隔离性 持久性
    事务控制语句：
    BEGIN或START TRANSACTION；显式地开启一个事务
    COMMIT会提交事务
    ROLLBACK回滚会结束用户的事务，并撤销正在进行的所有未提交的修改
    SAVEPOINT允许在事务中创建一个保存点，一个事务中可以有多个SAVEPOINT
    ROLLBACK TO identifier；把事务回滚到标记点
    SET TRANSACTION；用来设置事务的隔离级别
    InnoDB存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ和SERIALIZABLE

    SET AUTOCOMMIT=0 禁止自动提交
    SET AUTOCOMMIT=1 开启自动提交

    mysql> select * from python_t1;
    mysql> begin;
    mysql> insert into python_t1 value(5, 6);
    mysql> commit;
    mysql> select * from python_t1;
    mysql> begin;
    mysql> insert into python_t1 value(4);
    mysql> rollback;
    mysql> select * from python_t1;


    ALTER 命令
    当我们需要修改数据表名或者修改数据表字段时，使用

    删除，添加或修改表字段
    mysql> alert table python_t1 drop name;
    mysql> alert table python_t1 add age INT FIRST;


    修改字段类型及名称
    mysql> alert table python_t1 modify addr char(10);
    mysql> alert table python_t1 change name age INT;

    ALTER TABLE 对 Null 值和默认值的影响
    alert table python_t1 
    modify name TEXT not null
    dafault 666;

    修改字段默认值
    alert table python_t1 
    alert name set default 777;

    查看数据表类型可以使用 show table status 语句

    修改表名
    alert table python_t1 rename to python;

    删除外键约束：keyName是外键别名
    alert table python_t1 drop forign key keyName;

    修改字段的相对位置
    alert table python_t1
    modify 
    name1 type1 first|after name2;


7.  索引
    索引可以大大提高MySQL的检索速度，索引分单列索引和组合索引
    缺点：
    虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE，占用磁盘空间

7-1）创建索引
    create index indexname 
    on
    table(username(length));
    如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length

    修改表结构(添加索引)
    alert table tableName
    add index indecName(columnName);

    创建表的时候直接指定
    CREATE TABLE mytable(  
        ID INT NOT NULL,   
        username VARCHAR(16) NOT NULL,  
        INDEX [indexName] (username(length))  
    );  

    删除索引
    drop index [indexName] 
    on table;

    创建唯一索引
    create unique index indexname 
    on
    table(username(length));

    修改表结构
    alert table tableName
    add unique [indecName] (columnName);

    创建表的时候直接指定
    create table python(  
        ID INT NOT NULL,   
        username VARCHAR(16) NOT NULL,  
        unique [indexName] (username(length))  
    ); 


    使用ALTER 命令添加和删除索引
    在表中添加索引
    mysql> alert table python add index (name);

    使用 ALTER 命令添加和删除主键
    mysql> alert table python modify name int not null;
    mysql> alert table python add primary key (name);
    mysql> alert table python drop primary key;

    显示索引信息
    show index from python; \G


8. 临时表
    保存一些临时数据时是非常有用的
    临时表只在当前连接可见，当关闭连接时，Mysql会自动删除表并释放所有空间
    mysql> create tempOrary table SalesSummary (
        -> product_name VARCHAR(50) NOT NULL,
        -> total_sales DECIMAL(12,2) NOT NULL DEFAULT 0.00,
        -> avg_unit_price DECIMAL(7,2) NOT NULL DEFAULT 0.00,
        -> total_units_sold INT UNSIGNED NOT NULL DEFAULT 0
    );    

    mysql> INSERT INTO SalesSummary
        -> (product_name, total_sales, avg_unit_price, total_units_sold)
        -> VALUES
        -> ('cucumber', 100.25, 90, 2); 

    mysql> DROP TABLE SalesSummary;
    mysql>  SELECT * FROM SalesSummary;   

    复制数据表
    获取数据表的完整结构
    show create table python \G;

    修改SQL语句的数据表名，并执行SQL语句
    create table `python`(
        `python_id` int(11) NOT NULL auto_incriment,
        `python_title` varchar(100) NOT NULL default '',
        `submission_date` date default NULL,
        PRIMARY KEY (`python_id`),
        UNIQUE　ＫＥＹ `TITLE_INDEX` (`python_title`)
    ) ENGINE=InnoDB;

    拷贝数据表，使用 INSERT INTO... SELECT 语句来实现
    insert into clone_python (python_id,
        python_title,
        submission_date)
    select
    python_id, python_title, submission_date
    from
    python;


    另一种完整复制表的方法:
    create table python_1 like sourcePython;
    复制
    insert into python_1 select * from sourcePython;

    拷贝一个表中的一些字段
    create table newAdmin as (
        select username, passwd from admin
    )

    将新建的表的字段改名
    create table nerAdmin as(
        select id, username as uname, passwd as pd from admin
    )

    拷贝一部分数据
    create table nerAdmin as(
        select * from admin where left(uname,1) = 'e'
    )

    在创建表的同时定义表中的字段信息
    create table nerAdmin (
        id insert into not null auto_inriment primary key
    )
    as
    (
        select * from admin
    )


    第一、只复制表结构到新表
    create table 新表 like 旧表 
    create table 新表 select * from 旧表 where 1=2

    第二、复制表结构及数据到新表
    create table 新表 select * from 旧表 



9. 元数据
    获取查询语句影响的记录数
    在 DBI 脚本中， 语句影响的记录数通过函数 do( ) 或 execute( )返回
    # 方法 1
    # 使用do( ) 执行  $query 
    my $count = $dbh->do ($query);
    # 如果发生错误会输出 0
    printf "%d 条数据被影响\n", (defined ($count) ? $count : 0);

    # 方法 2
    # 使用prepare( ) 及 execute( ) 执行  $query 
    my $sth = $dbh->prepare ($query);
    my $count = $sth->execute ( );
    printf "%d 条数据被影响\n", (defined ($count) ? $count : 0);

    数据库和数据表列表
    # 获取当前数据库中所有可用的表。
    my @tables = $dbh->tables ( );
    foreach $table (@tables ){
    print "表名 $table\n";
    }

    获取服务器元数据
    SELECT VERSION( )	服务器版本信息
    SELECT DATABASE( )	当前数据库名 (或者返回空)
    SELECT USER( )	    当前用户名
    SHOW STATUS	        服务器状态
    SHOW VARIABLES	    服务器配置变量


10. 序列
    MySQL 序列是一组整数，实现其他字段也实现自动增加
    使用 AUTO_INCREMENT
    CREATE TABLE python(id INT UNSIGNED NOT NULL AUTO_INCREMENT,
         PRIMARY KEY (id),
         name VARCHAR(30) NOT NULL,
         date DATE NOT NULL,
         origin VARCHAR(30) NOT NULL)

    INSERT INTO insect (id,name,date,origin) VALUES
    -> (NULL,'housefly','2011-09-11','kitchen'),
    -> (NULL,'millipede','2011-09-11','driveway'),
    -> (NULL,'grasshopper','2011-09-11','front yard');

    SELECT * FROM insect ORDER BY id;

    获取AUTO_INCREMENT值
    在MySQL的客户端使用LAST_INSERT_ID( ) 函数来获取最后插入表中的自增列的值

    重置序列
    可以通过删除自增的列，然后重新添加来实现
    mysql> ALTER TABLE insect DROP id;
    mysql> ALTER TABLE insect
        -> ADD id INT UNSIGNED NOT NULL AUTO_INCREMENT FIRST,
        -> ADD PRIMARY KEY (id);

    设置序列的开始值
    mysql> CREATE TABLE insect
        -> (
        -> id INT UNSIGNED NOT NULL AUTO_INCREMENT,
        -> PRIMARY KEY (id),
        -> name VARCHAR(30) NOT NULL, 
        -> date DATE NOT NULL,
        -> origin VARCHAR(30) NOT NULL
    )engine=innodb auto_increment=100 charset=utf8;

    检查
    mysql> ALTER TABLE t AUTO_INCREMENT = 100;


    使用函数创建自增序列管理表(批量使用自增表,设置初始值,自增幅度)
    第一步：创建Sequence管理表 sequence
    DROP TABLE IF EXISTS sequence; 

    CREATE TABLE sequence ( 
        name VARCHAR(50) NOT NULL, 
        current_value INT NOT NULL, 
        increment INT NOT NULL DEFAULT 1, 
        PRIMARY KEY (name) 
    ) ENGINE=InnoDB;

    第二步：创建取当前值的函数 currval
    DROP FUNCTION IF EXISTS currval; 
    
    DELIMITER $ 
    CREATE FUNCTION currval (seq_name VARCHAR(50)) 
    RETURNS INTEGER
    LANGUAGE SQL 
    DETERMINISTIC 
    CONTAINS SQL 
    SQL SECURITY DEFINER 
    COMMENT ''
    BEGIN
    DECLARE value INTEGER; 
    SET value = 0; 
    SELECT current_value INTO value 
    FROM sequence
    WHERE name = seq_name; 
    RETURN value; 
    END
    $ 
    DELIMITER ;

    第三步：创建取下一个值的函数 nextval
    DROP FUNCTION IF EXISTS nextval; 

    DELIMITER $ 
    CREATE FUNCTION nextval (seq_name VARCHAR(50)) 
    RETURNS INTEGER
    LANGUAGE SQL 
    DETERMINISTIC 
    CONTAINS SQL 
    SQL SECURITY DEFINER 
    COMMENT ''
    BEGIN
    UPDATE sequence
    SET current_value = current_value + increment 
    WHERE name = seq_name; 
    RETURN currval(seq_name); 
    END
    $ 
    DELIMITER;


    第四步：创建更新当前值的函数 setval
    DROP FUNCTION IF EXISTS setval; 

    DELIMITER $ 
    CREATE FUNCTION setval (seq_name VARCHAR(50), value INTEGER) 
    RETURNS INTEGER
    LANGUAGE SQL 
    DETERMINISTIC 
    CONTAINS SQL 
    SQL SECURITY DEFINER 
    COMMENT ''
    BEGIN
    UPDATE sequence
    SET current_value = value 
    WHERE name = seq_name; 
    RETURN currval(seq_name); 
    END
    $ 
    DELIMITER ;

    测试函数功能
    设置需要创建的sequence名称以及设置初始值和获取当前值和下一个值
    INSERT INTO sequence VALUES ('TestSeq', 0, 1);
    ----添加一个sequence名称和初始值，以及自增幅度添加一个名为TestSeq 的自增序列

    SELECT SETVAL('TestSeq', 10);
    ---设置指定sequence的初始值，这里设置TestSeq 的初始值为10

    SELECT CURRVAL('TestSeq');  
    --查询指定sequence的当前值，这里是获取TestSeq当前值

    SELECT NEXTVAL('TestSeq');  
    --查询指定sequence的下一个值  这里是获取TestSeq下一个值


11. 处理重复数据
11-1）防止表中出现重复数据
    在MySQL数据表中设置指定的字段为 PRIMARY KEY（主键）
    或者 UNIQUE（唯一） 索引来保证数据的唯一性
    CREATE TABLE person_tbl
    (
        first_name CHAR(20),
        last_name CHAR(20),
        sex CHAR(10)
    );

    可以设置双主键模式来设置数据的唯一性first_name last_name
    CREATE TABLE person_tbl
    (
    first_name CHAR(20) NOT NULL,
    last_name CHAR(20) NOT NULL,
    sex CHAR(10),
    PRIMARY KEY (last_name, first_name)
    );

    INSERT IGNORE会忽略数据库中已经存在的数据
    如果数据库没有数据，就插入新的数据，如果有数据的话就跳过这条数据
    mysql> INSERT IGNORE INTO person_tbl (last_name, first_name)
        -> VALUES( 'Jay', 'Thomas');
    

    另一种设置数据的唯一性方法是添加一个UNIQUE索引
    CREATE TABLE person_tbl
    (
    first_name CHAR(20) NOT NULL,
    last_name CHAR(20) NOT NULL,
    sex CHAR(10),
    UNIQUE (last_name, first_name)
    );

11-2）统计重复数据
    mysql> SELECT COUNT(*) as repetitions, last_name, first_name
        -> FROM person_tbl
        -> GROUP BY last_name, first_name
        -> HAVING repetitions > 1;


11-3）过滤重复数据
    mysql> SELECT DISTINCT last_name, first_name
        -> FROM person_tbl;

    mysql> SELECT last_name, first_name
        -> FROM person_tbl
        -> GROUP BY (last_name, first_name);


11-4）删除重复数据
    使用临时表，拷贝原数据，再删除原表，将临时表更名为原表
    mysql> CREATE TABLE tmp SELECT last_name, first_name, sex FROM              person_tbl  GROUP BY (last_name, first_name, sex);
    mysql> DROP TABLE person_tbl;
    mysql> ALTER TABLE tmp RENAME TO person_tbl;

    或者 使用添加索引和主键方法，删除重复数据
    mysql> ALTER IGNORE TABLE person_tbl
        -> ADD PRIMARY KEY (last_name, first_name);

12. SQL 注入
    通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令

    输入的用户名必须为字母、数字及下划线的组合
    且用户名长度为 8 到 20 个字符之间
    if (preg_match("/^\w{8,20}$/", $_GET['username'], $matches))
    {
    $result = mysqli_query($conn, "SELECT * FROM users 
                            WHERE username=$matches[0]");
    }
    else 
    {
    echo "username 输入异常";
    }

    // 设定$name 中插入了我们不需要的SQL语句
    $name = "Qadir'; DELETE FROM users;";
    mysqli_query($conn, "SELECT * FROM users WHERE name='{$name}'");


    防止SQL注入，我们需要注意以下几个要点：
    1.永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和 双"-"进行转换等
    2.永远不要使用动态拼装sql，可以使用参数化的sql或者直接使用存储过程进行数据查询存取
    3.永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接
    4.不要把机密信息直接存放，加密或者hash掉密码和敏感的信息
    5.应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装
    6.sql注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用sql注入检测工具jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用MDCSOFT-IPS可以有效的防御SQL注入，XSS攻击等

    在脚本语言，如Perl和PHP你可以对用户输入的数据进行转义从而来防止SQL注入



13. 导出、导入数据
13-1）使用 SELECT ... INTO OUTFILE 语句导出数据
    mysql> SELECT * FROM python_tbl 
        -> INTO OUTFILE '/tmp/pyhton.txt';

    导出 CSV 格式
    mysql> SELECT * FROM passwd INTO OUTFILE '/tmp/pyhton.txt''
        -> FIELDS TERMINATED BY ',' ENCLOSED BY '"'
        -> LINES TERMINATED BY '\r\n';

    生成一个文件，各值用逗号隔开
    SELECT a, b, a+b INTO OUTFILE '/tmp/result.text'
    FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
    LINES TERMINATED BY '\n'
    FROM test_table;


    导出表作为原始数据
    mysqldump 是 mysql 用于转存储数据库的实用程序
    $ mysqldump -u root -p --no-create-info \
                --tab=/tmp xxx python_tbl 
    password ******


    导出 SQL 格式的数据
    $ mysqldump -u root -p xxx python_tbl  > dump.txt

    导出整个数据库的数据
    $ mysqldump -u root -p xxx > database_dump.txt

    需要备份所有数据库
    $ mysqldump -u root -p --all-databases > database_dump.txt

13-2）将数据表及数据库拷贝至其他主机
    在源主机上，将数据备份至 dump.txt 文件中
    $ mysqldump -u root -p database_name table_name > dump.txt

    将备份的数据库导入到MySQL服务器中
    $ mysql -u root -p database_name < dump.txt

    或者将导出数据直接导入到远程服务器上，确保两台服务器是相通，相互访问
    $ mysqldump -u root -p database_name | mysql -h other-host.com database_name

    将指定主机的数据库拷贝到本地
    在 mysqldump 命令中指定远程服务器的IP、端口及数据库名
    mysqldump -h other-host.com -P port -u root -p database_name > dump.txt



13-3）mysql 命令导入
    mysql -u用户名    -p密码    <  要导入的数据库数据(xxx.sql)

    source 命令导入
    source 命令导入数据库需要先登录到数库终端
    mysql> create database abc;      # 创建数据库
    mysql> use abc;                  # 使用已创建的数据库 
    mysql> set names utf8;           # 设置编码
    mysql> source /home/abc/abc.sql  # 导入备份数据库


    使用 LOAD DATA 导入数据
    mysql> LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE mytbl;
    LOCAL 表明从客户主机上按路径读取文，无则在服务器上按路径读取文件

    mysql> LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE mytbl
        -> FIELDS TERMINATED BY ':'
        -> LINES TERMINATED BY '\r\n';

    指定顺序
    mysql> LOAD DATA LOCAL INFILE 'dump.txt' 
        -> INTO TABLE mytbl (b, c, a);


    使用 mysqlimport 导入数据
    mysqlimport客户端提供了LOAD DATA INFILEQL语句的一个命令行接口

    文件 dump.txt 中将数据导入到 mytbl 数据表中
    $ mysqlimport -u root -p --local database_name dump.txt

    指定格式
    $ mysqlimport -u root -p --local --fields-terminated-by=":" \
         --lines-terminated-by="\r\n"  database_name dump.txt

    使用 --columns 选项来设置列的顺序
    $ mysqlimport -u root -p --local --columns=b,c,a \
        database_name dump.txt



14. MySQL 运算符
    算术运算符 + - * / %(取余)
    比较运算符 < > <= >= != between in not in <=> like regexp is not null
    逻辑运算符 and not  or  xor(逻辑异或)
    位运算符   &(按位与 或 非 )  | ^  !  （按位左移）<<   >>   ~ 

    优先级最高 ！ binary  collate


15. MySQL 函数
    MySQL 字符串函数
    MySQL 数字函数 abs(x)  acos(x)  atin(x)  avg(x) ceil()...
    MySQL 日期函数 adddate(d, n)  addtime(t, n) curdate() ..

    MySQL 高级函数
    bin(x)          返回 x 的二进制编码
    binary(x)       将字符串 s 转换为二进制字符串

    CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了

    CASE expression
        WHEN condition1 THEN result1
        WHEN condition2 THEN result2
    ...
        WHEN conditionN THEN resultN
        ELSE result
    END

    select case
    　　when 1 > 0
    　　then '1 > 0'
    　　when 2 > 0
    　　then '2 > 0'
    　　else '3 > 0'
    　　end
    ->1 > 0
    
    cast(x as type)     转换数据类型

    connection_id()     返回服务器的连接数

    current_user()      返回当前用户 user() session_user() system_user()
    database()

    if(expr, v1, v2)    如果表达式 expr 成立，返回结果 v1；否则，返回结果 v2
    SELECT IF(1 > 0,'正确','错误')  

    nullif(expr1, expr2) 如果字符串相等 返回 NULL，否则返回 expr1


    ifnull(v1,v2)       如果 v1 的值不为 NULL，则返回 v1，否则返回 v2

    last_insert_id()    返回最近生成的 AUTO_INCREMENT 值

    coalesce(expr1...expr_n)    返回参数中的第一个非空表达式（从左向右）

