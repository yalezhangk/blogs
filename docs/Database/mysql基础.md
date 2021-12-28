# Mysql基础

# 一、基本命令行操作

## 1 基本连接操作

### 1.1 Python操作连接Mysql数据库格式：mysql://账号:密码@地址:端口/库名

#### mysql://root:mysql@127.0.0.1:3306/yalezhang_test

**Mysql 库名不能带中划线，yalezhang-test不可**

#### 1.2 表增加字段

```mysql
duty表增加俩字段（不为空，有默认值tf, 加索引）
ALTER TABLE duty ADD COLUMN depart_name VARCHAR(64) NOT NULL DEFAULT 'UnKnow';
ALTER TABLE duty ADD INDEX depart_name (depart_name);
ALTER TABLE duty ADD COLUMN role_name VARCHAR(64) NOT NULL DEFAULT 'UnKnow';
ALTER TABLE duty ADD INDEX role_name (role_name);
desc duty;
show index from duty;
```

#### 1.3 反斜杠\ 转义问题
**\在SQL = 语句和like语句中，分别用2个\ 和4个\ 替换，方可生效**。

```shell
# SQL库中存在的数据：央视影音事业群\央视影音开发运营中心
# 实际SQL操作：select * from duty where depart_name = '央视影音事业群\\央视影音开发运营中心';
# like语句：select * from duty where depart_name like '%央视影音事业群\\\\央视影音开发运营中心%';
```

1. **mysql -uroot -p密码 -P端口 -hIP -D数据库名  -》连接数据库**
1. mysql -u root -p mysql 连接数据库
2. select version(); 查看版本
3. show databases; 查看所有库
4. use 库名; 使用数据库
5. **select database(); 查看当前使用的库名**
6. create database 数据库名 charset=utf8; 创建数据库
7. drop database 库名; 删除库
8. **show tables; 查看所有表**
9. desc 表名; 查看表结构
10. create table 表名 (字段介绍); 创建表
11. alter table 表名 add 列名 类型; 修改表，新增字段
12. alter table 表名 drop 列名; 删除字段
13. drop table 表名; 删除

#### 1.4 建库、表、插数据

```mysql
create database db_yalezhang charset=utf8;  # 建库db_yalezhang
SET NAMES utf8;  # 设置编码格式
SET FOREIGN_KEY_CHECKS=0;  # 去除外键关系检查(表操作完后记得修改过来)
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `sid` int(11) NOT NULL AUTO_INCREMENT,
  `gender` char(1) NOT NULL,
  `class_id` int(11) NOT NULL,
  `sname` varchar(32) NOT NULL,
  PRIMARY KEY (`sid`),
  KEY `fk_class` (`class_id`),
  CONSTRAINT `fk_class` FOREIGN KEY (`class_id`) REFERENCES `class` (`cid`)
) ENGINE=InnoDB AUTO_INCREMENT=17 DEFAULT CHARSET=utf8;
BEGIN;  # 插入数据
INSERT INTO `student` VALUES ('1', '男', '1', '理解'), ('2', '女', '1', '钢蛋');
COMMIT;
SET FOREIGN_KEY_CHECKS=0;  # 改过来外键依赖
# 查询笔记
if(isnull(number),0,ty)  # 字段为空时
```

# 二、CURD操作

### 2.1 增加
1. insert into students(name,hometown,birthday) values('黄蓉','桃花岛','2016-3-2');

### 2.2 修改
1. update 表名 set 列1=值1,列2=值2... where 条件;
2. **批量更新数据：update table set 更新字段 = case 依据字段 when 条件 then 更新值 end where 条件；**

```mysql
# 更新duty表：若role:四线值班 》 则role_name:LEVEL4
UPDATE duty
SET role_name = CASE role
    WHEN '四线值班' THEN 'LEVEL4'
	WHEN '三线值班' THEN 'LEVEL3'
	WHEN '2.5线值班' THEN 'LEVEL2.5'
	WHEN '二线值班' THEN 'LEVEL2'
	WHEN '一线值班' THEN 'LEVEL1'
	WHEN '页面值班' THEN 'LEVEY'
END
WHERE role IN ('四线值班','三线值班','2.5线值班','二线值班','一线值班','页面值班') AND duty_time > '2019-01-01';
```

### 2.3 删除
1. delete from 表名 where 条件
2. **完整的select语句**

```mysql
select distinct *
from 表名
where ....
group by ... having ...
order by ...
limit start,count
```

### 2.4 查询
1. select * from 表名; 查询所有列
2. select id,name from 表名; 查询指定列
3. **as 别名**的使用
4. select id as 别名 from 表名; 起别名
5. **distinct 消除重复的行**
6. select distinct name from author;
7. **where 条件查询**
8. select * from students where name != '黄蓉';
9. select * from students where name like '黄%'; 查询姓黄的学生
10. **范围查询 in表示存在的，between ... and ...表示在连续的范围内**
11. select * from students where id in (1,3,8); 查询id是1或3或8的学生
12. select * from students where id between 3 and 8; 查询id在3~8之间的学生
13. **select * from table1 order by id desc limit 10;查询倒数后10条数据**
14. **select * from `clog` order by rand() limit 10; 随机获取10条数据**
#### 完整的查询

```mysql
select distinct *
from 表名
where ....
group by ... having ...
order by ...
limit start,count
```

##### 1.1 排序 order by
1. desc 降序；asc 升序，默认升序
1. select * from students
where gender=1 and isdelete=0
order by id desc; 查询未删除男生信息，按学号降序

##### 1.2 聚合函数
1. **count(*或列名) 统计数量**
2. max(列名) 此列最大值
3. min(列名) 此列最小值
4. sum(列名) 此列求和
5. avg(列名) 求此列平均值
6. **聚合函数不可用于where语句中，可用于having语句中**

##### 1.3 分组 group by
1. select age as 年龄,count(*) as 数量 from students group by age; 查询各种年龄的人数
2. **where**是对from后面指定的表进行数据筛选，属于对原始数据的筛选，
**having是对group by的结果进行筛选**
3. **【HAVING与WHERE的区别】**：
4. **where在分组之前过滤数据，where条件中不能包含聚组函数。**
5. **having在分组后过滤数据，条件中经常包含聚组函数**

```mysql
1. where 子句的作用：在对查询结果进行分组前，把不符合where条件的行去掉，即在分组之前过滤数据。另外，where条件中不能包含聚组函数。
2. having 子句的作用：筛选满足条件的组，即在分组后过滤数据，条件中经常包含聚组函数，使用 having 条件过滤出特定的组
```


```mysql
# 查询：平均成绩大于60分的同学的学号和平均成绩；
# 思路：根据学生分组，使用avg获取平均值，通过having对avg进行筛选
select student_id,avg(num) from score group by student_id having avg(num) > 60;
```
##### 1.4 分页 limit start,count
1. select * from students where gender=1 limit 0,3; 查询前3行男生信息
2. **从start索引开始，获取count条数据。**
**start索引从0开始，而id是从1开始的**
3. **所以，获取第10-12条数据，应该是limit9,3**

```mysql
例1，假设数据库表student存在13条数据。
代码示例:
语句1：select * from student limit 9,4
语句2：slect * from student limit 4 offset 9
// 语句1和2均返回表student的第10、11、12、13行 
//语句2中的4表示返回4行，9表示从表的第十行开始
```

##### 1.5 连接查询
1. **select * from 表1
inner或left或right join 表2 on 表1.列=表2.列**
2. select * from students inner join pythons on students.cls_id = pythons.id; 查询班级表与学生表

##### 1.6 自关联
1. 例如省、市、县表，结构类似，且自上到下是一对多，可以定义一张表来存储；
2. **自关联，表中的某一列，关联了这个表中的另外一列，但是它们的业务逻
辑含义是不一样的，城市信息的pid引用的是省信息的id**

```mysql
定义表areas，结构如下
id
atitle
pid
# 因为省没有所属的省份，所以可以填写为null
# 城市所属的省份pid，填写省所对应的编号id
自关联，表中的某一列，关联了这个表中的另外一列，但是它们的业务逻
辑含义是不一样的，城市信息的pid引用的是省信息的id
# 查询省的名称为“河南省”的所有城市
select city.* from areas as city
inner join areas as province on city.pid=province.id
where province.atitle='河南省';
```

##### 1.7 子查询
1. 在一个 select 语句中,嵌套了另外一个select 语句, 那么被嵌套的select 语句称之
为子查询语句

##### 1.8 模糊查询
- **like**
- **%表示任意多个任意字符**
- **_表示一个任意字符**

1. select * from students where name like '黄_'; 查询姓黄并且名字是一个字的学生。

# 二 数据库设计及导出/导入数据
1. **遵循三范式**
2. 第一范式：**列的原子性，列不可再拆分**
3. 第二范式：**必须有一个主键，非主属性（列）依赖主键**
4. 第三范式：**非主属性（列）不依赖于其他非主属性**
2. 当一个表中的列太多时，并且某些列不经常出现在结果中，**可以对列中的列进行拆分**，分为两个表，一对一。
3. **导出：mysqldump, 导出数据及表结构：mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql**
4. **只导出表结构：mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql**
5. **导入数据：mysql -u用户名 -p密码 数据库名 < 数据库名.sql**
6. 导入导出数据

```shell
# 导出数据到sql文件
mysqldump -uroot -p密码 数据库名 > sql文件
# 导入数据
mysql -uroot -p密码 -P端口 -hIP -D数据库名 < sql文件
```

# 三 主键、外键
**mysql中处理字符串时，默认第一个字符下标为1**

## 1 主键
1. 主键（primary key）一列（或一组列），其值能够唯一区分表中的每个行。
**唯一标识表中每行的这个列（或这组列）称为主键**。
2. 没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只涉及相关的行
3. **Innodb建议使用与业务无关的自增ID作为主键**

## 2 外键
1. 可以使得两张表关联，保证数据的一致性和实现一些级联操作
2. 防止无效信息的插入，外键约束:对数据的有效性进行验证
3. 在实际开发中,很少会使用到外键约束,会极大的降低表更新的效率

## 3 是否使用外键
###### 外键是否采用看业务应用场景，以及开发成本
1. 互联网行业，用户量大，并发量高，不推荐使用外键，把数据一致性的控制放到事务中
2. 传统行业，人数可控，活跃人数有限，使用外键，降低服务器性能，降低开发成本
#### 4 外键的性能问题
1. 数据库需要维护外键的内部管理
2. 外键等于把数据的一致性事务实现，全部交给数据库服务器完成
3. 有了外键，当做一些涉及外键字段的增，删，更新操作之后，需要触发相关操作去检查，而不得不消耗资源
4. 外键还会因为需要请求对其他表内部加锁而容易出现死锁情况

# 四、mysql配置文件
1. mysql命令默认加载的配置文件：**~/.my.cnf、 /etc/mysql/my.cnf、/etc/my.cnf**

```shell
# /root/.my.cnf
[client]
host = 192.168.210.68
port = 16030
user = root
password = ouFo3bof53J1hqoTvrtnFMp7PP1DwCHM0hVGFjW1
```

# 五、SQL连接(JOIN)查询

## 1. 概述
1. SQL join 用于把来自两个或多个表的**行结合起来**，基于这些表之间的共同字段。
2. 展示LEFT JOIN、RIGHT JOIN、INNER JOIN、OUTER JOIN 相关的 7 种用法。
![image](https://note.youdao.com/yws/res/27933/0F0C5B66D31E449785CD07E9AAF9E35D)
- INNER JOIN（内连接，或等值连接）：获取两个表中字段匹配关系的记录
- LEFT JOIN（左连接）：获取左表中的所有记录，右表中没有的为None
- RIGHT JOIN（右连接）：获取右表中的所有记录，左表中没有的为None
- FULL JOIN：关键字只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行.
