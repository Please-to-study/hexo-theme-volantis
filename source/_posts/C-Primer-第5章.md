---
title: C++ Primer 第5章 控制语句
seo_title: C++ Primer 第5章 控制语句
toc: true
indent: true
comments: true
archive: true
cover: true
mathjax: false
pin: false
top_meta: true
bottom_meta: true
sidebar:
  - toc
icons: []
references:
  - title: C++ Primer
date: 2021-11-27 21:41:05
updated: 2021-11-27 21:41:05
categories:
- C++基础知识
keywords: 控制语句
description: C++语言的控制语句
headimg: https://cdn.pkubailu.cn/img/第5章封面.png
thumbnail:
tags:
- C++基础
---

{% note info:: 第 5 章 控制语句 %}

# 第5章 语句

## 5.3 控制语句

### 5.3.2 switch语句

case关键字和它对应的值一起被称为**case标签**。case标签必须是整型常量表达式：

```C++
char ch = getVal();
int ival = 42;
switch(ch) {
  case 3.14: // 错误：case标签不是一个整数
  case ival: // 错误：case标签不是常量
}
```

请记住整型常量这四个字，不满足这个特性的不能作为case值，编译会报错。这也决定了switch的参数必须是整型的。整型，意味着浮点数是不合法的，如case 3.14：不可以；常量，意味着变量是不合法的，如case ival： ival不能是变量。

1. C++中的const int，注意仅限于C++中的const，C中的const是只读变量，不是常量；

2. 单个字符，如case 'a': 是合法的，因为文字字符是常量，被转成ASCII码，为整型；

3. 使用#define定义的整型，#define定义的一般为常量，比如#define pi 3.14，但是也必须是整型才可以；

4. 使用enum定义的枚举成员。因为枚举成员是const的，且为整型。如果不手动指定枚举值，则默认枚举值为从0开始，依次加1。如下这段代码正常运行：

   ```C++
   #include<iostream>
   using namespace std;
   enum color{red,yellow,green};
   int main()
   {
       int co = 2;
       switch(co)
       {
       	case red:
           cout<<"red"<<endl;
           break;
       	case yellow:
           cout<<"yellow"<<endl;
           break;
       	case green:
           cout<<"green"<<endl;
           break;
       	default:
           cout<<"no match color"<<endl;
           break;
       }
       return 0;
   }
   ```

## 5.6 try语句块和异常处理

在复杂系统中，程序在遇到抛出异常的代码前，其执行路径可能已经经过了多个try语句块。例如，一个try语句块可能调用了包含另一个try语句块的函数，新的try语句块可能调用了包含又一个try语句块的新函数，以此类推。

寻找处理代码的过程与函数调用链刚好相反。当异常被抛出时，首先搜索抛出该异常的函数。如果没找到匹配的catch字句，终止该函数，并在调用该函数的函数中继续寻找。如果还是没有找到匹配的catch子句，这个新的函数也被终止，继续搜索调用它的函数。以此类推，沿着程序的执行路径逐层回退，直到找到适当类型的catch子句为止。

如果最终还是没能找到任何匹配的catch子句，程序转到名为terminate的标准库函数，一般情况下，执行该函数将导致程序非正常退出。

如果一段程序没有try语句块且发生了异常，系统会调用terminate函数并终止当前程序的执行。
