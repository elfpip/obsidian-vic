# 函数式编程概要

## 一、什么是函数式编程
**函数式编程**，或称**函数程序设计**、**泛函编程**（英语：Functional programming），是一种[编程范式](https://zh.wikipedia.org/wiki/%E7%BC%96%E7%A8%8B%E8%8C%83%E5%BC%8F "编程范式")，它将[电脑运算](https://zh.wikipedia.org/wiki/%E9%9B%BB%E8%85%A6%E9%81%8B%E7%AE%97 "电脑运算")视为[函数](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0 "函数")运算，并且避免使用程序[状态](https://zh.wikipedia.org/w/index.php?title=%E7%8A%B6%E6%80%81_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)&action=edit&redlink=1)以及[易变对象](https://zh.wikipedia.org/wiki/%E4%B8%8D%E5%8F%AF%E8%AE%8A%E7%89%A9%E4%BB%B6 "不可变对象")。其中，[λ演算](https://zh.wikipedia.org/wiki/%CE%9B%E6%BC%94%E7%AE%97 "Λ演算")为该语言最重要的基础。而且，λ演算的函数可以接受函数作为输入参数和输出返回值。

比起[指令式编程](https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B "指令式编程")，函数式编程更加强调程序执行的结果而非执行的过程，倡导利用若干简单的执行单元让计算结果不断渐进，逐层推导复杂的运算，而不是设计一个复杂的执行过程。

==在函数式编程中，函数是[第一类对象](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%80%E7%B1%BB%E5%AF%B9%E8%B1%A1 "第一类对象")==，意思是说一个函数，既可以作为其它函数的输入参数值，也可以从函数中返回值，被修改或者被分配给一个变量。

## 二、函数式编程有典型的例子
1. Java.util.function 包下所有内容
2. 比较典型的 API
	1. Stream
	2. CompletableFuture
	3. Optional

## 三、函数式编程有哪些特性

