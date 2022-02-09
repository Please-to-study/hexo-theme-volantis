---
title: C++ Primer 第11章 关联容器
seo_title: C++ Primer 第11章 关联容器
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
date: 2022-01-01 01:43:37
updated: 2022-01-01 01:43:37
categories:
- C++基础知识
keywords: 关联容器
description: C++中的关联容器
headimg: https://cdn.pkubailu.cn/img/第11章封面.png
thumbnail:
tags:
- C++基础
---

{% note info:: 第 11 章 关联容器 %}

# 第11章 关联容器

关联容器支持高效的关键字查找和访问。两个主要的**关联容器**类型是map和set。map中的元素是一些（关键字-值）对：关键字起到索引的作用，值则表示与索引相关联的数据。set中每个元素只包含一个关键字；set支持高效的关键字查询操作—检查一个给定关键字是否在set中。例如，在某些文本处理过程中，可以用一个set来保存想要忽略的单词。字典则是一个很好的使用map的例子：可以将单词作为关键字，将单词释义作为值。

标准库提供8个关联容器。这8个容器间的不同体现在三个维度上：

1. 或者是一个set，或者是一个map；
2. 或者要求不重复的关键字，或者允许重复关键字；
3. 按顺序保存元素，或无序保存。

允许重复关键字的容器的名字中都包含单词multi；

不保持关键字按顺序存储的容器的名字都以单词unordered开头。

无序容器使用哈希函数来组织元素。

![](https://cdn.pkubailu.cn/img/11.1.png)

## 11.1 使用关联容器

map通常被称为**关联数组**。关联数组与”正常数组“类似，不同之处在于其下标不必是整数。我们通过一个关键字而不是位置来查找值。

与之相对，set就是关键字的简单集合。当只是想知道一个值是否存在时，set是最有用的。

### 11.1.1 使用 map

![](https://cdn.pkubailu.cn/img/使用map.png)

### 11.1.2 使用 set

![](https://cdn.pkubailu.cn/img/使用set-1.png)

![](https://cdn.pkubailu.cn/img/使用set-2.png)

## 11.2 关联容器概述

关联容器都支持9.2节中介绍的普通容器操作。关联容器不支持顺序容器的位置相关的操作，例如push_front、push_back。原因是关联容器中元素是根据关键字存储的。

关联容器的迭代都是双向的。

### 11.2.1 定义关联容器

当定义一个map时，必须即指明关键字类型又指明值类型；而定义一个set时，只需指明关键字类型就行。

```C++
map<string, size_t> word_count; // 空容器
// 列表初始化
set<string> exclude = {"the", "but", "and"};
// 三个元素；authors将姓映射为名
map<string, string> authors = { {"Joyce", "james"},
                               {"Austen", "Jane"},
                               {"Dickens", "Charles"}
                              };
```

初始化器必须能转换为容器中元素的类型。

当初始化一个map时，必须提供关键字类型和值类型。我们将每个关键字—值对包围在花括号中：{key, value}。

#### 初始化multimap 或 multiset

一个map或set中的关键字必须是唯一的。容器multimap和multiset没有此限制，它们都允许多个元素具有相同的关键字。

```C++
vector<int> ivec;
for(vector<int>::size_type i = 0; i != 10; ++i){
  ivec.push_back(i);
  ivec.push_back(i);
}
// ivec 包含来自ivec的不重复的元素；miset包含所有20个元素
set<int> iset(ivec.cbegin(), ivec.cend());
multiset<int> miset(ivec.cbegin(), ivec.cend());
```

即使我们用整个ivec容器来初始化iset，它也只含有10个元素：对应ivec中每个不同的元素。另一方面，miset有20个元素，与ivec中的元素数量一样多。

### 11.2.2 关键字类型的要求

关联容器对其关键字类型有一些限制。对于有序容器——map、multimap、set、multiset，关键字类型必须定义元素比较的方法。默认情况下，标准库使用关键字类型的<运算符来比较两个关键字。

> Note! 传递给排序算法的可调用对象必须满足与关联容器中关键字一样的类型要求。

#### 有序容器的关键字类型

可以向一个算法提供我们自己定义的比较操作，与之类似，也可以提供自己定义的操作来代替关键字上的<运算符。所提供的操作必须在关键字类型上定义一个**严格弱序**。可以将严格弱序看作“小于等于”，虽然实际定义的操作可能是一个复杂的函数。无论我们怎样定义比较函数，它必须具备如下基本性质：

- 两个关键字不能同时“小于等于”对方；如果k1“小于等于”k2，那么k2决不能“小于等于”k1。
- 如果k1“小于等于”k2，且k2”小于等于“k3，那么k1必须”小于等于“k3。
- 如果存在两个关键字，任何一个都不”小于等于“另一个，那么我们称这两个关键字是”等价“的。

如果两个关键字是等价的，那么容器将他们视为相等来处理。当用作map的关键字时，只能有一个元素与这两个关键字关联，我们可以用两者中任意一个来访问对应的值。

> Note! 在实际编程中，重要的是，如果一个类型定义了”行为正常“的<运算符，则它可以用作关键字类型。

#### 使用关键字类型的比较函数

用来组织一个容器中元素的操作的类型也是该容器类型的一部分。为了指定使用自定义的操作，必须在定义关联容器类型时提供此操作的类型。如前所述，用尖括号指出要定义哪种类型的容器，自定义的操作类型必须在尖括号中紧跟着元素类型给出。

在尖括号中出现的每个类型，就仅仅是一个类型而已。当我们创建一个容器（对象）时，才会以构造函数参数的形式提供真正的比较操作（其类型必须与在尖括号中指定的类型相吻合）。

<img src="https://cdn.pkubailu.cn/img/使用关键字类型的比较函数.png"  />

### 11.2.3 pair 类型

一个pair保存两个数据成员。类似容器，pair是一个用来生成特定类型的模板。当创建一个pair时，我们必须提供两个类型名，pair的数据成员将具有对应的类型。两个类型不要求一样：

```C++
pair<string, string> anon; // 保存两个string
pair<string, size_t> word_count; // 保存一个string和一个size_t
pair<string, vector<int>> line; // 保存string和vector<int>
```

pair的默认构造函数对数据成员进行值初始化。因此，anon是一个包含两个空string的pair，word_count中的size_t成员值为0，而string成员被初始化为空。

我们也可以为每个成员提供初始化器：

```C++
pair<string, string> author{"James", "Joyce"};
```

与其他标准库类型不同，pair的数据成员是public的。两个成员分别命名为first和second。我们用普通的成员访问符号（.）来访问他们。

![](https://cdn.pkubailu.cn/img/11.2.png)

#### 创建pair对象的函数

想象有一个函数需要返回一个pair。在新标准下，我们可以对返回值进行列表初始化

```C++
pair<string, int> process(vector<int> &v) {
  // 处理v
  if(!v.empty())
    return {v.back(), v.back().size()}; // 列表初始化
  else
    return pair<string, int>(); // 隐式构造返回值
}
// 显式构造返回值
if(!v.empty()) {
  return pair<string, int>(v.back(), v.back().size());
}
// 使用make_pair来生成pair对象，pair的两个类型来自于make_pair的参数
if(!v.empty()) {
  return make_pair(v.back(), v.back().size());
}
```

## 11.3 关联容器操作

除了表9.2中列出的类型，关联容器还定义了以下列出的类型。这些类型表示容器关键字和值的类型。

![](https://cdn.pkubailu.cn/img/11.3.png)

对于set类型，key_type和value_type是一样的；set中保存的值就是关键字。在一个map中，元素是关键字—值对。即，每个元素是一个pair对象，包含一个关键字和一个关联的值。由于我们不能改变一个元素的关键字，因此这些pair的关键字部分是const的：

```C++
set<string>::value_type v1; // v1是一个string
set<string>::key_type v2; // v2是一个string
map<string, int>::value_type v3; // v3是一个pair<const string, int>
map<string, int>::key_type v4; // v4是一个string
map<string, int>::mapped_type v5; // v5是一个int
```

与顺序容器一样，我们使用作用域运算符来提取一个类型的成员——例如，map<string, int>::key_type。

只有map类型（unordered_map、unordered_multimap、multimap、map）才定义了mapped_type。

### 11.3.1 关联容器迭代器

当解引用一个关联迭代器时，我们会得到一个类型为容器的value_type的值的引用。

```C++
// 获得指向word_count中一个元素的迭代器
auto map_it = word_count.begin();
// *map_it是一个指向pair<const string, size_t>对象的引用
cout << map_it->first; // 打印此元素的关键字
cout << map_it->second // 打印此元素的值
map_it->first = "new key"; // 错误：关键字是const的
++map_it->second; // 正确：我们可以通过迭代器改变元素
```

> Note! 必须记住，一个map的value_type是一个pair，我们可以改变pair的值，但不能改变关键字成员的值。

#### set的迭代器是const的

虽然set类型同时定义了iterator和const_iterator类型，但两种类型都只允许只读访问set中的元素。与不能改变一个map元素的关键字一样，一个set中的关键字也是const的。可以用一个set迭代器来读取元素的值，但不能修改：

```C++
set<int> iset = {0,1,2,3,4,5};
set<int>::iterator set_it = iset.begin();
if(set_it != iset.end()){
  *set_it = 42; // 错误：set中的关键字是只读的
  cout << *set_it <<endl; // 正确：可以读关键字
}
```

#### 遍历关联容器

map和set类型都支持表9.2中的begin和end操作。与往常一样，我们可以用这些函数获取迭代器，然后用迭代器来遍历容器。

```C++
// 获得一个指向首元素的迭代器
auto map_it = word_count.cbegin();
while(map_it != word_count.cend()){
  cout << map_it->first;
  cout << map_it->second;
  ++map_it; // 递增迭代器，移动到下一个元素
}
```

> Note! 本程序的输出是按字典序排列的。当使用一个迭代器遍历一个map、multimap、set、multiset时，迭代器按关键字升序遍历元素

#### 关联容器和算法

我们通常不对关联容器使用泛型算法。关键字是const这一特性意味着不能将关联容器传递给修改或重排容器元素的算法，因为这类算法需要向元素写入值，而set类型中的元素是const的，map中的元素是pair，其第一个成员是const的。

关联容器可用于只读取元素的算法。但是，很多这类算法都要搜索序列。由于关联容器中的元素不能通过它们的关键字进行（快速）查找，因此对其使用泛型搜索算法几乎总是个坏主意。例如，关联容器定义了一个名为find的成员，它通过一个给定的关键字直接获取元素。我们可以用泛型find算法来查找一个元素，但此算法会进行顺序搜索。使用关联容器定义的专用的find成员会比调用泛型find快得多。

在实际编程中，如果我们真要对一个关联容器使用算法，要么是将它当做一个源序列，要么当做一个目的位置。

### 11.3.2 添加元素

关联容器的insert成员向容器中添加一个元素或一个元素范围。由于map和set（以及对应的无序类型）包含不重复的关键字，因此插入一个已经存在的元素对容器没有任何影响：

```C++
vector<int> ivec = {2,4,6,8,2,4,6,8};
set<int> set2;
set2.insert(ivec.begin(), ivec.end()); // set2有4个元素
set2.insert({1,3,5,7,1,3,5,7}); // set2 现在有8个元素
```

insert有两个版本，分别接受一对迭代器，或是一个初始化器列表，这两个版本的行为类似对应的构造函数——对于一个给定的关键字，只有第一个带此关键字的元素才被插入到容器中。

#### 向map添加元素

对一个map进行insert操作时，必须记住元素类型是pair。通常对于想要插入的数据，并没有一个现成的pair对象。可以在insert的参数列表中创建一个pair：

```C++
// 向word_count插入word的4种方法
word_count.insert({word, 1});
word_count.insert(make_pair(word, 1));
word_count.insert(pair<string, size_t>(word, 1));
word_count.insert(map<string, size_t>::value_type(word, 1));
```

如我们所见，在新标准下，创建一个pair最简单的方法是在参数列表中使用花括号初始化。

![](https://cdn.pkubailu.cn/img/11.4.png)

#### 检测insert的返回值

insert（或emplace）返回的值依赖于容器类型和参数。对于不包含重复关键字的容器，添加单一元素的insert和emplace版本返回一个pair，告诉我们插入操作是否成功。pair的first成员是一个迭代器，指向具有给定关键字的元素；second成员是一个bool值，指出元素是插入成功还是已经存在于容器中。如果关键字已在容器中，则insert什么事情也不做，且返回值中的bool部分为false。如果关键字不存在，元素被插入容器中，且bool值为true。

例子：用insert重写单词计数程序：

```C++
map<string, size_t> word_count;
string word;
while(cin >> word){
  auto ret = word_count.insert({word, 1});
  if(!ret.second) // word 已在word_count中
    ++ret.first->second; // 递增计数器
}
```

#### 展开递增语句

在这版本的单词计数程序中，递增计数器的语句很难理解。通过添加一些括号来反映出运算符的优先级，会使表达式更容易理解一些：

```C++
++((ret.first)->second); // 等价的表达式
```

下面我们一步一步来解释此表达式：

ret 保存insert返回的值，是一个pair。

ret.first是pair的第一个成员，是一个map迭代器，指向具有给定关键字的元素。

ret.first->解引用此迭代器，提取map中的元素，元素也是一个pair。

ret.first->second map中元素的值部分。

++ret.first->second递增此值。

#### 向multiset 或 multimap添加元素

我们的单词计数程序依赖于这样一个事实：一个给定的关键字只能出现一次。我们有时希望能添加具有相同关键字的多个元素。例如，可能想建立作者到他所著书籍题目的映射。

```C++
multimap<string, string> authors;
// 插入第一个元素，关键字为Barth, John
authors.insert({"Barth, John", "Sot-Weed Factor"});
// 正确：添加第二个元素，关键字也是Barth, John
authors.insert({"Barth, John", "Lost in the Funhouse"});
```

对允许重复关键字的容器，接收单个元素的insert操作返回一个指向新元素的迭代器。这里无须返回一个bool值，因为insert总是向这类容器中加入一个新元素。

### 11.3.3 删除元素

![](https://cdn.pkubailu.cn/img/11.5.png)

关联容器定义了三个版本的erase。与顺序容器一样，我们可以通过传递给erase一个迭代器或一个迭代器对来删除一个元素或者一个元素范围。

关联容器提供一个额外的erase操作，它接受一个key_value参数。此版本删除所有匹配给定关键字的元素（如果存在的话），返回实际删除的元素的数量。

```C++
// 删除一个关键字，返回删除的元素数量
if(word_count.erase(removal_word))
  cout << "OK: " << removal_word << "removed\n";
else
  cout << "oops: " << removal_word << "not found!\n";
```

对于保存不重复关键字的容器，erase的返回值总是0或1。若返回值为0，则表明想要删除的元素并不在容器中

对允许重复关键字的容器，删除元素的数量可能大于1：

```C++
auto cnt = authors.erase("Barth, John");
// cnt的值为2
```

### 11.3.4 map的下标操作

![](https://cdn.pkubailu.cn/img/11.6.png)

map和unordered_map容器提供了下标运算符和一个对应的at函数。set类型不支持下标因为set中没有与关键字相关联的”值“。元素本身就是关键字。我们不能对一个multimap或一个unordered_multimap进行下标操作，因为这些容器中可能有多个值与一个关键字相关联。

类似我们用过的其他下标运算符，map下标运算符接受一个索引（即，一个关键字），获取与此关键字相关联的值。但是，与其他下标运算符不同的是，如果关键字并不在map中，会为它创建一个元素并插入到map中，关联值将进行值初始化。

```C++
// 例如，如果我们编写如下代码
map <string, size_t> word_count; // empty map
word_count["Anna"] = 1;
```

将会执行如下操作

- 在word_count中搜索关键字为Anna的元素，未找到。
- 将一个新的关键字—值对插入到word_count中。关键字是一个const string，保存Anna。值进行值初始化，在本例中意味着值为0.
- 提取出新插入的元素，并将值1赋予它。

由于下标运算符可能插入一个新元素，我们只可以对非const的map使用下标操作。

#### 使用下标操作的返回值

map的下标运算符与我们用过的其他下标运算符的另一个不同之处是其返回类型。通常情况下，解引用一个迭代器所返回的类型与下标运算符返回的类型是一样的。但对map则不然：当对一个map进行下标操作时，会获得一个mapped_type对象；当解引用一个map迭代器时，会得到一个value_type对象。

与其他下标运算符相同的是，map的下标运算符返回一个左值。由于返回的是一个左值，所以我们既可以读也可以写元素：

```C++
cout << word_count["Anna"]; // 用Anna作为下表提取元素；会打印出1
++word_count["Anna"]; // 提取元素，将其增1
cout << word_count["Anna"];
```

> Note! 与vector和string不同，map的下标运算符返回的类型与解引用map迭代器得到的类型不同。

有时只是想知道一个元素是否已在map中，但在不存在的时候并不想添加元素。在这种情况下就不能使用下标运算符。

### 11.3.5 访问元素

关联容器提供多种查找一个指定元素的方法，应该使用哪个操作依赖于我们要解决什么问题。如果我们所关心的只不过是一个特定元素是否已在容器中，可能find是最佳选择。对于不允许重复关键字的容器，可能使用find还是count没什么区别。但对于允许重复关键字的容器，count还会做更多的工作：如果元素在容器中，它还会统计有多少个元素有相同的关键字。如果不需要计数，最好使用find：

```C++
set<int> iset = {0,1,2,3,4,5};
iset.find(1); // 返回一个迭代器，指向key == 1 的元素
iset.find(11); // 返回一个迭代器，指向iset.end()
iset.count(1); // 返回1
iset.count(11); // 返回0
```

![](https://cdn.pkubailu.cn/img/11.7-1.png)

![](https://cdn.pkubailu.cn/img/11.7-2.png)

#### 对map使用find代替下标操作

对map和unordered_map类型，下标运算符提供了最简单的提取元素的方法。但是，使用下标运算符有一个严重的副作用：如果关键字还未在map中，下标操作会插入一个具有给定关键字的元素。

但有时，我们只是想知道一个给定关键字是否在map中，而不想改变map。这种情况下，应该使用find。

#### 在multimap或multiset中查找元素

在允许重复关键字的容器中查找一个元素其过程较为复杂：在容器中可能有很多元素具有给定的关键字。如果一个multimap或multiset中有多个元素具有给定关键字，则这些元素在容器中会相邻存储。

![](https://cdn.pkubailu.cn/img/在multimap中查找元素.png)

> Note! 当我们遍历一个multimap或multiset时，保证可以得到序列中所有具有给定关键字的元素。

#### 一种不同的，面向迭代器的解决办法

我们还可以用lower_bound和upper_bound来解决此问题。如果关键字在容器中，lower_bound返回的迭代器将指向第一个具有给定关键字的元素，而upper_bound返回的迭代器则指向最后一个匹配给定关键字的元素之后的位置。如果元素不在multimap中，则lower_bound和upper_bound会返回相等的迭代器——指向一个不影响排序的关键字插入位置。因此，用相同的关键字调用lower_bound和upper_bound会得到一个迭代器范围，表示所有具有该关键字的元素的范围。

当然，这两个操作返回的迭代器可能是容器的尾后迭代器。

> lower_bound返回的迭代器可能指向一个具有给定关键字的元素，但也可能不指向。如果关键字不在容器中，则lower_bound会返回关键字的第一个安全插入点——不影响容器中元素顺序的插入位置

```C++
// 重写前面的程序
for(auto beg = authors.lower_bound(search_item), end = authors.upper_bound(search_item); beg != end; ++beg)
  cout << beg->second << endl; // 打印每个题目
```

#### equal_range 函数

解决此问题的最后一种方法是三种方法中最直接的：不必再调用upper_bound和lower_bound，直接调用equal_range即可。此函数接受一个关键字，返回一个迭代器pair。若关键字存在，则第一个迭代器指向第一个与关键字匹配的元素，第二个迭代器指向最后一个匹配元素之后的位置。若未找到匹配元素，则两个迭代器都指向关键字可以插入的位置。

```C++
for(auto pos = authors.equal_range(search_item); pos.first != pos.end; ++pos.first)
  cout << pos.first->second << endl;
```

## 11.4 无序容器

新标准定义了4个无序关联容器，这些容器不是使用比较运算符来组织元素，而是使用一个哈希函数和关键字类型的==运算符。在关键字类型的元素没有明显的序关系的情况下，无序容器是非常有用的。在某些应用中，维护元素的序代价非常高昂，此时无序容器也很有用。

> 如果关键字类型固有就是无序的，或者性能测试发现问题可以用哈希技术解决，就可以使用无序容器。

### 11.4.1 使用无序容器

除了哈希管理操作之外，无序容器还提供了与有序容器相同的操作。这意味着我们曾用于map和set的操作也能用于unordered_map和unordered_set。

因此，通常可以用一个无序容器替换对应的有序容器，反之亦然。但是，由于元素未按顺序存储，一个使用无序容器的程序的输出（通常）会与使用有序容器的版本不同。

### 11.4.2 管理桶

无序容器在存储上组织为一组桶，每个桶保存零个或多个元素。无序容器使用一个哈希函数将元素映射到桶。为了访问一个元素，容器首先计算元素的哈希值，他指出应该搜索哪个桶。容器将具有一个特定哈希值的所有元素都保存在相同的桶中。如果容器允许重复关键字，所有具有相同关键字的元素也都会在同一个桶中。因此，无序容器的性能依赖于哈希函数的质量和桶的数量和大小。

无序容器提供了一组管理桶的函数。这些成员函数允许我们查询容器的状态以及在必要时强制容器进行重组。

![](https://cdn.pkubailu.cn/img/11.8.png)

### 11.4.3 无序容器对关键字类型的要求

默认情况下，无序容器使用关键字类型的==运算符来比较元素，他们还使用一个hash<key_type>类型的对象来生成每个元素的哈希值。标准库为内置类型（包括指针）提供了hash模板。还为一些标准库类型，包括string和智能指针类型定义了hash。因此，我们可以直接定义关键字是内置类型（包括指针）、string还是智能指针类型的无序容器。

但是，我们不能直接定义关键字类型为自定义类类型的无序容器。与容器不同，不能直接使用哈希模板，而必须提供我们自己的hash模板版本。

![](https://cdn.pkubailu.cn/img/无序容器重载hash.png)

如果我们的类定义了==运算符，则可以只重载哈希函数：

```C++
// 使用FooHash 生成哈希值； Foo必须有==运算符
unorder_set<Foo, decltype(FooHash)*> fooSet(10, FooHash);
```

## 11.5 总结

1. 关联容器不支持顺序容器的位置相关的操作，例如push_front、push_back
2. 对于有序容器——map、multimap、set、multiset，关键字类型必须定义元素比较的方法
3. 为了指定使用自定义的操作，必须在定义关联容器类型时提供此操作的类型。如前所述，用尖括号指出要定义哪种类型的容器，自定义的操作类型必须在尖括号中紧跟着元素类型给出。
4. 在尖括号中出现的每个类型，就仅仅是一个类型而已。当我们创建一个容器（对象）时，才会以构造函数参数的形式提供真正的比较操作（其类型必须与在尖括号中指定的类型相吻合）。
5. 与其他标准库类型不同，pair的数据成员是public的。两个成员分别命名为first和second
6. 必须记住，一个map的value_type是一个pair，我们可以改变pair的值，但不能改变关键字成员的值
7. 与不能改变一个map元素的关键字一样，一个set中的关键字也是const的
8. 由于下标运算符可能插入一个新元素，我们只可以对非const的map使用下标操作
9. 与vector和string不同，map的下标运算符返回的类型与解引用map迭代器得到的类型不同
10. 新标准定义了4个无序关联容器，这些容器不是使用比较运算符来组织元素，而是使用一个哈希函数和关键字类型的==运算符
11. 与容器不同，不能直接使用哈希模板，而必须提供我们自己的hash模板版本
