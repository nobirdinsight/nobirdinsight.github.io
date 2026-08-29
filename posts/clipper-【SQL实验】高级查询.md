---
date: 2024-11-11 22:02:25
date created: 2024-11-11 22:02:25 Mon
date modified: 2026-08-29
title: 【SQL 实验】高级查询（三）含附加数据库操作 - CSDN 博客
url: https://blog.csdn.net/Z15922342915/article/details/143592972?spm=1001.2014.3001.5502
---

 **完整代码在文章末尾【代码是自己的解答，并非标准答案，也有可能写错，**文中可能会有不准确或待完善之处，恳请各位读者不吝批评指正，共同促进学习交流**】**

将素材中的 “学生管理” 数据库附加到 [SQL](https://so.csdn.net/so/search?q=SQL&spm=1001.2101.3001.7020) SERVER 中，完成以下操作：                              

## 附加**数据库**操作：

打开 [SSMS](https://so.csdn.net/so/search?q=SSMS&spm=1001.2101.3001.7020)，在 对象资源管理器中，右击数据库，选择附加

![](https://i-blog.csdnimg.cn/direct/30cdf3e693b74dafbd9e464ec1e93fcc.png)

在弹出的 附加数据库窗口中，点击 `Add`（添加）按钮

![](https://i-blog.csdnimg.cn/direct/8a9b742a1fe44259a3aad0a06af14c86.png)

浏览到存放[数据库文件](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E5%BA%93%E6%96%87%E4%BB%B6&spm=1001.2101.3001.7020)（.mdf 和 .ldf 文件）的位置。

![](https://i-blog.csdnimg.cn/direct/afd68d481ded44bf9e34a0d253de8423.png)

选择数据库的 `.mdf` 文件（主数据文件），然后点击 确定

![](https://i-blog.csdnimg.cn/direct/bb8cd808c1ee48d18947ebd3641f1223.png)

在弹出的窗口中，你会看到数据库的相关信息（如 `.mdf` 文件的路径、日志文件等）。确认信息正确。如果数据库日志文件（.ldf）也没有出现，系统会自动识别并列出。确认无误后，点击 `OK` 进行附加。

![](https://i-blog.csdnimg.cn/direct/37c17bc35f6e47ca871bf85b2cbb2e7f.png)

数据库附加完成

![](https://i-blog.csdnimg.cn/direct/9c0d3771e5b84695bdf39fb28ed993fd.png)

个人因为版本问题附加还原不了，所以导入学生管理. xls 文件

## 导入学生管理. xls 文件操作：

右键点击 “数据库” 节点，选择“[新建数据库](https://so.csdn.net/so/search?q=%E6%96%B0%E5%BB%BA%E6%95%B0%E6%8D%AE%E5%BA%93&spm=1001.2101.3001.7020 "新建数据库")”。

在弹出的窗口中，设置数据库名称为 “学生管理”，点击“确定” 完成创建

![](https://i-blog.csdnimg.cn/direct/2df2e0cd9c38486c981101ac42eba89d.png)

浏览选择文件作为数据库文件路径

![](https://i-blog.csdnimg.cn/direct/a52d8c13db9e4201abd2016104a042c4.png)

![](https://i-blog.csdnimg.cn/direct/89c6a1c7481d4528959b991092dbf06e.png)

![](https://i-blog.csdnimg.cn/direct/7aa2e8a981ad4841a9da9c316e4dc816.png)

![](https://i-blog.csdnimg.cn/direct/b2f1bdd48ecb49469a7ba3829b455218.png)

![](https://i-blog.csdnimg.cn/direct/3d0fce3dffc84920bd70bef6a31f4869.png)

![](https://i-blog.csdnimg.cn/direct/0fe1a4a6eede4a72b667ebf8e086a8b5.png)

![](https://i-blog.csdnimg.cn/direct/1b99dc4939544546a1e74a6bdd93f533.png)

导入过程不知道出来什么问题【这个我不知道】，但不要担心，这个步骤是没问题的。

把有问题的删除 or 重命名

![](https://i-blog.csdnimg.cn/direct/bd312e189dc747f8a9077334683c2172.png)

##  题目：

1. 查询大于入学分数平均值的男生的人数

![](https://i-blog.csdnimg.cn/direct/da10bd5c61da4b8ca8c8db88c675fef8.png)

虽然有标红但不影响运行

```
--1.查询大于入学分数平均值的男生的人数。
SELECT COUNT(*) AS 男生人数
FROM 学生信息
WHERE 性别 = '男'
  AND 入学分数 > (SELECT AVG(入学分数) FROM 学生信息)
```

2. 查询最受学生欢迎（选课人数最多）的课程号、课程名。

![](https://i-blog.csdnimg.cn/direct/ee063ffdaf554a3db77f58e445e6d1b5.png)

外部查询使用 `WHERE` 条件，筛选出与内部子查询结果相同的课程号，并返回该课程的号和名称

```
SELECT 课程号,课程名
FROM 课程信息
WHERE 课程信息.课程号 = (
    SELECT TOP 1 学生成绩.课程号
    FROM 学生成绩 
    GROUP BY 学生成绩.课程号
    ORDER BY COUNT(学生成绩.学号) DESC
)
```

考虑存在并列的情况：

![](https://i-blog.csdnimg.cn/direct/263d620728ac45e292f6a2510d22c509.png)

`RANK() OVER (ORDER BY COUNT(*) DESC)` 根据人数降序排列，并生成排名。

`WHERE ranking = 1` 过滤出排名第一的课程，即人数最多的课程

3. 查询学生考的最好的那门课程情况，显示学号、课程号、成绩（相关子查询）。

![](https://i-blog.csdnimg.cn/direct/7ca53696f73743a7bb0799dcf702aee5.png)

外部查询通过 `WHERE a.成绩 = …` 匹配最高成绩的记录并返回该课程的学号、课程号和成绩信息。

```
SELECT 学号, 课程号, 成绩
FROM 学生成绩 a
WHERE a.成绩 = (SELECT MAX(b.成绩) FROM 学生成绩 b)
```

4. 查询 “王岩” 同学具有相同籍贯的同学，显示姓名，籍贯（使用 exists）。

![](https://i-blog.csdnimg.cn/direct/bbd3baed65564d25b7964f530f22d863.png)

`EXISTS` 子查询检查条件是否存在

`s1.姓名 <> '王岩'`：排除 “王岩” 本人，只显示其他同学

```
SELECT s1.姓名, s1.籍贯
FROM 学生信息 AS s1
WHERE EXISTS (
    SELECT *
    FROM 学生信息 AS s2
    WHERE s1.籍贯 = s2.籍贯 
      AND s2.姓名 = '王岩'
      AND s1.姓名 <> '王岩'
)
```

5. 查询选修了'1001'号课程的学生姓名（使用 exists）。

![](https://i-blog.csdnimg.cn/direct/ff7acaee2426494596cb8e340a5aa7c9.png)

`WHERE 学生信息.学号 = 学生成绩.学号`：将 `学生信息` 和 `学生成绩` 表关联，以确定哪些学生选修了该课程

```
SELECT 姓名
FROM 学生信息 
WHERE EXISTS (
    SELECT *
    FROM 学生成绩
    WHERE 学生信息.学号 = 学生成绩.学号
      AND 学生成绩.课程号 = 1001
)
```

 完整代码：

```
--1.查询大于入学分数平均值的男生的人数。
SELECT COUNT(*) AS 男生人数
FROM 学生信息
WHERE 性别 = '男'
  AND 入学分数 > (SELECT AVG(入学分数) FROM 学生信息)
 
--2.查询最受学生欢迎（选课人数最多）的课程号、课程名
SELECT 课程号,课程名
FROM 课程信息
WHERE 课程信息.课程号=(SELECT TOP 1 学生成绩.课程号
						FROM 学生成绩 
						GROUP BY 学生成绩.课程号
						ORDER BY COUNT(学生成绩.学号) DESC)
 
--3.查询学生考的最好的那门课程情况，显示学号、课程号、成绩（相关子查询）。
SELECT  学号, 课程号, 成绩
FROM 学生成绩 a
WHERE a.成绩 = (SELECT MAX(b.成绩) FROM 学生成绩 b)
 
--4.查询“王岩”同学具有相同籍贯的同学，显示姓名，籍贯（使用exists）。
SELECT s1.姓名, s1.籍贯
FROM 学生信息 AS s1
WHERE EXISTS (
    SELECT *
    FROM 学生信息 AS s2
    WHERE s1.籍贯 = s2.籍贯 
		AND s2.姓名 = '王岩'
		AND s1.姓名 <> '王岩'
)
 
--5.查询选修了'1001'号课程的学生姓名（使用exists）。
SELECT 姓名
FROM 学生信息 
WHERE EXISTS (
    SELECT *
    FROM 学生成绩
    WHERE 学生信息.学号 = 学生成绩.学号
      AND 学生成绩.课程号 = 1001
)
```