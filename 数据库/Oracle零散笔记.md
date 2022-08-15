# varchar2 类型

在Oracle Database中，VARCHAR字段类型，最大值为4000。

当VARCHAR2容纳不下我们需要存储的信息时，就需要使用Oracle的大数据类型LOB（Large Object，大型对象）



# Oracle中的LOB类型

1. 在Oracle中，LOB（Large Object，大型对象）类型的字段现在用的比较多。这种字段的容量大（最多能容纳4GB的数据），且一个表中可以有u东哥这种类型的字段，比较灵活，适用于数量非常大的业务领域（如图像、档案）
2. LOB类型分为BLOB和CLOB两种：
   * **BLOB（Binary Large Object）**即二进制大型对象，适用于存储非文本的**字节流**数据（如程序、图像、影音）
   * **CLOB（Character Large Object**）即字符型大型对象，则与字符集相关，适于存储**文本型**的数据（如历史档案、大部头著作等）。



# Oracle中的distinct使用

distinct和group by是等效的。

distinct关键字后有多个字段，表示后面所有的字段组合不重复。

```sql
select distinct PLACEHOLDER, UI_KEY from UI_CONF_FORM; -- PLACEHOLDER + UI_KEY不重复
select distinct LABLE, UI_KEY from UI_CONF_FORM;
select distinct LABLE, PLACEHOLDER, UI_KEY from UI_CONF_FORM; -- PLACEHOLDER + LABLE + UI_KEY不重复
```

与distinct等效的group by语句。

```sql
select distinct PLACEHOLDER, UI_KEY from UI_CONF_FORM group by PLACEHOLDER, UI_KEY; -- PLACEHOLDER + UI_KEY不重复
```



# Oracle中start with connect by prior用法

`START WITH CONNECT BY PRIOR` 实际上是一个递归查询。

如果表中存在层次数据，则可以使用层次化查询子句，查询出表中行记录之间的**层次关系**。



START WITH 用来标识哪一行作为树形结构的第一行（即根节点，可以指定多个根节点）

CONNECT BY PRIOR 后面是递归条件，指定层次结构中**父节点与子节点之之间的关系**。

```sql
SELECT *
FROM SYS_MENU_PERMISSION
START WITH NAME ='Forecast'
CONNECT BY PRIOR ID=PARENT_ID;
```



以下是查询结果：

PARENT_ID是ID的子节点，NAME = 'Forcast' 是第一行根节点，根据指定的 PARENT_ID = ID 这种层级关系，来递归查询子节点

![image-20210513105620321](Oracle零散笔记.assets/image-20210513105620321.png)



# Left join和Where谁先执行

Left join时，先使用ON的条件生成临时表（left join，无论条件是否为真，都返回左表中的记录），然后再使用WHERE对临时表的内容进行筛选（此时不再有left join的概念）。



# 存储过程

## 代码块结构

XXXX  **IS**

**BEGIN**

处理过程逻辑

**EXCEPTION**

**END**



# 变量定义

**针对存储过程的变量定义，不需要DECLAREA声明**

1. 变量名 表明.字段名%TYPE；  新变量与指定变量类型一致
2. 变量名 变量类型；
3. 变量名 表明%ROWTYPE； 新变量的数据类型与指定表的指定行记录（所有字段）的类型一致



# 变量赋值

1. 对于前两种命名方式可以直接使用 `:=` 赋值，比如 `V_CLO := 1`，也可以在变量声明的时候同时赋值

2. SELECT XXX INTO 变量名

3. execute immediate sql语句字符串 into 变量 

   ![image-20220801110847320](Oracle零散笔记.assets/image-20220801110847320.png)



# Oracle Schema和User（用户）的区别

User（用户）是用来找到Schema的，Schema包含数据库对象，比如表、分区、视图、索引、包、存储过程、函数、触发器、类型、序列和同义等。

![image-20220801162216201](Oracle零散笔记.assets/image-20220801162216201.png)

# Oracle中分区概念

**表空间：**

表空间指的是一个或多个数据文件的集合，所有的数据对象都存放在指定的表空间中，但主要存放的是表， 所以称作表空间。
　　通俗来讲，数据库（一套房子，可以有多个房间），表空间（房间），数据库文件（房间中的书架），表（书架上的书）。



**分区表：**

当表中的数据量不断增大，查询数据的速度就会变慢，应用程序的性能就会下降，这时就应该考虑对表进行分区。表进行分区后，即分区表，逻辑上表仍然是一张完整的表，只是将表中的数据在物理上存放到多个表空间(物理文件上)，这样查询数据时，不至于每次都扫描整张表。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200807094743911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0OTcyODc2,size_16,color_FFFFFF,t_70)



# NVL与NVL2的区别

NVL(E1, E2)，如果E1为空，那么返回E2；E1不为空返回E1

NVL2（E1， E2， E3），如果E1为空，那么返回E3；E1不为空返回E2