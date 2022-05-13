---
layout: post
title:  第三章 向量和字符串 
subtitle: c++ primer笔记
tags: [programming, c++, notes] 
comments: true
---


# 第 3 章 字符串、向量和数组
`string` 表示长度可变的字符串，`vector` 表示长度可变的集合。
迭代器是 `string` 和 `vector` 的配套类型，用于访问它们中的元素。
数组是更基础的类型。  




## 3.1 命名空间的 using 声明
用 using 声明（using declaration）使用命名空间的成员更方便。

可以每个 using 声明引入一个成员
```c++
using namespace::name //基本形式
using std::cout; using std::endl; //一行可以放多条语句
```
注：头文件一般不应使用 `using` 声明，可能产生冲突。

另：`using namespace std` 最方便，书里没有提到。



## 3.2 标准库类型 string
必须先包含 string 头文件。
`string` 定义是在命名空间 `std` 中的。

### 3.2.1 定义和初始化
初始化方法
```c++
string s0;
string s1="hiya"; //拷贝初始化
string s2(10, 'c'); //'c'重复十次，直接初始化
```

### 3.2.2 字符串操作
getline() 函数取一整行，遇到换行符结束（换行符也被读进来了），但得到的字符串里不包含换行符。
```c++
getline([输入流], [string对象])
```

empty()方法根据字符串是否为空返回一个对应的布尔值。
```c++
while (getline(cin, line)) //读入整行，跳过空行
    if (!line.empty())
        cout << line << endl; //输出非空行

```

size()方法返回字符串的长度，返回值是 `size_type` 类型的无符号整型数

可以用 `==, !=, <, <=, >, >=` 来比较字符串对象，按照（大小写敏感的）字典顺序。

用 `+, +=` 让两个字符串相加，或让字符串和字面值相加。

### 3.2.3 单独处理 string 对象中的字符
cctype 头文件定义了一组标准库函数
![表 3.3.png](/assets/img/table3.3.png)

注：C++标准库兼容了 C 标准库，文件名由 name.h 变为 cname（加了字母 c）

**范围 for 语句（range for）**：遍历给定序列的每个元素并对每个值执行操作
```c++
for (declaration : expression)
    statement
```
其中，expression 是一个对象，表示一个序列。declaration 负责定义一个变量，用于访问序列元素。每次迭代，declaration 变量会被初始化为序列的下一个元素。

例如：
```c++
string str("some string");
for (auto c : str)
    cout << c << endl;
```

范围 for 语句改变字符串中的字符
必须把循环变量定义成引用类型，它实际上会依次绑定每个元素
```c++
string s("HEllo"); 
for (auto &c : s) //对 s 中的每个字符
    c = toupper(c); //转换成大写形式，改变 s 中字符的值
cout << s << endl;    
```

下标运算符（[ ]）访问字符串某个值。可以把下标类型设为 string:: size_type，确保它不小于 0



## 3.3 标准库类型 vector
表示同类型对象的集合，每个对象都有对应的索引。也常被称作容器（container）。
必须包含头文件 `<vector>`，使用命名空间 `std`。

vector 是一个类模板（class template）（另外还有函数模板），编译器根据模板闯将类或函数的过程叫做实例化（instantiation）。

类模板实例化时要在名字后面跟一对尖括号，提供额外信息
```c++
vector<int> ivec; //ivec保存int类型的对象
vector<vector<string>> file; //file的元素是vector对象
```



### 3.3.1 定义和初始化 vector 对象
一般先创建空 vector，运行时再动态添加元素。
![表 3.4.png](/assets/img/table3.4.png)
注：区分圆括号（构造）和花括号（列表初始化）。
无法列表初始化时，编译器会尝试用默认值初始化 vector 对象。



### 3.3.2 向 vector 对象中添加元素
push_back()方法把一个值变成 vector 对象的尾元素
```c++
vector<int> v2; //空vector对象
for (int i=0; i!=100; ++i)
    v2.push_back(i);  //依次把整数值放到尾端
//v2有0到99的100个元素    
```

insert()方法把一个值或一个序列插入某个位置
```c++
a.insert(a.begin(),"元素")
words.insert(--std::end(words) , std::begin(more), std::end(more));
```



### 3.3.3 其他 vector 操作
![表 3.5.png](/assets/img/table3.5.png)

注：不能用下标形式添加元素，正确方法是用 push_back，尽可能用范围 for 语句 。
因为下标只能访问已存在的元素，否则会产生严重问题。



### 注 vector 删除元素

pop_back()方法删除最后一个元素，改变容器大小。

erase()方法删除指定位置的一个元素或删除指定范围内的元素。
```c++
void pop_back()
iterator erase (const_iterator position);  
iterator erase (const_iterator first, const_iterator last);
```
clear()方法删除所有元素。



## 3.4 迭代器（iterator）介绍
提供对对象的间接访问，其对象是容器中的元素或 string 对象中的字符。
用迭代器可以访问某个元素，也能从一个元素移动到另一个。

### 3.4.1 使用迭代器

有迭代器的类型可以返回迭代器。这些类型都有 begin()和 end()方法。
```c++
auto b = v.begin(), e = v.end(); 
```

其中 begin()方法返回指向第一个元素的迭代器，end()方法返回指向尾元素的下一位置。
end()方法返回的迭代器，称为尾后迭代器，指示的是本不存在的尾后元素，只是标记。
如果容器为空，begin()和 end()返回的都是同一个尾后迭代器。

![表 3.6.png](/assets/img/table3.6.png)

和指针类似，可以通过解引用（\*）获取迭代器指示的元素。

注：泛型编程：
for 循环中使用!=进行判断，在标准库提供的所有容器上都有效。而<只有少数有效。

一般无需知道迭代器的类型。但实际上标准库类型使用 iterator 和 const_iterator。
cbegin()和 cend()可以强制返回 const_iterator 类型。

箭头运算符（- >）把解引用和成员访问两个操作结合在一起。
```c++
it->empty  //含义相同
(*it).empty()
```

例，text 是字符串向量
```c++
for (auto it = text.cbegin());
    it != text.cend() && !it->empty(); ++it)
    cout << *it << endl;
```

注：不能在范围 for 循环中向 vector 对象添加元素。任何可能改变 vector 对象容量的操作都会使迭代器失效。



### 3.4.2 迭代器运算
所有标准库容器都有支持递增运算和\==、!=的迭代器。
但 vector 和 string 的迭代器提供了更多额外运算符。
![表 3.7.png](/assets/img/table3.7.png)



## 3.5 数组

数组也是存放同类对象的容器，但大小不变，不能随意增加元素。



### 3.5.1 定义和初始化内置数组
```c++
类型 名字[维度] = {1，2，3} //维度必须是一个常量表达式
```
不允许用 auto 关键字推断类型。

不允许拷贝和赋值。

### 3.5.2 访问数组元素
和 vector 与 string 一样，数组元素也能用范围 for 语句或下标来访问。

需要遍历所有元素时，用范围for语句是最好的办法。
注意防止下标越界。

### 3.5.3 指针和数组
数组名字等价于一个指向数组首元素的指针。

vector和string的迭代器支持的运算，数组的指针全都支持。

标准库函数begin()和end()与容器中两个同名方法功能类似。
```c++
int ia[] = {0, 1, 2};
int *beg = begin(ia);  //指向首元素的指针
int *last = end(ia);  //指向尾后元素的指针
```

只要指针指向的是数组中的元素（或尾后元素），都可以执行下标运算。因此下标可以是负值（而vector和string必须是无符号类型）。


### 3.5.4 C风格字符串
不方便，极易引发程序漏洞。

定义在cstring头文件：
![table3.8.png](/assets/img/table3.8.png)

（即字符数组，名字是指针）

### 3.5.5 与旧代码的接口

## 3.6 多维数组
严格来说，C++没有多维数组，通常所说的多维数组其实是数组的数组。

初始化时内层的花括号并非必须，可以只用一对。可以只初始化一部分元素。

如果下标运算符数量和维度一样多，返回元素，如果下标运算符更少，将返回内层数组。

要使用范围for语句处理多维数组，除了最内层的循环，其他所有循环的控制变量都应该是引用类型。
```c++
size_t cnt=0;
for (auto &row : ia)
	for (auto &col : row){
		col = cnt;
		++cnt;
	}
```

多维数组名字是指向第一个内层数组的指针。
类型别名可以简化多维数组的指针
