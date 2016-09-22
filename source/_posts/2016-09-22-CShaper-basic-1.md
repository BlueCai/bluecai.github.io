---
title: readonly、static、const
date: 2016-09-22 21:56:24
tags:
	- C# 基础
	- 易忘
categories:
	- C# 基础
---
## readonly、static、const 的区别

### const
>const修饰的字段表示是常量，其本质上在编译完成，执行之前其字段的真实值已经被写在了DLL中了。只有在声明的时候才能改变其值

### readonly
>readonly表示只读的，其只有在声明的时候和构造函数中能被修改。*通过编译后的代码可以看到，其实质是在构造函数中赋值的*

### static
> static静态的，在一个地方修改，对所有的对象都是有效的。其使用类名.字段名来访问的。


### 注意
1. static readonly 对所有的对象都是有效的，只读的。其使用对象.字段名来访问。注意：和readonly不同的是，其只有在声明的时候和静态构造函数中才可以赋值
2. const隐藏着static，所以static和const修饰符不能同时使用