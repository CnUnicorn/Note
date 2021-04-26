# varchar2 类型

在Oracle Database中，VARCHAR字段类型，最大值为4000。

当VARCHAR2容纳不下我们需要存储的信息时，就需要使用Oracle的大数据类型LOB（Large Object，大型对象）



# Oracle中的LOB类型

1. 在Oracle中，LOB（Large Object，大型对象）类型的字段现在用的比较多。这种字段的容量大（最多能容纳4GB的数据），且一个表中可以有u东哥这种类型的字段，比较灵活，适用于数量非常大的业务领域（如图像、档案）
2. LOB类型分为BLOB和CLOB两种：
   * **BLOB（Binary Large Object）**即二进制大型对象，适用于存储非文本的**字节流**数据（如程序、图像、影音）
   * **CLOB（Character Large Object**）即字符型大型对象，则与字符集相关，适于存储**文本型**的数据（如历史档案、大部头著作等）。