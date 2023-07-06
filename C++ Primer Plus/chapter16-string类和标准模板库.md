# 第 16 章 string类和标准模板库
## 本章内容包括
- 标准C++ string类
- 模板auto_ptr、unique_ptr 和 shared_ptr
- 标准模板库（STL）
- 容器类
- 迭代器
- 函数对象（functor）
- STL算法
- 模板initializer_list

### 小结
- 本章介绍一些其他可重用代码，它们将给编程工作带来快乐
- 介绍“智能指针”模板类，它们让管理动态内存更容易；
- 接下来介绍标准模板库（STL），它是一组用于处理各种容器对象的模板。STL演示了一种编程范式——泛型编程；
- 最后，本章将介绍C++11新增的模板initializer_list，它让您能够将初始化列表语法用于STL对象

## 16.1 string类
- string类由头文件string支持的
- 注意，头文件string.h 和 cstring是支持对C-风格字符串进行操纵的C字符函数库，但不支持string类

### 16.1.1 构造字符串
- string的7个构造函数和C++11新增的两个构造函数
- [ ] 使用构造函数时都进行了简化，即隐藏了这样一个事实：string实际上是模板具体化basic_string<char>的一个typedef，同时省略了与内存管理相关的参数（这将在本章后面和附录F中讨论）
- size_type 是一个依赖于实现的整型
- string类将``` string::npos ```定义为字符串的最大长度，通常为unsigned int的最大值

```C++
// 16.1 str1.cpp -- introducing the string class
#include <iostream>
#include <string>

int main()
{
	using namespace std;

	string one("Lottery Winner!");
	cout << one << endl;  // overloaded << 

	string two(20, '$');
	cout << two << endl;

	string three(one);
	cout << three << endl;

	one += "Oops!";  // overloaded +=
	cout << one << endl;

	two = "Sorry! That was ";
	three[0] = 'P';

	string four;
	four = two + three;  // overloaded +, =
	cout << four << endl;

	char alls[] = "All's well that ends well";
	string five(alls, 20);
	cout << five << "!\n";

	string six(alls + 6, alls + 10);
	cout << six << ", ";

	string seven(&five[6], &five[10]);
	cout << seven << "...\n";

	string eight(four, 7, 16);
	cout << eight << " in motion!" << endl;
	return 0;
}
```
#### 1.程序说明
- [ ] 第6个构造函数的解释

#### 2.C++11 新增的构造函数

### string类输入

```C++

```
```C++

```
















## 16.2 智能指针模板类

## 16.3 标准模板库

## 16.4 泛型编程

## 16.5 函数对象

## 16.6 算法
