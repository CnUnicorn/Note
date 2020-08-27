[toc]

#  Java方法笔记

#### 根据下标取出String中的某个字符：

```java
String a = "abc";
char b = a.charAt(1);
```



***

####  数字型字符串(String)与int类型相互转换

* **int转成String有三种方式**

```java
String s = String.valueOf(10);
String s = Integer.toString(10); //这条比较好记，个人感觉
String s = 10 + ""; //数字直接加上空字符串，自动转换成字符串，如：打印中字符串和数值直接相加
```

* **String转成int**

```java
int i = Integer.parseInt("120"); //同上面。这条比较好记
int i = String.valueOf(str).intValue("100");
```



***

#### 数字型字符(char)转换成int

直接用改字符减去'0',字符型变量实际上是AscII码，char之间做运算操作=对它们的AscII码操作

```java
int i = '10' - '0';
// i = 10
```

***

#### Scanner.nextInt()`方法

`Scanner.nextInt()`方法不会跳过换行，所以控制台多个输入如果有换行，需要使用`Scanner.nextLine()`来跳过换行。



***

#### 特殊的取余

整数1对其他数取余均为0，因为所有整数都能够被1整除,作为特殊情况需要考虑进去。`n % 1 = 0`

所有整数对0取余，结果均为0。`0 % n = 0`



***

#### 格式化输出

```java
double d = 3.1415926;
System.out.println(String.format("%.2f",d)); //格式化输出保留小数点后两位
```



***

#### A ? B : C 表达式

如果表达式A为真，那么返回表达式B的结果，反之返回表达式C的结果。



#### 对象内容比较，equals( )方法

对象之间比较内容是否相等，一定要用对象重写的equals()方法，不能用==比较。

只有基本的数据类型可以用==比较值。

对对象使用==，实际上是比较对象指针变量指向的地址是否相等。

特殊的，String实际上是不可变的对象，而不是基本数据类型。

只有当String变量用这种形式初始化时，才可以用==比较值。因为这种时候，生成的字符串放在常量池中，当之后任何String使用这种方法进行初始化时，都会指向常量池中的同一个字符串。所以这个时候用==比较，能够返回true，因为指针指向的地址都是相同的。

其他函数返回或者new对象生成的String都不是在常量池中的，和普通的对象相同。

```java
String a = "123";
String b = "123";
a == b; //true

String c = new String("123");
String d = new String("123");
c == d; //false,指向的地址不同,实际上在堆内存中是两个不同的地址，但是值相同
c.equals(d); //true
```

