# SQL数据库笔记

## 概念说明

### 主键

指能够通过某个字段唯一区分出不同的记录，这个字段被称为主键。

选取主键的一个基本原则是：不使用任何业务相关的字段作为主键。

作为主键最好是完全业务无关的字段，我们一般把这个字段命名为id。常见的可作为id字段的类型有：

1. 自增整数类型：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；

2. 全局唯一GUID类型：使用一种全局唯一的字符串作为主键，类似8f55d96b-8acc-4636-8cb8-76bf8abc2f57。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。

### 外键

在students表中，通过class_id的字段，可以把数据与另一张表关联起来，这种列称为外键。

外键约束

## 操作说明

<img src="..\data\SQL\SQL websites表 示例.png" style="zoom:80%;" />

<center> SQL websites表 示例  </center>

### 1. SELECT  

从数据库中提取数据

实例：SELECT name,country FROM Websites;

### 2. UPDATE 

更新数据库中的数据

实例：UPDATE Websites ;

### 3. DELETE 

DELETE 语句用于删除表中的行

实例：DELETE FROM Websites WHERE name='Facebook' AND country='USA';

### 4.  INSERT INTO - 向数据库中插入新数据

向表中插入新记录












CREATE DATABASE - 创建新数据库

ALTER DATABASE - 修改数据库

CREATE TABLE - 创建新表

ALTER TABLE - 变更（改变）数据库表

DROP TABLE - 删除表

CREATE INDEX - 创建索引（搜索键）

DROP INDEX - 删除索引