# SQL语法

[toc]

# select

## 公共表达式

子查询不能直接引用外部的别名，因为子查询执行时，外部查询上下文不可见。

错误：

```sql
SELECT seller_id
FROM (
    SELECT seller_id, SUM(price) AS total
    FROM Sales 
    GROUP BY seller_id
) AS t
WHERE total = (select max(total) from t);
```

正确：

```sql
WITH TotalSales AS (
    SELECT seller_id, SUM(price) AS total
    FROM Sales 
    GROUP BY seller_id
)
SELECT seller_id
FROM TotalSales
WHERE total = (SELECT MAX(total) FROM TotalSales);
```



## group by

where语句在group by前面

having必须接聚合函数





## partition by

```sql
-- 语法
SELECT column1, column2, ..., 
       WINDOW_FUNCTION() OVER (PARTITION BY column1 ORDER BY column2) AS alias
FROM table_name;

--例子
SELECT
    department_id,
    employee_id,
    salary,
    SUM(salary) OVER (PARTITION BY department_id) AS total_department_salary,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
FROM employees;
```

一个窗口函数需要有一个over



### lag和lead

`LAG()` 函数返回当前行的某一列相对于指定偏移量的前一行的值。如果没有前一行，则返回默认值（可以指定）。

`LEAD()` 函数与 `LAG()` 函数类似，但它返回的是当前行的某一列相对于指定偏移量的后一行的值。

```sql
LAG(column_name, offset, default_value) OVER (PARTITION BY partition_column ORDER BY order_column)

LEAD(column_name, offset, default_value) OVER (PARTITION BY partition_column ORDER BY order_column)

```



举例：[197. 上升的温度](https://leetcode.cn/problems/rising-temperature/)

```sql
select id
from
    (select 
        id,
        temperature,
        recordDate,
        lag(recordDate,1), lag(temperature,1) over(order by recordDate) as last_date
        -- lag(temperature,1) over(order by recordDate) as last_temperature 错误，一个lag对应一个over
    from Weather) a
where temperature > last_temperature and datediff(recordDate, last_date) = 1
```





### 排序

row_number： 依次排序不重复 1 2 3 4 5 6

rank： 可重复 ， 1 1 3 3 3 6

dense_rank： 可重复且不跳过  1 1 2 2 2 3

```sql
SELECT
    id,
    name,
    salary,
    RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```



## limit

```sql
SELECT column1, column2, ...
FROM table_name
LIMIT number;
```

number是要返回的数据

```sql
SELECT *
FROM employees
LIMIT 10 OFFSET 10;

SELECT *
FROM employees
LIMIT 10, 10;
```

从第10行返回10个



## 时间

### 日期和时间的数据类型

1. **DATE**:
   - 格式：`YYYY-MM-DD`
   - 示例：`2024-08-01`
2. **TIME**:
   - 格式：`HH:MM:SS`
   - 示例：`13:45:30`
3. **DATETIME**:
   - 格式：`YYYY-MM-DD HH:MM:SS`
   - 示例：`2024-08-01 13:45:30`
4. **TIMESTAMP**:
   - 格式：`YYYY-MM-DD HH:MM:SS`
   - 自动更新为当前时间戳（当插入或更新行时）
   - 示例：`2024-08-01 13:45:30`
5. **YEAR**:
   - 格式：`YYYY`
   - 示例：`2024`

### 日期和时间的常用函数

1. **CURRENT_DATE()** 或 **CURDATE()**:
   - 返回当前日期
   - 示例：`SELECT CURRENT_DATE();` 返回 `2024-08-01`
2. **CURRENT_TIME()** 或 **CURTIME()**:
   - 返回当前时间
   - 示例：`SELECT CURRENT_TIME();` 返回 `13:45:30`
3. **NOW()**:
   - 返回当前日期和时间
   - 示例：`SELECT NOW();` 返回 `2024-08-01 13:45:30`
4. **DATE()**:
   - 提取日期部分
   - 示例：`SELECT DATE('2024-08-01 13:45:30');` 返回 `2024-08-01`
5. **TIME()**:
   - 提取时间部分
   - 示例：`SELECT TIME('2024-08-01 13:45:30');` 返回 `13:45:30`
6. **YEAR(), MONTH(), DAY(), HOUR(), MINUTE(), SECOND()**:
   - 提取特定部分
   - 示例：`SELECT YEAR('2024-08-01');` 返回 `2024`
   - 示例：`SELECT HOUR('13:45:30');` 返回 `13`
7. **DATE_ADD()**:
   - 添加日期或时间间隔
   - 示例：`SELECT DATE_ADD('2024-08-01', INTERVAL 1 DAY);` 返回 `2024-08-02`
8. **DATE_SUB()**:
   - 减去日期或时间间隔
   - 示例：`SELECT DATE_SUB('2024-08-01', INTERVAL 1 DAY);` 返回 `2024-07-31`
9. **DATEDIFF()**:
   - 计算两个日期之间的天数差
   - 示例：`SELECT DATEDIFF('2024-08-01', '2024-07-31');` 返回 `1`
10. **TIMEDIFF()**:
    - 计算两个时间之间的差
    - 示例：`SELECT TIMEDIFF('13:45:30', '12:30:00');` 返回 `01:15:30`
11. **DATE_FORMAT()**:
    - 格式化日期
    - 示例：`SELECT DATE_FORMAT('2024-08-01', '%W %M %Y');` 返回 `Thursday August 2024`

```
DATE_FORMAT(date, format)
```

- `date`：要格式化的日期或时间。
- `format`：格式字符串。

常见的格式字符串：

- `%Y`：四位年份
- `%m`：两位月份（01-12）
- `%d`：两位日期（01-31）
- `%H`：两位小时（00-23）
- `%i`：两位分钟（00-59）
- `%s`：两位秒（00-59）



## 格式化输出

**ROUND()**: 对数值进行四舍五入

```sql
SELECT ROUND(12345.6789, 2) AS rounded_value; --返回12345.68
```

**CEIL()**、**FLOOR()**



## 存在运算符

### EXISTS 运算符

EXISTS 运算符用于判断查询子句是否有记录，如果有一条或多条记录存在返回 True，否则返回 False。

SQL EXISTS 语法h

```sql
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
```



### IN操作符

```sql
SELECT column1, column2, ...
FROM table_name
WHERE column IN (value1, value2, ...);

SELECT column1, column2, ...
FROM table_name
WHERE column IN (SELECT ... from);
```

相应地，如果后面的是一个值，可以用`=`，例子：

[586. 订单最多的客户](https://leetcode.cn/problems/customer-placing-the-largest-number-of-orders/)

```sql
with temp as (
    select customer_number, count(customer_number) cnt
    from orders
    group by customer_number
)

select customer_number
from temp
where cnt = (
    select max(cnt)
    from temp
);
```



## 选择

case -when

```sql
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    ELSE default_result
END

--例子
SELECT
    employee_name,
    sales_amount,
    CASE
        WHEN sales_amount > 10000 THEN 'High Sales'
        WHEN sales_amount > 5000 THEN 'Medium Sales'
        ELSE 'Low Sales'
    END AS sales_status
FROM sales;

```



if

```sql
IF(condition, true_value, false_value)
```





# 增删改

## create table

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
);

-- 例子
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    birthdate DATE,
    is_active BOOLEAN DEFAULT TRUE
);
```



## insert

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

-- 例子
INSERT INTO users (username, email, birthdate, is_active)
VALUES
    ('test1', 'test1@runoob.com', '1985-07-10', true),
    ('test2', 'test2@runoob.com', '1988-11-25', false),
    ('test3', 'test3@runoob.com', '1993-05-03', true);
```



## update

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;

```



## delete

基础语法：

```sql
--基础语法
DELETE FROM table_name
WHERE condition;

--多表删除
DELETE t1, t2 FROM table1 t1
JOIN table2 t2 ON t1.column = t2.column
WHERE condition;
```

例子： [196. 删除重复的电子邮箱](https://leetcode.cn/problems/delete-duplicate-emails/)



# 常用函数

## 字符类

substring

```sql
SUBSTRING(str, index, cnt);
--从index处选cnt个，index从1开始
```



concat：连接两个字符





# 注意点

- 要判断一个值是不是NULL，要用is 而不是=

- 在 MySQL 中，每个子查询（派生表）必须有一个别名。	

- isnull()可判断是否为null， if null(a, b) 如果a是null的话，返回b
- 返回单值的子查询可以作为表达式