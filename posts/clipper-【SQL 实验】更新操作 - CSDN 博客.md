---
aliases: []
date: 2024-11-11 21:58:52
date created: 2024-11-11 21:58:52 Mon
date modified: 2026-06-14
tag: []
title: 【SQL 实验】更新操作 - CSDN 博客
url: https://blog.csdn.net/Z15922342915/article/details/143597380?spm=1001.2014.3001.5502
---

 **完整代码在文章末尾【代码是自己的解答，并非标准答案，也有可能写错，**文中可能会有不准确或待完善之处，恳请各位读者不吝批评指正，共同促进学习交流**】**

将素材 “[图书管理](https://so.csdn.net/so/search?q=%E5%9B%BE%E4%B9%A6%E7%AE%A1%E7%90%86&spm=1001.2101.3001.7020)” 文件下载到本地，并将其还原到 SQL SERVER 库中，完成以下操作。

个人因为版本问题还原不了，所以导入. xls 文件

导入操作和之前一样

如果不太会，可以去重新看一下

[【SQL 实验】高级查询（三）含附加数据库操作 - CSDN 博客](https://blog.csdn.net/Z15922342915/article/details/143592972 "【SQL实验】高级查询（三）含附加数据库操作-CSDN博客")

![](https://i-blog.csdnimg.cn/direct/139b6bbce8dd42db8c450419f0e26191.png)

![](https://i-blog.csdnimg.cn/direct/75ad4273c74c4b4d81578c4c4bf4f3b9.png)

1.    将财会系读者借书记录存入一个新表 ckjy 中，保存字段为借书证号、姓名、书名、借书日期。

查询会创建新表 `ckjy` 

```
SELECT dz.借书证号,dz.姓名,ts.书名,jy.借书日期
INTO ckjy
FROM dz
JOIN jy ON dz.借书证号 = jy.借书证号
JOIN ts ON jy.总编号 = ts.总编号
WHERE dz.单位 = '财会系';
```

或者

```
SELECT dzjy.借书证号,姓名,书名,借书日期
INTO ckjy
from dz，jy,ts
where dz.借书证号= jy.借书证号
and jy.总编号= ts.总编号
and 单位= '财会系'
```

使用 `CREATE TABLE` 创建了一个名为 `ckjy` 的新表，包含字段 `借书证号`, `姓名`, `书名`, `借书日期`。

通过 `INSERT INTO` 从三个表（`dz`, `jy`, `ts`）中选择符合条件的数据，并插入到 `ckjy` 表中

```
CREATE TABLE ckjy (
    借书证号 CHAR(8),
    姓名 CHAR(8),
    书名 CHAR(8),
    借书日期 CHAR(8)  
);
 
INSERT INTO ckjy (借书证号, 姓名, 书名, 借书日期)
SELECT dz.借书证号, dz.姓名, ts.书名, jy.借书日期
FROM dz
JOIN jy ON dz.借书证号 = jy.借书证号
JOIN ts ON jy.总编号 = ts.总编号
WHERE dz.单位 = '财会系';
```

2.    将记录（'123','沈小霞','大学英语','2009-10-15'),（'125','张自强','[线性代数](https://so.csdn.net/so/search?q=%E7%BA%BF%E6%80%A7%E4%BB%A3%E6%95%B0&spm=1001.2101.3001.7020)','2011-4-15') 插入到 ckjy 表中。

```
INSERT INTO ckjy (借书证号, 姓名, 书名, 借书日期)
VALUES ('123', '沈小霞', '大学英语', '2009-10-15'),
       ('125', '张自强', '线性代数', '2011-4-15');
```

 **3.    根据 ts 表的结构用命令建立一个新表 ts1。**

直接建

![](https://i-blog.csdnimg.cn/direct/c4a12f13057b4c999f338b934688e068.png)

 只要空表的话

```
-- 1. 将 ts 表的数据插入到 ts1 表中
SELECT *
INTO ts1
FROM ts;
 
-- 2. 删除 ts 表中的所有数据
DELETE FROM ts;
```

 创建一个空的新表 `ts1`，你可以使用 `CREATE TABLE` 语句，并从 `ts` 表中选择表结构，而不复制数据

```
SELECT *
INTO ts1
FROM ts
WHERE 1 = 0;
```

4.    将数据库类的图书插入到表 ts1 中，数据库类图书包含书 “数据库” 和“Fox”两个关键字。

```
--4、
INSERT INTO ts1 (总编号, 书名, 作者, 出版单位, 分类号, 单价)
SELECT 总编号, 书名, 作者, 出版单位, 分类号, 单价
FROM ts
WHERE 书名 LIKE '%数据库%' AND 书名 LIKE '%Fox%';
```

 `%` 是通配符，表示匹配任意字符。

5.    将 dz 表中的年龄字段利用出生日期字段计算出并填充到各个记录中。

```
--5
UPDATE dz
SET 年龄 = DATEDIFF(YEAR, 出生日期, GETDATE()) - 
           CASE WHEN MONTH(出生日期) > MONTH(GETDATE()) OR (MONTH(出生日期) = MONTH(GETDATE()) AND DAY(出生日期) > DAY(GETDATE())) THEN 1 ELSE 0 END;
```

6.    将 ts 表中所有科学出版社的图书的价格设置成原来价格的八折。

```
--6
UPDATE ts
SET 单价 = 单价 * 0.8
WHERE 出版单位 = '科学出版社';
```

7.    将所有的高级职称（包含 “教授” 两个字）、姓名为 2 个字的读者 插入到新表 gjdz 表中。

```
--7
CREATE TABLE gjdz (
    借书证号 VARCHAR(20),
    单位 VARCHAR(50),
    姓名 VARCHAR(50),
    性别 VARCHAR(10),
    年龄 INT,
    出生日期 DATE,
    职称 VARCHAR(50),
    地址 VARCHAR(100)
);
 
INSERT INTO gjdz (借书证号, 单位, 姓名, 性别, 年龄, 出生日期, 职称, 地址)
SELECT 借书证号, 单位, 姓名, 性别, 年龄, 出生日期, 职称, 地址
FROM dz
WHERE 职称 LIKE '%教授%' AND LEN(姓名) = 2;
```

8.    删除 dz 表中家住 3 楼或 3 楼以下的读者。

```
--8
DELETE FROM dz
WHERE 地址 LIKE '%3楼%' OR 地址 LIKE '%2层%'OR 地址 LIKE '%1层%';
```

![](https://i-blog.csdnimg.cn/direct/25734965177a4048be66a3f04a29cad3.png)

 

9.    删除借阅表中关于计算机基础的借阅信息。

```
--9
DELETE FROM jy
WHERE 总编号 IN (
    SELECT 总编号
    FROM ts
    WHERE 书名 LIKE '%计算机基础%'
);
```

10.    删除 gjdz 表。

```
--10
DROP TABLE gjdz;
```

 完整代码：

```
SELECT dz.借书证号,dz.姓名,ts.书名,jy.借书日期
INTO ckjy
FROM dz
JOIN jy ON dz.借书证号 = jy.借书证号
JOIN ts ON jy.总编号 = ts.总编号
WHERE dz.单位 = '财会系';
 
--2
INSERT INTO ckjy (借书证号, 姓名, 书名, 借书日期)
VALUES ('123', '沈小霞', '大学英语', '2009-10-15'),
       ('125', '张自强', '线性代数', '2011-4-15');
--3
SELECT *
INTO ts1
FROM ts
WHERE 1 = 0;
 
 
--4、
INSERT INTO ts1 (总编号, 书名, 作者, 出版单位, 分类号, 单价)
SELECT 总编号, 书名, 作者, 出版单位, 分类号, 单价
FROM ts
WHERE 书名 LIKE '%数据库%' AND 书名 LIKE '%Fox%';
--5
UPDATE dz
SET 年龄 = DATEDIFF(YEAR, 出生日期, GETDATE()) - 
           CASE WHEN MONTH(出生日期) > MONTH(GETDATE()) OR (MONTH(出生日期) = MONTH(GETDATE()) AND DAY(出生日期) > DAY(GETDATE())) THEN 1 ELSE 0 END;
--6
UPDATE ts
SET 单价 = 单价 * 0.8
WHERE 出版单位 = '科学出版社';
--7
CREATE TABLE gjdz (
    借书证号 VARCHAR(20),
    单位 VARCHAR(50),
    姓名 VARCHAR(50),
    性别 VARCHAR(10),
    年龄 INT,
    出生日期 DATE,
    职称 VARCHAR(50),
    地址 VARCHAR(100)
);
 
INSERT INTO gjdz (借书证号, 单位, 姓名, 性别, 年龄, 出生日期, 职称, 地址)
SELECT 借书证号, 单位, 姓名, 性别, 年龄, 出生日期, 职称, 地址
FROM dz
WHERE 职称 LIKE '%教授%' AND LEN(姓名) = 2;
--8
--8
DELETE FROM dz
WHERE 地址 LIKE '%3楼%' OR 地址 LIKE '%2层%'OR 地址 LIKE '%1层%';
 
--9
DELETE FROM jy
WHERE 总编号 IN (
    SELECT 总编号
    FROM ts
    WHERE 书名 LIKE '%计算机基础%'
);
--10
DROP TABLE gjdz;
```