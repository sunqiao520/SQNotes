# SQL练习

##### SQL基本指令

###### 基础查询指令

> SELECT 查询列表 from 表名

查询单个字段、多个字段和所有字段

```sql
SELECT Last_name FROM employees;
SELECT Last_name,salary,email FROM employees;
SELECT * FROM employees;
```

起别名，将查询的字段以别名的方式呈现

```sql
#方式一
SELECT 100%98 AS 结果;
SELECT last_name AS 姓, first_name AS 名 FROM employees;
#方式二
SELECT last_name 姓, first_name 名 FROM employees;
#案例：查询salary，显示结果为out put 其中out为关键字
SELECT salary AS "out put" FROM employees;
```

去重

```sql
#案例：查询员工表中涉及到的所有的部门编号
SELECT DISTINCT department_id FROM employees;
```

###### 条件查询指令

```java
/*
语法： 
     select
           查询列表
     from  
           表名
     where 
           筛选条件;
  分类：
      一、按条件表达式筛选
      条件运算符：> < = !=  <> <= >=
      二、按逻辑表达式筛选
      逻辑运算符：&& || !
                  and or not
      三、模糊查询
               like
               between and
               in
               is null
 */

# 一、按条件表达式筛选

#案例1：查询工资>12000的员工信息
SELECT
      *
FROM
      employees
WHERE 
      salary>12000;
      
#案例2：查询部门编号不等于 90的员工名和部门编号
SELECT last_name, department_id 
FROM employees 
WHERE department_id <>90;

# 二、按逻辑表达式筛选

#案例1：查询工资在10000到20000之间的员工名、工资以及奖金
SELECT last_name,salary,commission_pct		
FROM employees;
WHERE salary>=10000 AND salary<=20000;

#三、模糊查询
#1.like 一般和通配符搭配使用 %任意多个字符，包含0个  _ 任意一个字符
#案例1：查询员工名中包含字符a的员工信息
SELECT *
FROM employees
WHERE last_name LIKE '%a%';

# 案例2：查询员工名中第三个字符为e，第五个字符为a的员工名

SELECT last_name,salary
FROM employees
WHERE last_name LIKE '__e_a%';

#案例3：查询员工名中第二个字符为_的员工名
SELECT last_name
FROM employees
WHERE last_name LIKE '_\_%';

#2.between and 包含临界值，先小后大

#案例1：查询员工编号在100到120之间的员工信息
SELECT *
FROM employees
WHERE employee_id BETWEEN 100 AND 120;

#3. in 
#案例：查询员工的公种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT last_name, job_id
FROM employees
WHERE job_id IN ('IT_PROG','AD_VP','AD_PRES');

#4. is null   =与<>不能判断null
#案例1：查询没有奖金的员工名和奖金率
SELECT last_name,commission_pct
FROM employees
WHERE commission_pct IS NULL;


#安全等于 <=>
#案例1：查询没有奖金的员工名和奖金率
SELECT last_name,commission_pct
FROM employees
WHERE commission_pct <=> NULL;

#案例12：查询工资为12000的员工信息
SELECT
      *
FROM
      employees
WHERE 
      salary <=> 12000;
```

###### 排序查询

```sql
/*
select 查询列表
from   表
   【where 筛选条件】
order by 排序列表 【asc|desc】
          asc升序，desc降序，默认升序

*/

#案:1：查询员工信息，要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC;

#案例2：查询部门编号>=90的员工信息，按入职时间先后排序
SELECT * FROM employees
WHERE department_id >=90
ORDER BY hiredate ASC;

#案例3：按年薪的高低显示员工的信息和年薪【按表达式排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0))  年薪
FROM employees
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;

#案例4：按年薪的高低显示员工的信息和年薪【按别名排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0))  年薪
FROM employees
ORDER BY 年薪 DESC;

#案例5：按姓名的长度显示员工的姓名和工资【按函数排序】
SELECT LENGTH(last_name) 字节长度,last_name,salary
FROM employees
ORDER BY LENGTH(last_name) DESC;

#案例6：查询员工信息，要求先按员工工资排序，再按员工编号排序【多字段排序】
SELECT *
FROM employees
ORDER BY salary ASC, employee_id DESC;

```

###### 单行函数练习

```sql
#进阶4：常见函数
/*
分类：
	1、单行函数
	concat、length、ifnull等
	2、分组函数
	做统计使用，又称为统计函数
*/

#一、字符函数

#1. length  获取参数值得字节个数
SELECT LENGTH('john');

#2. concat 拼接字符串
SELECT CONCAT(last_name,'_',first_name) FROM employees;

#3. upper、lower
SELECT UPPER('john');
SELECT LOWER('joHN');
#将姓大写，名小写
SELECT CONCAT(UPPER(last_name),'_',LOWER(first_name)) FROM employees;

#4. substr、substring 索引从1开始
#截取从指定索引处后面所有字符
SELECT SUBSTR('jdshgshfgh',7) out_put;

#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('jsdfghsdhgdf',2,4);

#5. instr
#返回子串第一次出现的索引，如果找不到返回0
SELECT INSTR('nasdhfv','sdh') out_put;

#6. trim 去掉前后的指定字符
SELECT TRIM('     safd');
SELECT TRIM('a' FROM 'aaaasdfgfdgaaaaa') put;

#7.lpad 用指定的字符实现左填充指定长度
SELECT LPAD('fsdgd',10,'*') AS out_put;

#8.rpad 用指定的字符实现右填充指定长度
SELECT RPAD('fs',10,'*') AS out_put;

#9.repalce 替换
SELECT REPLACE('sdsdsdsd','s','a') put;


# 二、数学函数
#1.round 四舍五入
SELECT ROUND(1.65);
SELECT ROUND(1.56723,2);

#2. cell 向上取整,返回>=该参数的最小整数
SELECT CEIL(1.23);

#3. floor 向下取整，返回<=该参数的最大整数
SELECT FLOOR(-9.99);

#truncate 截断
SELECT TRUNCATE(1.6999,1);

#mod 取余 mod(a,b):a-a/b*b
SELECT MOD(10,3);
SELECT 10%3;

#三、日期函数
#now 返回当前系统日期加时间
SELECT NOW();

#curdate 返回当前系统日期。不包含时间
SELECT CURDATE();

#curtime 返回当前时间，不包含日期
SELECT CURTIME();

#str_to_date 将字符通过指定格式装换为日期
SELECT STR_TO_DATE('1998-3-21','%Y-%c-%d') AS out_put;

#查询入职日期为1992--4-3的员工信息
SELECT * FROM employees WHERE hiredate='1992-4-3';

SELECT * FROM employees WHERE hiredate=STR_TO_DATE('4-3-1992','%c-%d-%Y');

#data_formate 将日期转换成字符
SELECT DATE_FORMAT(NOW(),'%y年%m月%d日') AS out_put;

#四、其他函数
SELECT VERSION();
SELECT DATABASE();
SELECT USER();

#五、流程控制函数
#1.if 函数：if else的效果
SELECT IF(10>5,'da','xiao');

SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金，呵呵','有奖金，嘻嘻') AS out_put
FROM employees;

#2.case函数的使用一：switch case 的效果
/*
case 要判断的字段或表达式
when 常量一 then 语句1;
when 常量二 then 语句2;
...
else 要显示的值n或语句n;
end
*/

/*
查询员工的工资，要求
部门号=30，显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示原工资

*/

SELECT salary 原始工资, department_id,
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END AS 新工资
FROM employees;

#3.case函数的使用二：类似于多重if
/*
case
when 条件1 then 语句1
when 条件2 then 语句2
...
else 语句n
end
*/

SELECT salary,
CASE
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'c'
ELSE 'D'
END AS 工资级别
FROM employees;

```

###### 分组函数

```sql
# 分组函数
/*
功能：用作统计使用，又称为聚合或统计函数
分类：
sum 求和、avg 平均值、max 最大值、min最小值、count计算个数
其中sum avg用于数值型
max min count支持所有类型

都会忽略null再运算
*/

# 1、简单使用
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;

# 2、参数类型支持哪些类型
SELECT SUM(last_name),AVG(last_name) FROM employees;

SELECT MAX(last_name),MIN(last_name) FROM employees;

SELECT MAX(hiredate),MIN(hiredate) FROM employees;

# 3、 和distinct搭配
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

# 4、count函数的详细介绍
SELECT COUNT(salary) FROM employees;
SELECT COUNT(*) FROM employees;

SELECT COUNT(1) FROM employees;


# 5、和分组函数一同查询的字段有限制，要求是group by后的字段

SELECT DATEDIFF(NOW(),'1998-4-17');

```

###### 分组查询

```sql
# 进阶5：分组查询
/*
语法：	
	select 分组函数，列（要求出现在group by的后面）
	from 表
	【where 筛选条件】
	group by 分组的列表
	【order by 子句】
注意：查询列表必须特殊，要求是分组函数和group by 后出现的字段

特点：
	1、分组查询中的筛选条件分为两类
			数据源		位置			关键字
	分组前筛选	原始表		group by子句的前面	where
	分组后筛选	分组后的结果表	group by子句的后面	having
	
*/
#引入：查询每个部门的平均工资
SELECT AVG(salary) FROM employees;

#简单查询
#案例1：查询每个工种的最高工资
SELECT MAX(salary), job_id
FROM employees
GROUP BY job_id;

#案例2：查询每个位置上的部门个数
SELECT COUNT(*),location_id
FROM departments
GROUP BY location_id;

#添加筛选条件
#案例1：查询邮箱中包含a字符的，每个部门的平均工资

SELECT AVG(salary),department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;


```

##### 