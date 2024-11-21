# Effective C++

## 从C转向C++

- C是一种简单的语言。它真正提供的只有宏、指针、结构、数组和函数。什么问题都靠这五个来解决。

### 条款1：尽量用const和inline而不用#define
- 这个条款最好称为：“尽量用编译器而不用预处理”
- 有两个需要特殊注意，一个是指针，一个是类
- 指针定义时，除了指针所指的类型定义为const，重要的是指针也经常要定以成const。
```C++
// 例如：要在头文件中定义一个基于char*的字符串常量
const char* const authorName = "Scott Meyers";
```
- 关于const的用法，特别是和指针关联的问题，参见条款21.

- 另外，定义某个类(class)的常量一般也很方便，只有一点点不同。要把常量限制在类中，首先要使它成为类的成员；为了保证常量最多只有一份拷贝，还要把它定义为静态成员：
```C++
class GamePlayer {
private:
	static const int NUM_TURNS = 5;  	// constant declaration
	int scores[NUM_TURNS];  			// use of constant
	...
};
```
- 上面的语句是NUM_TURNS声明，而不是定义，所以你还必须在类的实现代码文件中定义类的静态成员：

```C++
const int GamePlayer::NUM_TURNS;  // modatory definition; goes in class impl. file
```

- 旧一点的编译器回不接受这种语法，因为它认为类的静态成员在声明时定义初始值是非法的；而且，类内只允许初始化整数类型（如：int，bool，char等），还只能是常量。

- 上面语法不能使用的情况下，可以在定义时赋初值。
- 但是数组的声明会出错。这时候可以借用enum的方法来解决。
- 这种技术很好地利用了当需要int类型时可以使用枚举类型的原则。
```C++
// 所以GamePlayer也可以像这样来定义
class GamePlayer{
private:
	enum {NUM_TURNS = 5};
	int scores[NUM_TURNS];
}
```

- 可以用inline函数来代替宏的函数替换功能
- 内联函数见条款33
```C++
template<class T>
inline const T& max(const T& a, const T& b)
	{ return a > b ? a : b; }
```
- 顺便说一句，在你打算用模板写像max这样有用的通用函数时，可以看看标准库它们是否已经存在。max就可以后人乘凉

### 条款2：尽量用<iostream>而不是<studio.h>
- 类型安全？
- 简单来说iostream可以重载，类型安全，扩展性比studio.h好。不考虑这些的话，studio.h也不错，因为它更简单。

### 条款3：尽量用new和delete而不用malloc和free
- new可以调用构造函数，new出来的直接就是对象对应的类型，而malloc出来的只是地址，没有对象对应的操作。
- delete可以调用对象的析构函数，如果对象也分配了内存，可以很好地释放。free显然不行。
- new、delete、malloc、free四个混合使用显然结果是不可预测的。
- 不可预测：它可能在开发阶段工作良好，测试阶段工作良好，但也可能会最后在你最重要的客户的脸上爆炸。


### 条款4：尽量使用C++风格的注释

## 内存管理

- 程序创建和销毁会调用构造函数和析构函数
- new和delete也会调用构造和析构函数
- 这把程序搞得很复杂，下面这些条款将告诉你如何避免产生那些普遍发生的问题

### 条款5：对应的new和delete要采用相同的形式

```C++
new string			- 	delete stringPtr1;
new string[100] 	- 	delete[] stringPtr2;
```

- 在写一个包含指针数据成员，并且提供多个构造函数的类时，牢记这一规则尤其重要。因为这样的话，你就必须在所有初始化指针成员的构造函数里采用相同的形式。否则析构函数将采用什么形式的delete呢？

### 条款6：析构函数对指针成员调用delete

- 删除空指针是安全的（因为它什么也没做）。所以，在写构造函数，赋值操作符，或其他成员函数时，类的每个指针成员要么指向有效的内存，要么就指向空，那么你的析构函数里你就可以只用简单地delete掉它们，而不用担心他们是不是被new过。

### 条款7：预先准备好内存不够的情况

```C++
#define NEW(PTP, TYPE)
	try { (PTR) = new TYPE; }
	catch (std::bad_alloc&) {assert(0);}
```
- assert: 见头文件<assert.h> 或 <cassert>。就会发现assert是个宏。这个宏检查传给它的表达式是否非零。如果不是非零值，就会发出一条出错信息并调用abort。
- assert只是在没定义宏NDEBUG的时候，即在调试状态下才这么做。在产品发布状态下，即定义了NDEBUG的时候，assert什么也不做，相当于一条空语句。所以你只能在调试时才能检查断言(assertion)





