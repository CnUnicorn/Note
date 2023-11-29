# 时间日期格式转换

| 序号 | 函数 | 输入类型 | 输出类型 | 说明 |
| ---- | ---- | -------- | -------- | ---- |
|      |      |          |          |      |
|      |      |          |          |      |


```sql
timestamp的默认格式是yyyy-MM
select now(); -- 获取当前时间，timestamp类型。默认是yyyy-MM-dd HH:mm:ss类型
select to_timestamp(bigint);  -- 将输入的整数（unixtime）转换成对应的timestamp。默认日期格式是yyyy-MM-dd HH:mm:ss.SSSSSS
select to_timestamp('2022-11-23 22:45:12.000000', 'yyyy-MM-dd HH:mm:ss.SSSSSS'); -- 将字符类型的时间，转换成timestamp类型时间，第二个参数是字符串格式
select from_timestamp(timestamp, 'yyyy-MM-dd HH:mm:ss.SSSSSS'); -- 将timestamp类型转换成字符串类型，第二个参数是输出字符串格式  

unix时间是距离1970-01-01 00:00:00.0000000的秒数。向前是负，向后是正
select unix_timestamp(); -- 没有参数，返回当前unix时间
select unix_timestamp('2022/03/02 10:10:30.000000', 'yyyy/MM/dd HH:mm:ss.SSSSSS'); -- 将字符串转换成unix时间
select from_unixtime(1, 'yyyy/MM/dd HH:mm:ss.SSSSSS')  -- unixtime转换成指定格式的字符串，如果没有指定字符串格式，默认格式是yyyy-MM-dd HH:mm:ss.SSSSSS

```

