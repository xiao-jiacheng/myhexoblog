---
title: c++ const.md
author: Chaos
date: 2024/2/1
--


指针与const关键字

  指针也可以使用const关键字来声明。当我们使用const和指针时，我们可以用两种方式来做：可以把const应用到指针指向的地方，或者我们可以使指针本身成为一个常数。 

①指向const变量的指针：

  这意味着指针指向一个const变量。

const int* u;
  在这里，表示u是一个指针，可以指向const int类型变量。我们也可以这样写，

char const* v;
表示v是指向const类型的char的指针。

指向const变量的指针非常有用，因为它可以用来使任何字符串或数组不可变

②const指针

  为了使指针保持不变，我们必须把const关键字放到右边。

int x = 1;
int* const w = &x;
  这里，w是一个指针，它是const，指向一个int，现在我们不能改变指针，这意味着它总是指向变量x但是可以改变它指向的值，通过改变x的值。

    当你想要一个可以在值中改变但不会在内存中移动的存储器时，常量指针指向一个变量是很有用的。因为指针总是指向相同的内存位置，因为它是用const关键字定义的，但是那个内存位置的值可以被更改。


常量引用无法在分支中赋值
const Apn_name& apn = ue_ctxt->getSubscribedApn();
if (any_condition){
    apn = ue_ctxt->getSubscribedApn();
}
else if (other_condition){
    apn = req_ctxt->getRequestApn();
}
passing as 'this' argument discards qualifier
错误在apn第二次赋值时编译器报错，常量只能一次初始化，无法使用=再次赋值

Apn_name const* apn = &(ue_ctxt->getSubscribedApn());
if(any_condition)
{
    apn = &(ue_ctxt->getSubscribedApn());
}
else if( other_condition)
{
    apn = &(procCtxt->getRequestedApn());
}