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