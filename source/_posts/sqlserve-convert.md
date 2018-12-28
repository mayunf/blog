---
title: sql server 中将datetime类型转换为date
date: 2018-12-28 10:12:44
tags:
---

## 常用转换

### datetime 转换为 date
```
convert(varchar(10),getdate(),120)
```

### datetime 转换为 time
```
convert(varchar(12),getdate(),108)
```

## convert 用法

### 定义和用法

CONVERT() 函数是把日期转换为新数据类型的通用函数。

CONVERT() 函数可以用不同的格式显示日期/时间数据。

### 语法

> CONVERT(data_type(length),data_to_be_converted,style)

data_type(length) 规定目标数据类型（带有可选的长度）。data_to_be_converted 含有需要转换的值。style 规定日期/时间的输出格式。

可以使用的 style 值：

| StyleId | Style 格式 |
| --- | --- |
| 100 或者 0 | mon dd yyyy hh:miAM （或者 PM） |
| 101 | 	mm/dd/yy |
| 102 | yy.mm.dd |
| 103 | dd/mm/yy |
| 104 | dd.mm.yy |
| 105 | dd-mm-yy |
| 106 | dd mon yy |
| 107 | Mon dd, yy |
| 108 | hh:mm:ss |
| 109 或者 9 | mon dd yyyy hh:mi:ss:mmmAM（或者 PM） |
| 110 | mm-dd-yy |
| 111 | yy/mm/dd |
| 112 | yymmdd |
| 113 或者 13 | dd mon yyyy hh:mm:ss:mmm(24h) |
| 114 | hh:mi:ss:mmm(24h) |
| 120 或者 20 | yyyy-mm-dd hh:mi:ss(24h) |
| 121 或者 21 | yyyy-mm-dd hh:mi:ss.mmm(24h) |
| 126 | yyyy-mm-ddThh:mm:ss.mmm（没有空格） |
| 130 | dd mon yyyy hh:mi:ss:mmmAM |
| 131 | dd/mm/yy hh:mi:ss:mmmAM |


参考： [SQL Server CONVERT() 函数](http://www.w3school.com.cn/sql/func_convert.asp)