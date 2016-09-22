---
title: 常用时间格式化
date: 2016-09-22 22:04:55
tags:
	- C# 基础
	- 易忘
categories:
	- C# 基础
---

## C#中的时间格式化
>d 月中的某一天。一位数的日期没有前导零。
>dd 月中的某一天。一位数的日期有一个前导零。

>M 月份数字。一位数的月份没有前导零。
>MM 月份数字。一位数的月份有一个前导零。

>yyyy 包括纪元的四位数的年份。

>h 12 小时制的小时。一位数的小时数没有前导零。
>hh 12 小时制的小时。一位数的小时数有前导零。
>H 24 小时制的小时。一位数的小时数没有前导零。
>HH 24 小时制的小时。一位数的小时数有前导零。

>m 分钟。一位数的分钟数没有前导零。
>mm 分钟。一位数的分钟数有一个前导零。
 
>s 秒。一位数的秒数没有前导零。
>ss 秒。一位数的秒数有一个前导零。
 
>f 秒的小数精度为一位。其余数字被截断。
>ff 秒的小数精度为两位。其余数字被截断。

```
String.Format("{0:y yy yyy yyyy}", dt);  // "8 08 008 2008"   year
String.Format("{0:M MM MMM MMMM}", dt);  // "3 03 Mar March"  month
String.Format("{0:d dd ddd dddd}", dt);  // "9 09 Sun Sunday" day
String.Format("{0:h hh H HH}",     dt);  // "4 04 16 16"      hour 12/24
String.Format("{0:m mm}",          dt);  // "5 05"            minute
String.Format("{0:s ss}",          dt);  // "7 07"            second
String.Format("{0:f ff fff ffff}", dt);  // "1 12 123 1230"   sec.fraction
String.Format("{0:F FF FFF FFFF}", dt);  // "1 12 123 123"    without zeroes
String.Format("{0:t tt}",          dt);  // "P PM"            A.M. or P.M.
String.Format("{0:z zz zzz}",      dt);  // "-6 -06 -06:00"   time zone
```