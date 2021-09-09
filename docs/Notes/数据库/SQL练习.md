# SQL练习

## 175.组合两个表

```sql
Create table Person (PersonId int, FirstName varchar(255), LastName varchar(255))
Create table Address (AddressId int, PersonId int, City varchar(255), State varchar(255))
Truncate table Person
insert into Person (PersonId, LastName, FirstName) values ('1', 'Wang', 'Allen')
Truncate table Address
insert into Address (AddressId, PersonId, City, State) values ('1', '2', 'New York City', 'New York')
```

```shell
#表1：Person
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
#PersonId 是上表主键
#表2：Address
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
#AddressId 是上表主键
```

需求：编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息： 

```sql
FirstName, LastName, City, State
```

```sql
select FirstName,LastName,City,State 
from Person left join Address 
on Person.PersonId = Address.PersonId;
```

#### 多表连接查询

左连接（left join），结果保留左表全部数据

右连接（right join），结果保留右表的全部数据

内连接（inner join），取两表的公共数据

## 176、第二高的薪水

```sql
Create table If Not Exists Employee (Id int, Salary int)
Truncate table Employee
insert into Employee (Id, Salary) values ('1', '100')
insert into Employee (Id, Salary) values ('2', '200')
insert into Employee (Id, Salary) values ('3', '300')
```

例如上述 `Employee` 表，SQL查询应该返回 `200` 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 `null`。

分析：

limit n 子句表示查询结果返回前 n 条数据

offset n 表示跳过 n 条语句

limit x offset y 表示查询结果跳过 x 条数据，读取前 y 条数据。

ifnull 函数：ifnull(expression_1,expression_2)，如果expression_1 不为null，则ifnull 返回expression_1，否则返回expression_2。

```sql
select ifnull((select distinct Salary
from Employee
order by Salary desc 
limit 1 offset 1), null)  as SecondHighestSalary
```

