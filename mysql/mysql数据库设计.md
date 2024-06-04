```
==> mysql
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -u root
    mysql -u root -p1107
    mysql -u root -p

password: 1107

To start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  /usr/local/opt/mysql/bin/mysqld_safe --datadir\=/usr/local/var/mysql
  
  
sudo chown -R _mysql:mysql /usr/local/var/mysql

sudo mysql.server start
  
```



mysql -u root -p1107



#### sql通用语法

- SQL语句可以单行/多行书写，以分号结束。

- SQL语句中可以增加缩进/空格来增强可读性。

- SQL语句中的关键字不区分大小写。

- SQL语句注释：单行注释（-- 注释），多行注释（/* 注释 */）



#### sql分类

- ddl，Data Definition Language, used to define the database object(tables, rows)
- dml, Data Manipulation Language, used to perform create, delete, update operations on database
- dql, Data Query Language, used to search records in the database
- dcl, Data Control Language, used to create Database users, controlling user permissions, mainly done by the Database administrator



## 数据库操作入门

```sql
show database
select database() # 查看使用的数据库名字
use 'database' # 使用注定的数据库
drop database [if exists] 'database'
create database [if not exists] 'database'
```

上面的database关键字可以换成schema



sql语句我们其实是尽量不想在terminal操作的，只是会特别繁琐，所以得使用图形工具，比如dbeaver或者intellij

![image-20240526111800012](/Users/jasonjin/Library/Application Support/typora-user-images/image-20240526111800012.png)



### 常见约束与表创建

```sql
create table tb_user (
    id integer primary key auto_increment comment "id",
    username varchar(20) not null unique comment "uesrname",
    name varchar(20) not null comment "name",
    age int not null comment "the age",
    gender char(1) default 'm' comment "the gender"
)
```



| 约束                         | 描述                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| not null                     | making a column not accepting null values                    |
| unique                       | making sure all values in the column are unique              |
| primary key [auto_increment] | making a row uniquely identifiable, has to been non null and unique |
| default                      | defauly values for a column                                  |
| foreign key                  | ???                                                          |







### SQL数据类型

#### 数值

| 分类      | 类型    | 大小(byte)                                            | 有符号(SIGNED)范围                                         | 无符号(UNSIGNED)范围 | 描述                                               | 备注 |
| --------- | ------- | ----------------------------------------------------- | ---------------------------------------------------------- | -------------------- | -------------------------------------------------- | ---- |
| 数值类型  | tinyint | 1                                                     | (-128，127)                                                | (0，255)             | 小整数值                                           |      |
| smallint  | 2       | (-32768，32767)                                       | (0，65535)                                                 | 大整数值             |                                                    |      |
| mediumint | 3       | (-8388608，8388607)                                   | (0，16777215)                                              | 大整数值             |                                                    |      |
| int       | 4       | (-2147483648，2147483647)                             | (0，4294967295)                                            | 大整数值             |                                                    |      |
| bigint    | 8       | (-2^63，2^63-1)                                       | (0，2^64-1)                                                | 极大整数值           |                                                    |      |
| float     | 4       | (-3.402823466 E+38，3.402823466351  E+38)             | 0 和 (1.175494351  E-38，3.402823466 E+38)                 | 单精度浮点数值       | float(5,2)：5表示整个数字长度，2  表示小数位个数   |      |
| double    | 8       | (-1.7976931348623157 E+308，1.7976931348623157 E+308) | 0 和  (2.2250738585072014 E-308，1.7976931348623157 E+308) | 双精度浮点数值       | double(5,2)：5表示整个数字长度，2  表示小数位个数  |      |
| decimal   |         |                                                       |                                                            | 小数值(精度更高)     | decimal(5,2)：5表示整个数字长度，2  表示小数位个数 |      |

像是age这种属性，我们可以用**age tinyint unsigned**

而score这种属性，可以用score double(4, 1)



#### 字符串

| 分类       | 类型                  | 大小                         | 描述 |
| ---------- | --------------------- | ---------------------------- | ---- |
| char       | 0 - 255 bytes         | 定长字符串                   |      |
| varchar    | 0-65535 bytes         | 变长字符串                   |      |
| tinyblob   | 0-255 bytes           | 不超过255个字符的二进制数据  |      |
| tinytext   | 0-255 bytes           | 短文本字符串                 |      |
| blob       | 0-65 535 bytes        | 二进制形式的长文本数据       |      |
| text       | 0-65 535 bytes        | 长文本数据                   |      |
| mediumblob | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据 |      |
| mediumtext | 0-16 777 215 bytes    | 中等长度文本数据             |      |
| longblob   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据     |      |
| longtext   | 0-4 294 967 295 bytes | 极大文本数据                 |      |

- char是固定长度，假设是char(10)，那么就相当于一个10个位置的数组，存储“abc”的时候后面的7个空位都会被空字符填充，**缺点是浪费空间，用空间换时间**

- varchar是动态长度，对于varchar(10)，存储“abc”的话，只占用3个位置， **缺点是需要运算，用时间换空间**



日期的话主要用**date**和**datetime**







### Insert, Update, Delete

```sql
指定字段添加数据：insert into 表名 (字段名1, 字段名2)  values (值1, 值2);
全部字段添加数据：insert into 表名 values (值1, 值2, ...);
批量添加数据（指定字段）：insert into 表名 (字段名1, 字段名2)  values (值1, 值2), (值1, 值2);
批量添加数据（全部字段）：insert into 表名 values (值1, 值2, ...), (值1, 值2, ...);

修改数据：update  表名  set  字段名1 = 值1 , 字段名2 = 值2 , .... [ where  条件 ] ;

删除数据：delete  from  表名  [ where  条件 ];
```

where不写的话其实就是对整个表生效

