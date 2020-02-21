# MySql

### where

#### 简单运算符

`< `, ` >`, `<>`,  ` =`, `!=`, ` >=`, ` <=` , `<=>` 

#### 逻辑运算符

`and`, `or`, `not`

#### 模糊查询

`like`, `between and`, `in`, `is null`,  `is not null`

### like

通配符: 

1. % 任意多个字符, >=0

2.  _  一个字符

3.  \ 转义字符

4.  ESCAPE    

    ```sql
    select * from abc where last_name like '_$_%' escape '$'; # 表明$后面一个为普通字符, 即x_xxx...
    ```



### 函数

`concat('a', 'b', 'c')`    -> abc 字符串拼接

`ifnull(source, 'output')`  如果为null则输出output

`isnull(source)`  null?1:0

`curdate()`  日期,不包含时间

`curtime()`  时间, 不包含日期

`year(now())`, `month(now())`, `day(now())`

