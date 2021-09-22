[toc]



# sqlServer

sqlServer中每个**库**都有系统信息表（**SysObjects**），系统信息表存放该数据库内创建的所有对象，如约束、默认值、日志、规则、存储过程等，每个对象在表中占一行。

**查询SysObjects表的语法比较特殊，需要两个点：**

`select * from [database]..SysObjects where xtype = 'U'; --查询所有用户表`

SysObjects的表结构：

最常用的字段是name和xtype（表明对象类型）

| **列名**          | **数据类型** | **描述**                                                     |
| ----------------- | ------------ | ------------------------------------------------------------ |
| **name**          | sysname      | 对象名,常用列                                                |
| id                | int          | 对象标识号                                                   |
| **xtype**         | char(2)      | **对象类型**。常用列。**xtype**可以是下列对象类型中的一种：  C = CHECK 约束　　D = 默认值或 DEFAULT 约束　　F = FOREIGN KEY 约束　　L = 日志　　FN = 标量函数  IF = 内嵌表函数 　P = 存储过程 　PK = PRIMARY KEY 约束（类型是 K） 　RF = 复制筛选存储过程   S = 系统表 　TF = 表函数 　TR = 触发器 　**U = 用户表** 　UQ = UNIQUE 约束（类型是 K）  V = 视图 　X = 扩展存储过程 |
| uid               | smallint     | 所有者用户对象编号                                           |
| info              | smallint     | 保留。仅限内部使用                                           |
| status            | int          | 保留。仅限内部使用                                           |
| base_schema_ ver  | int          | 保留。仅限内部使用                                           |
| replinfo          | int          | 保留。供复制使用                                             |
| parent_obj        | int          | 父对象的对象标识号（例如，对于触发器或约束，该标识号为表 ID）。 |
| crdate            | datetime     | 对象的创建日期。                                             |
| ftcatid           | smallint     | 为全文索引注册的所有用户表的全文目录标识符，对于没有注册的所有用户表则为 0 |
| schema_ver        | int          | 版本号，该版本号在每次表的架构更改时都增加。                 |
| stats_schema_ ver | int          | 保留。仅限内部使用。                                         |
| type              | char(2)      | 对象类型。可以是下列值之一：   C = CHECK 约束  D = 默认值或 DEFAULT 约束 F = FOREIGN KEY 约束   FN = 标量函数 IF = 内嵌表函数  K = PRIMARY KEY 或 UNIQUE 约束   L = 日志 P = 存储过程 R = 规则  RF = 复制筛选存储过程  S = 系统表  TF = 表函数 TR = 触发器 U = 用户表 V = 视图 X = 扩展存储过程 |
| userstat          | smallint     | 保留。                                                       |
| sysstat           | smallint     | 内部状态信息                                                 |
| indexdel          | smallint     | 保留                                                         |
| refdate           | datetime     | 留用                                                         |
| version           | int          | 保留                                                         |
| deltrig           | int          | 保留                                                         |
| instrig           | int          | 保留                                                         |
| updtrig           | int          | 保留                                                         |
| seltrig           | int          | 保留                                                         |
| category          | int          | 用于发布、约束和标识                                         |
| cache             | smallint     | 保留                                                         |



# Oracle



## 字符串拼接

用 `||` 符号拼接字符串，`:=` 对声明的变量赋值 

```sql
DECLARE 
	str VARCHAR2(50)
str := 'SELECT * FROM tbl_name where id = ' || i.id || ';' 
```





## 自增序列

oracle没有办法让字段值自增，只能通过新建一个自增序列的方式，来给对应的字段值赋值

**从10开始，自增为1的自增序列**

```sql
create sequence employees_seq
minvalue 1
maxvalue 9999999999
start with 10
increment by 1
cache 20;
```



## 通过序列和触发器实现自增ID

oracle 12 才有 identity 实现自增ID，低版本用序列和触发器实现插入数据时自增ID。

```sql
-- 创建自增1的序列
CREATE SEQUENCE TC_CFG_REQ_SEQ
MINVALUE 1
MAXVALUE 99999999999999999999
START WITH 1
INCREMENT BY 1
nocache;

-- 创建插入前触发器
-- 这个触发器在手动设置OID的时候不会触发，OID会用手动设置的值，
-- 这样可能会导致OID唯一性约束出现问题
-- 比如，序列自增到6，但是下一条插入数据，手动设置OID为10
-- 接下来的插入值都用序列自增，当序列自增到10的时候，就会出现唯一性错误
-- 所以这里要去掉WHEN子句，即使手动设置了OID的值，仍然是采用序列的值
CREATE OR REPLACE TRIGGER TRG_ON_REQ_OID BEFORE INSERT ON dcomp.TC_CFG_CPMS_REQ
FOR EACH ROW -- WHEN (NEW.REQ_OID IS NULL)
BEGIN
	SELECT TC_CFG_REQ_SEQ.NEXTVAL INTO :NEW.REQ_OID FROM DUAL;
END;
```



## 通过触发器实现，更新数据时，自动更新时间

将更新时间字段的默认值设置成SYSDATE，结合更新时触发器，就可以自动修改更新时间。

```sql
CREATE OR REPLACE TRIGGER TRG_ON_REQ_UPD_DATE BEFORE UPDATE ON DCOPM.TC_CFG_CPMS_REQ
FOR EACH ROW
BEGIN
	:NEW.LAST_UPD_DATE := SYSDATE;
END;
```



## merge语法

这条数据如果在数据库中，那么更新它；如果不在，插入这条数据

```sql
MERGE INTO DCOMP.TC_CFG_CPMS_REQ_req req
USING (SELECT 22 AS REQ_OID FROM DUAL) t
ON (req.REQ_OID = t.REQ_OID)
WHEN MATCHED THEN 
	UPDATE SET req.SAVE_TYPE = 1
WHEN NOT MATCHED THEN
	INSERT (REQ_NAME, REQ_DESC)values('test_merge', 'test of merge');
```



## inner join, left join, right join

* inner join

  查出联结的两张表都有的字段

* left join

  `From tb1 LEFT JOIN tb2`

  返回包括**左表中的所有记录**和**右表中联结字段**有关的组合记录。如果左表中数据多于右表，查询结果中右表的数据为null。

* right join

  `FROM tb1 RIGHT JOIN tb2`

  返回包括**右表中的所有记录**和**左表中联结字段**相等的组合记录。如果右表中数据多于左表，查询结果中左表的数据为null。

