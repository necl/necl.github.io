---
layout: post
title: gcc C语言构造函数(constructor)和析构函数(destructor)
date: 2014-11-06 07:34
category: Code
tags: C
---

{% include JB/setup %}

相比C++，gcc的C语言也支持构造函数和析构函数，用于隐式地初始化数据，QEMU中的`type_init()`就使用了这种方式的初始化。

构造函数在`main()`函数执行前提前执行，析构函数在`main()`退出后或`exit()`后执行。

构造函数通过在函数名前面加constructor属性指定，析构函数通过在函数名前面加destructor属性指定。
可以通过优先级指定调用顺序，优先级值越小的先执行。如下面函数声明：

    void __attribute__((constructor)) constructor_default(void );
    void __attribute__((constructor(110))) constructor_110(void );
    void __attribute__((destructor)) constructor_default(void );
    void __attribute__((destructor(110))) constructor_110(void );



测试代码如下：

    #include<stdio.h>
    int main()
    {
    printf("In main().\n");
    return 0;
    }
    void __attribute__((constructor)) constructor_pd()
    {
    printf("In constructor_pd().\n");
    }
    void __attribute__((constructor(101))) constructor_p101()
    {
    printf("In constructor_p101().\n");
    }
    void __attribute__((constructor(1))) constructor_p1()
    {
    printf("In constructor_p1().\n");
    }
    void __attribute__((destructor)) destructor_pd()
    {
    printf("In destructor_pd().\n");
    }
    void __attribute__((destructor(101))) destructor_p101()
    {
    printf("In destructor_p101().\n");
    }
    void __attribute__((destructor(1))) destructor_p1()
    {
    printf("In destructor_p1().\n");
    }

在Windows MinGW环境下执行结果：

    C:\new>a.exe
    In constructor_p1().
    In constructor_p101().
    In constructor_pd().
    In main().
    In destructor_pd().
    In destructor_p101().
    In destructor_p1().

    C:\new>

参考：[https://gcc.gnu.org/onlinedocs/gcc/Function-Attributes.html](https://gcc.gnu.org/onlinedocs/gcc/Function-Attributes.html)    
