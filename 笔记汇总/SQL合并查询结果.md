# SQL合并查询结果

***

## 概述

* `UNION`操作符用于合并多个`SELECT语句`的结果集
* `UNION`内部的`SELECT语句`必须拥有相似的列（列名可以不一样，但列数、列类型、列顺序需一样）

## UNION和UNION ALL

* `UNION`操作符用于合并多个`SELECT语句`的结果集，但不重复

  ```sql
  --UNION语法
  SELECT column_names(s) FROM table_name1
  UNION
  SELECT column_names(s) FROM table_name2
  ```

* `UNION ALL`操作符用于合并多个`SELECT语句`的结果集，但允许重复

  ```sql
  --UNION ALL语法
  SELECT column_names(s) FROM table_name1
  UNION ALL
  SELECT column_names(s) FROM table_name2
  ```

* 举例：

  现有表`Wuhan_Employees`和`Beijing_Employees`如下

  表`Wuhan_Employees`

  | E_ID | E_Name |
  | ---- | ------ |
  | 01   | Tim    |
  | 02   | Mike   |
  | 03   | Grace  |

  表`Beijing_Employees`

  | E_ID | E_Name |
  | ---- | ------ |
  | 01   | Bill   |
  | 02   | Tim    |
  | 03   | Mike   |

  现在想要查询武汉和北京所有的不同的雇员

  ```sql
  SELECT E_Name FROM Wuhan_Employees
  UNION
  SELECT E_Name FROM Beijing_Employees
  ```

  查询结果如下

  | E_Name |
  | ------ |
  | Tim    |
  | Mike   |
  | Grace  |
  | Bill   |

  现在想要查询武汉和北京所有的雇员

  ```sql
  SELECT E_Name FROM Wuhan_Employees
  UNION ALL
  SELECT E_Name FROM Beijing_Employees
  ```

  查询结果如下

  | E_Name |
  | ------ |
  | Tim    |
  | Mike   |
  | Grace  |
  | Bill   |
| Tim    |
  | Mike   |
  
  