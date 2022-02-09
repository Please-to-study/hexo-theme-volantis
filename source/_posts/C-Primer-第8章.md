---
title: C++ Primer 第8章 IO库
seo_title: C++ Primer 第8章 IO库
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
    url: https://github.com/Please-to-study/C-Study/blob/master/C++primer.md
date: 2021-12-11 21:32:49
updated: 2021-12-11 21:32:49
categories:
- C++基础知识
keywords: IO库
description: C++中的IO库
headimg: https://cdn.pkubailu.cn/img/第8章封面.png
thumbnail:
tags:
- C++基础
---

{% note info:: 第 8 章 IO库 %}

# 第8章 IO库

## 8.1 IO类

标准库还定义了其他一些IO类型：iostream定义了用于读写流的基本类型，fstream定义了读写命名文件的类型，sstream定义了读写内存string对象的类型。

![](https://cdn.pkubailu.cn/img/8.1.png)

为了支持使用宽字符的语言，标准库定义了一组类型和对象来操纵wchar_t类型的数据。宽字符版本的类型和函数的名字以一个w开始。

### 8.1.0 IO类型间的关系

标准库使我们能忽略这些不同类型的流之间的差异，这是通过**继承机制**实现的。利用模板，我们可以使用具有继承关系的类，而不必了解继承机制如何工作的细节。

简单地说，继承机制使我们可以声明一个特定的类继承自另一个类。我们通常可以将一个派生类（继承类）对象当作其基类（所继承的类）对象来使用。

### 8.1.1 IO对象无拷贝或赋值

 不能拷贝或对IO对象赋值：

```C++
ofstream out1, out2;
out1 = out2; // 错误：不能对流对象赋值
ofstream print(ofstream); // 错误：不能初始化ofstream参数
out2 = print(out2); // 错误： 不能拷贝流对象
```

由于不能拷贝IO对象，因此我们也不能将形参或返回类型设置为流类型。进行IO操作的函数通常以引用方式传递和返回流。读写一个IO对象会改变其状态，因此传递和返回的引用不能是const的。

### 8.1.2 条件状态

下表列出了IO类所定义的一些函数和标志，可以帮助我们访问和操纵流的**条件状态**。

![](https://cdn.pkubailu.cn/img/8.2-1.png)

![](https://cdn.pkubailu.cn/img/8.2-2.png)

一个流一旦发生错误，其上后续的IO操作都会失败。只有当一个流处于无错状态时，我们才可以从它读取数据，向它写入数据。由于流可能处于错误状态，因此代码通常应该在使用一个流之前检查它是否处于良好状态。确定一个流对象的状态的最简单的方法是将它当作一个条件来使用：

```C++
while(cin >> word)
  // ok:读操作成功.....
```

while循环检查>>表达式返回的流的状态。

#### 查询流的状态

IO库定义了一个与机器无关的iostate类型，它提供了表达流状态的完整功能。这个类型应作为一个位集合来使用。IO库定义了4个iostate类型的constexpr值，表示特定的位模式。这些值用来表示特定类型的IO条件，可以与位运算符一起使用来一次性检验或设置多个标志位。

badbit表示系统级错误，如不可恢复的读写错误。一旦badbit被置位，流就无法再使用了。在发生可恢复错误后，failbit被置位。如果到达文件结束位置，eofbit和fail都会被置位

goodbit的值为0，表示流未发生错误。如果badbit、failbit、eofbit任一个被置位，则检测流状态的条件会失败。

使用good或fail是确定流的总体状态的正确方法。实际上，我们将流当做条件使用的代码就等价于!fail()。

#### 管理条件状态

流对象的rdstate成员返回一个iostate值，对应流当前状态。setstate操作将给定条件置位，表示发生了对应错误。clear成员是一个重载的成员：它有一个不接收参数的版本，而另一个版本接受一个iostate类型的参数。

clear不接收参数的版本清除（复位）所有错误标志位。执行clear()后，调用good会返回true。

```C++
// 记住cin的当前状态
auto old_state = cin.rdstate(); // 记住cin当前状态
cin.clear(); // 使cin有效
process_input(cin); // 使用cin
cin.setstate(old_state); // 将cin置为原有状态
```

带参数的clear版本接受一个iostate值，表示流的新状态。为了复位单一的条件状态位，我们首先用rdstate读出当前条件状态，然后用位操作将所需位复位来生成新的状态。

```C++
cin.clear(cin.rdstate & ~cin.failbit & ~cin.badbit);
```

### 8.1.3 管理输出缓冲

每个输出流都管理一个缓冲区，用来保存程序读写的数据。

```C++
os << "please enter a value";
```

文本串可能立即打印出来，但也有可能被操作系统保存在缓冲区中，随后再打印。

导致缓冲刷新（即，数据真正写到输出设备或文件）的原因有很多：

- 程序正常结束，作为main函数的return操作的一部分，缓冲刷新被执行。
- 缓冲区满时，需要刷新缓冲，而后新的数据才能继续写入缓冲区。
- 我们可以使用操作符如endl来显示刷新缓冲区。
- 在每个输出操作之后，我们可以用操纵符unitbuf设置流的内部状态，来清空缓冲区。默认情况下，对cerr是设置unitbuf的，因此写到cerr的内容都是立即刷新的。
- 一个输出流可能被关联到另一个流。在这种情况下，当读写被关联的流时，关联到的流的缓冲区会被刷新。例如，默认情况下，cin和cerr都关联到cout。因此，读cin或写cerr都会导致cout的缓冲区刷新。

#### 刷新输出缓冲区

```C++
cout << "hi!" << endl; // 输出hi和一个换行，然后刷新缓冲区
cout << "hi!" << flush; // 输出hi，然后刷新缓冲区，不附加任何额外字符
cout << "hi!" << ends; // 输出hi和一个空字符，然后刷新缓冲区
```

#### unitbuf 操纵符

如果想在每次输出操作后都刷新缓冲区，我们可以使用unitbuf操纵符。它告诉流在接下来的每次写操作之后都进行一次flush操作。而nounitbuf操纵符则重置流，使其恢复使用正常的系统管理的缓冲区刷新机制：

```C++
cout << unitbuf; // 所有输出操作后都会立即刷新缓冲区
cout << nounitbuf; // 回到正常的缓冲方式
```

> Warning! 如果程序异常终止，输出缓冲区不会被刷新。它所输出的数据很可能停留在输出缓冲区中等待打印。

#### 关联输入和输出流

当一个输入流被关联到一个输出流时，任何试图从输入流读取数据的操作都会先刷新关联的输出流。标准库将cout和cin关联在一起。

```c++
cin >> ival;
// 导致cout的缓冲区被刷新
```

tie有两个重载的版本：一个版本不带参数，返回指向输出流的指针。如果本对象当前关联到一个输出流，则返回的就是指向这个流的指针，如果对象未关联到流，则返回空指针。tie的第二个版本接受一个指向ostream的指针，将自己关联到此ostream。即x.tie(&o)将x流关联到输出流o。

我们既可以将一个istream对象关联到另一个ostream，也可以将一个ostream关联到另一个ostream：

```C++
cin.tie(&cout); // 标准库将cin和cout关联在一起
ostream *old_tie = cin.tie(nullptr); // cin不再与其他流关联
cin.tie(&cerr); // 读取cin会刷新cerr而不是cout
cin.tie(old_tie); // 重建cin和cout间的正常关联
```

为了将一个给定的流关联到一个新的输出流，我们将新流的指针传递给了tie。为了彻底揭开流的关联，我们传递了一个空指针。每个流同时最多关联到一个流，但多个流可以同时关联到同一个ostream。

## 8.2 文件输入输出

头文件fstream定义了三个类型来支持文件IO: ifstream从一个给定文件读取数据，ofstream向一个给定文件写入数据，以及fstream可以读写给定文件。

这些类型提供的操作与我们之前已经使用过的对象cin和cout的操作一样。

除了继承自iostream类型的行为之外，fstream中定义的类型还增加了一些新的成员来管理与流关联的文件。我们可以对fstream、ifstream和ofstream对象调用这些操作，但不能对其他IO类型调用这些操作。

![](https://cdn.pkubailu.cn/img/8.3.png)

### 8.2.1 使用文件流对象

当我们想要读写一个文件时，可以定义一个文件流对象，并将对象与文件关联起来。每个文件流类都定义了一个名为open的成员函数，它完成一些系统相关的操作，来定位给定的文件，并视情况打开为读或写模式。

创建文件流对象时，我们可以提供文件名（可选的）。如果提供了一个文件名，则open会自动调用：

```C++
ifsream in(ifile); // 构建一个ifstream并打开给定文件
ofstream out; // 输出文件流未关联到任何文件
```

这段代码定义了一个输入流in，它被初始化为从文件读取数据，文件名由string类型的参数ifile指定。第二条语句定义了一个输出流out，未与任何文件关联。文件名既可以是库类型string对象，也可以是C风格字符数组。

#### 用fstream代替iostream&

我们已经提到过。在要求使用基类型对象的地方，我们可以用继承类型的对象来替代。这意味着，接受一个iostream类型引用（或指针）参数的函数，可以用一个对应的fstream（或sstream）类型来调用。

例如：我们假定输入和输出文件的名字是通过传递给main函数的参数来指定的：

![](https://cdn.pkubailu.cn/img/8.2.1.png)

#### 成员函数open和close

如果我们定义了一个空文件流对象，可以随后调用open来将它与文件关联起来：

```C++
ifstream in(ifile); // 构筑一个ifstream并打开给定文件
ofstream out; // 输出文件流未与任何文件相关联
out.open(ifile + ".copy"); // 打开指定文件
```

如果调用open失败，failbit会被置位。因为调用open可能失败，进行open是否成功的检测通常是一个好习惯：

```C++
if (out) // 检查open是否成功
  // open成功执行的操作
```

一旦一个文件流已经打开，它就保持与对应文件的关联。实际上，对一个已经打开的文件流调用open会失败，并会导致failbit被置位。随后的试图使用文件流的操作都会失败。为了将文件流关联到另外一个文件，必须首先关闭已经关联的文件。一但文件成功关闭，我们可以打开新的文件：

```C++
in.close(); // 关闭文件
in.open(ifile + "2"); // 打开另一个文件
```

如果open成功，则open会设置流的状态，使得good()为true。

#### 自动构造和析构

考虑这样一个程序，它的main函数接受一个要处理的文件列表。这种程序可能会有如下的循环：

```C++
// 对每个传递给程序的文件执行循环操作
for(auto p = argv + 1; p != argv + argc; ++p) {
  ifstream input(*p); // 创建输出流并打开文件
  if (input) { // 如果文件打开成功，处理此文件
    process(input);
  } else
    cerr << "couldn't open: " + string(*p);
} // 每个循环步 input都会离开作用域，因此会被销毁
```

当一个fstream对象离开其作用域时，与之关联的文件会自动关闭。在下一步循环中，input会再次被创建。

> 当一个fstream对象被销毁时，close会自动被调用。

### 8.2.2 文件模式

每个流都有一个关联的**文件模式**，用来指出如何使用文件。

![](https://cdn.pkubailu.cn/img/8.4.png)

无论用哪种方式打开文件，我们都可以指定文件模式，调用open打开文件时可以，用一个文件名初始化流来隐式打开文件时也可以。指定文件模式有如下限制：

- 只可以对ofstream或fstream对象设定out模式。
- 只可以对ifstream或fstream对象设定in模式。
- 只有当out也被设定时才可设定trunc模式。
- 只要trunc没被设定，就可以设定app模式。在app模式下，即使没有显示指定out模式，文件也总是以输出方式被打开。
- 默认情况下，即使我们没有指定trunc，以out模式打开的文件也会被截断。为了保留以out模式打开的文件的内容，我们必须同时指定app模式，这样只会将数据追加写到文件末尾；或者同时指定in模式，即打开文件同时进行读写操作。
- ate和binary模式可用于任何类型的文件流对象，且可以与其他任何文件模式组合使用。

每个文件流类型都定义了一个默认的文件模式，当我们未指定文件模式时，就使用此默认模式。与ifstream关联的文件默认以in模式打开；与ofstream关联的文件默认以out模式打开；与fstream关联的文件默认以in和out模式打开。

#### 以out模式打开文件会丢弃已有数据

默认情况下，当我们打开一个ofstream时，文件的内容会被丢弃。阻止一个ofstream清空给定文件内容的方法是同时指定app模式：

```C++
// 在这几条语句中，file1都被截断
ofstream out("file1") // 隐含以输出模式打开文件并截断文件
ofstream out("file1", ofstream::out) // 隐含的截断文件
ofstream out("file1", ofstream::out | ofstream::trunc)
// 为了保留文件内容，我们必须显示指定app模式
ofstream app("file2", ofstream::app) // 隐含为输出模式
ofstream app("file2", ofstream::out | ofstream::app)
```

> 保留被ofstream打开的文件中已有数据的唯一方法是显示指定app或in模式

#### 每次调用open时都会确定文件模式

对于一个给定流，每当打开文件时，都可以改变其文件模式。

```C++
ofstream out; // 未指定文件打开模式
out.open("file1"); // 模式隐含设置为输出和截断
out.close(); // 关闭out,以便我们将其用于其他文件
out.open("file2", ofstream::app); // 模式为输出和追加
out.close();
```

第一个open调用未显示指定输出模式，文件隐式地以out模式打开。通常情况下，out模式意味着同时使用trunc模式。因此，当前目录下名为file1的文件的内容将被清空。当打开名为file2的文件时，我们制定了append模式。文件中已有的数据都得以保留，所有写操作都在文件末尾进行。

> 在每次打开文件时，都要设置文件模式，可能是显示设置，也可能是隐式的设置。当程序未指定模式时，就使用默认值。

## 8.3 string 流

istringstream从string读取数据，ostringstream向string写入数据，而头文件stringstream即可从string读数据也可向string写数据。与fstream类型类似，头文件sstream中定义的类型都继承自我们已经使用过的iostream头文件中定义的类型。除了继承得来的操作，sstream中定义的类型还增加了一些成员来管理与流相关联的string。

![](https://cdn.pkubailu.cn/img/8.5.png)

可以对stringstream对象调用这些操作，但不能对其他IO类型调用这些操作。

### 8.3.1 使用istringstream

当我们的某些工作是对整行文本进行处理，而其他一些工作是处理行内的单个单词时，通常可以使用istringstream。

![](https://cdn.pkubailu.cn/img/8.3.1.png)

### 8.3.2 使用ostringstream

当我们逐步构造输出，希望最后一起打印时，ostringstream是很有用的。例如，对比上一节的例子，我们可能想逐个验证电话号码并改变其格式。如果所有号码都是有效的，我们希望输出一个新的文件，包含改变格式后的号码。对于无效的号码，我们不会将它们输出到新文件中，而是打印一条包含人名和无效号码的错误信息。

![](https://cdn.pkubailu.cn/img/8.3.2.png)

## 8.4 总结

1. iostream定义了用于读写流的基本类型，fstream定义了读写命名文件的类型，sstream定义了读写内存string对象的类型。
2. 继承机制使我们可以声明一个特定的类继承自另一个类。我们通常可以将一个派生类（继承类）对象当作其基类（所继承的类）对象来使用
3.  不能拷贝或对IO对象赋值
4. 由于不能拷贝IO对象，因此我们也不能将形参或返回类型设置为流类型。进行IO操作的函数通常以引用方式传递和返回流。读写一个IO对象会改变其状态，因此传递和返回的引用不能是const的。
5. 一个流一旦发生错误，其上后续的IO操作都会失败。只有当一个流处于无错状态时，我们才可以从它读取数据，向它写入数据
6. 每个输出流都管理一个缓冲区，用来保存程序读写的数据
7. 文本串可能立即打印出来，但也有可能被操作系统保存在缓冲区中，随后再打印
8. 导致缓冲刷新的原因有很多:
   - 程序正常结束
   - 缓冲区满
   - 使用操纵符（endl、flush、unitbuf）
   - 读写关联流（cin 与 cerr 都与cout关联）
9. 如果程序异常终止，输出缓冲区不会被刷新。它所输出的数据很可能停留在输出缓冲区中等待打印
10. 每个流同时最多关联到一个流，但多个流可以同时关联到同一个ostream
11. 文件名既可以是库类型string对象，也可以是C风格字符数组
12. 如果我们定义了一个空文件流对象，可以随后调用open来将它与文件关联起来
13. 一旦一个文件流已经打开，它就保持与对应文件的关联
14. 对一个已经打开的文件流调用open会失败，并会导致failbit被置位。随后的试图使用文件流的操作都会失败。
15. 为了将文件流关联到另外一个文件，必须首先关闭已经关联的文件
16. 如果open成功，则open会设置流的状态，使得good()为true。
17. 当一个fstream对象被销毁时，close会自动被调用
18. 保留被ofstream打开的文件中已有数据的唯一方法是显示指定app或in模式
19. 在每次打开文件时，都要设置文件模式，可能是显示设置，也可能是隐式的设置。当程序未指定模式时，就使用默认值
20. 当我们的某些工作是对整行文本进行处理，而其他一些工作是处理行内的单个单词时，通常可以使用istringstream
21. 当我们逐步构造输出，希望最后一起打印时，ostringstream是很有用的
