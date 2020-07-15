# SQL联合查询

***

## 概述

* 有时为了得到完整的查询结果，我们需要从多个表中获取结果。这时就需要执行`JOIN`
* 根据多表之间数据的交叉提取需求，有不同的`JOIN`方式
  * `INNER JOIN`：返回条件匹配的行
  * `LEFT JOIN`：返回左表所有行，即使右表没有匹配的行
  * `RIGHT JOIN`：返回右表所有行，即使左表没有匹配的行
  * `FULL JOIN`：返回左右表所有的行，即使没有匹配的行

## INNER JOIN

* `INNER JOIN`和`JOIN`是等价的，在表中存在至少一个匹配时，`INNER JOIN`返回行

  ```sql
  --INNER JOIN语法
  SELECT column_name(s)
  FROM table_name1
  INNER JOIN table_name2
  ON table_name1.colume_name=table_name2.column_name 	--使用and可添加更多匹配条件
  ```

* 举例：

  现有表`Persons`和`Orders`如下

  `Persons`表

  | Id_P | LastName | FirstName | Address  | City     |
  | ---- | -------- | --------- | -------- | -------- |
  | 1    | Adams    | John      | A Street | Wuhan    |
  | 2    | Bush     | George    | B Street | Beijing  |
  | 3    | Carter   | Thomas    | C Street | Shanghai |

  `Orders`表

  | Id_O | OrderNo | Id_P |
  | ---- | ------- | ---- |
  | 1    | 77895   | 3    |
  | 2    | 44678   | 3    |
  | 3    | 22456   | 1    |
  | 4    | 24562   | 1    |
  | 5    | 34764   | 65   |

  现在，我们希望列出所有人的订单

  ```sql
  SELECT Persons.LastName, Orders.OrderNo
  FROM Persons
  INNER JOIN Orders
  ON Persons.Id_P=Orders.Id_P 	--只有匹配该条件的行才会返回
  ORDER BY Persons.LastName
  ```

  查询结果如下：

  | LastName | OrderNo |
  | -------- | ------- |
  | Adams    | 22456   |
| Adams    | 24562   |
  | Carter   | 77895   |
  | Carter   | 44678   |
  

## LEFT JOIN

* `LEFT JOIN`会从左表(`table_name1`)里返回所有行，即使右表(`table_name2`)没有匹配的行

  ```sql
  --LEFT JOIN语法
  SELECT column_name(s)
  FROM table_name1
  LEFT JOIN table_name2
  ON table_name1.colume_name=table_name2.column_name	--使用and可添加更多匹配条件
  ```

* 举例：

  现有表`Persons`和`Orders`如下

  `Persons`表

  | Id_P | LastName | FirstName | Address  | City     |
  | ---- | -------- | --------- | -------- | -------- |
  | 1    | Adams    | John      | A Street | Wuhan    |
  | 2    | Bush     | George    | B Street | Beijing  |
  | 3    | Carter   | Thomas    | C Street | Shanghai |

  `Orders`表

  | Id_O | OrderNo | Id_P |
  | ---- | ------- | ---- |
  | 1    | 77895   | 3    |
  | 2    | 44678   | 3    |
  | 3    | 22456   | 1    |
  | 4    | 24562   | 1    |
  | 5    | 34764   | 65   |
  
  现在，我们希望列出所有人，以及他们的订单（如果有的话）
  
  ```sql
  SELECT Persons.LastName, Orders.OrderNo
  FROM Persons
  LEFT JOIN Orders
  ON Persons.Id_P=Orders.Id_P 	--从左表返回所有行，即使右表中没有匹配的行
  ORDER BY Persons.LastName
  ```
  
  查询结果如下：
  
  | LastName | OrderNo |
  | -------- | ------- |
  | Adams    | 22456   |
  | Adams    | 24562   |
  | Carter   | 77895   |
  | Carter   | 44678   |
  | Bush     |         |
  

## RIGHT JOIN

* `RIGHT JOIN`会从右表（`table_name2`）返回所有的行，即使在左表（`table_name1`）没有匹配的行

  ```sql
  --RIGHT JOIN语法
  SELECT column_name(s)
  FROM table_name1
  RIGHT JOIN table_name2
  ON table_name1.colume_name=table_name2.column_name	--使用and可添加更多匹配条件
  ```

* 举例：

  现有表`Persons`和`Orders`如下

  `Persons`表

  | Id_P | LastName | FirstName | Address  | City     |
  | ---- | -------- | --------- | -------- | -------- |
  | 1    | Adams    | John      | A Street | Wuhan    |
  | 2    | Bush     | George    | B Street | Beijing  |
  | 3    | Carter   | Thomas    | C Street | Shanghai |

  `Orders`表

  | Id_O | OrderNo | Id_P |
  | ---- | ------- | ---- |
  | 1    | 77895   | 3    |
  | 2    | 44678   | 3    |
  | 3    | 22456   | 1    |
  | 4    | 24562   | 1    |
  | 5    | 34764   | 65   |

  现在，我们希望列出所有的订单，以及他们的订购人（如果有的话）
  
  ```sql
  SELECT Persons.LastName, Orders.OrderNo
  FROM Persons
  RIGHT JOIN Orders
  ON Persons.Id_P=Orders.Id_P 	--从右表返回所有行，即使左表中没有匹配的行
  ORDER BY Persons.LastName
  ```
  
  查询结果为：
  
  | LastName | OrderNo |
  | -------- | ------- |
  | Adams    | 22456   |
  | Adams    | 24562   |
  | Carter   | 77895   |
  | Carter   | 44678   |
  |          | 34764   |

## FULL JOIN

* 只要其中某个表存在匹配，`FULL JOIN`就会返回行

  ```sql
  --FULL JOIN语法
  SELECT column_name(s)
  FROM table_name1
  FULL JOIN table_name2
  ON table_name1.colume_name=table_name2.column_name	--使用and可添加更多匹配条件
  ```

* 举例：

  现有表`Persons`和`Orders`如下

  `Persons`表

  | Id_P | LastName | FirstName | Address  | City     |
  | ---- | -------- | --------- | -------- | -------- |
  | 1    | Adams    | John      | A Street | Wuhan    |
  | 2    | Bush     | George    | B Street | Beijing  |
  | 3    | Carter   | Thomas    | C Street | Shanghai |

  `Orders`表

  | Id_O | OrderNo | Id_P |
  | ---- | ------- | ---- |
  | 1    | 77895   | 3    |
  | 2    | 44678   | 3    |
  | 3    | 22456   | 1    |
  | 4    | 24562   | 1    |
  | 5    | 34764   | 65   |
  
  现在我们希望列出所有人和所有订单，以及他们之间的关系（如果有的话）。
  
  ```sql
  SELECT Persons.LastName, Orders.OrderNo
  FROM Persons
  FULL JOIN Orders
  ON Persons.Id_P=Orders.Id_P 	
  ORDER BY Persons.LastName
  ```
  
  查询结果为：
  
  | LastName | OrderNo |
  | -------- | ------- |
  | Adams    | 22456   |
  | Adams    | 24562   |
  | Carter   | 77895   |
  | Carter   | 44678   |
  | Bush     |         |
  |          | 34764   |
  
  




