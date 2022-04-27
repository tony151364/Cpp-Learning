# 一、《C Primer Plus》第5版
## 第12章
- 如果变量声明中带有const，则不能通过赋值、增量或减量来修改该变量的值。
----------------------------------------

# 二、《C语言程序设计》甘勇
- [ ] 指针进阶
- [x] C预处理
- [ ] 文件


## 第二章 简单C程序设计
### 2.2.3 常量
- C语言的常量分为：
    - 字面常量
    - 宏常量
    - const常量
#### 2.宏常量 
```C
#define 标识符常量 替换文本 
#define PI 3.1415926
```
- 命令编译器将源代码中所有标识符常量替换为替换文本

#### 3.const常量
```C 
const double PI = 3.1415926; 
```
- const常量与变量的定义类似，加上const修饰，告诉编译器，它的值是固定的，不能被改变，编译器会帮你检查，监督。

- 在编译的的时候，由于const定义常量只给出了对应的内存地址，而不是像 #define 给出的是替换文本，所以const定义的常量在程序运行过程中只有一份拷贝，而 #define定义的常量在内存中有若干份拷贝
- const推出的目的正是为了取代宏常量，消除其缺点，继承其优点。

## 第四章   
    题目18.三个字符排序。空格隔开，从小到大。输出也用空格隔开。  
        1.输入：当输入三个字符时，最后的\n会被缓存，下一次会被读入，从而影响输入；  
        解决：%*c 或getchar() 二者均可，目的是消除\n  
        2.算法：三元运算一般比if更加耗内存；我采用的max，min，mid其实是一种笨方法，没有考虑三个元素之间的关系。把简单问题给复杂化了
        解决：采用if语句以及temp进行两两对换，最大值取出后，剩余两个进行一次比较即可得到最小值和中间值。
        使 a<=b<=c,分步就是：  
  ```C
   a<=b  
   a<=c  
   b<=c  
 ```  

## 第五章

类型           |    声明             |    作用域    |    声明周期
---            |   ----              |    ------    |    ------
局部变量       |  内部               | 一对{}内     | 函数语句结束
static局部变量 | static内部         | 一对{}内     | 整个程序运行周期
全局变量       |  第一个函数前       | 整个程序      | 整个程序运行周期
static全局变量 | static第一个函数前 | 当前文件    | 整个程序运行周期


```
题目12 用递归实现任意正整数向八进制的转换
问题：陷入一个误区，以为凡是递归必须要“return”。实际上递归只要直接或间接调用自身函数即可，“return”不是必须的。       
方案：有这个误区是因为大多数递归需要return而已。
```
## 第七章  
    题目：程序7-5统计单词个数
      1.问题：将简单问题复杂化，或者说题目的描述不清楚，存在歧义
      2.算法 ：for(i = 0; str[i] != '\0' ; i++)
                    d = d * 2 + (str[i] - '0');
     这两行可以说是精髓，这个转换太巧妙了，应用了秦九韶算法

题目：下面的字符串逆置函数更巧妙
  ```C
  char *strrev(char *str)
  {
    char *p1, *p2;  
    
    if (! str || ! *str)
        return str;

        for (p1 = str, p2 = str + strlen(str) - 1; p2 > p1; ++p1, --p2)
            {
                *p1 ^= *p2;
                *p2 ^= *p1;
                *p1 ^= *p2;
            }

        return str;
 }
```

## 第八章  
    题目：课后作业9 在一个字符串中删除一个字符;  homeword_test.c的方法更好
 ```C
  1 #include <stdio.h>
  2 
  3 void delete(char *s, char ch)
  4 {
  5     int i, j;
  6 
  7     i = j = 0;
  8 
  9     while(s[j] != 0)
 10     {
 11         if(s[j] != ch)
 12             s[i++] = s[j];
 13 
 14         j++;
 15     }
 16 
 17     s[i] = 0;
 18 }
 19 
 20 void main(void)
 21 {
 22     char str[100], ch;
 23 
 24     printf("input a string\n");
 25     scanf("%s", str);
 26     getchar();
 27 
 28     printf("input ch\n");
 29     scanf("%c", &ch);
 30 
 31     delete(str, ch);
 32     puts(str);
 33 }
 ```
## 第10章 指针进阶
### 10.1 指针与二维数组
#### 10.1.1 用一级指针访问二维数组

### 10.2 指针数组
### 10.3 带参数的main函数
### 10.4 指向函数的指针
- 函数指针是指向函数的指针变量，只不过该指针变量包含的是一个函数在内存中的地址
- 有了指向函数的指针变量后，可用该指针变量调用函数，就如同指针变量引用其他类型的变量一样
- 指向函数的指针主要的两个用途：调用函数 和 做函数的参数

#### 1.函数指针的声明和使用
```C
函数类型 (*指针变量名) (形参列表);
```
- 1.“(\*指针变量名)”中的括号不能省略，若省略则整体成为一个函数声明，声明是一个函数，该函数的返回数据类型是指针
- 2.“函数类型”表示指针变量所指向的函数的返回值类型
- 3.“形参列表”表示指针变量指向的函数所带的参数列表，如
```C
int func(int x);  // 声明一个函数
int (*f)(intx)  // 声明一个函数指针
f = func;  // 将函数func的首地址赋给f
```
- 赋值时func()不用带括号，也不带参数，因为函数名func代表函数的首地址。
```C
// 程序10-7：函数指针的声明与使用
#include<stdio.h>

int max(int x, int y);
int main(void)
{
	// 声明ptr是一个指针变量，指向带两个int类型的形参，返回值是int类型的函数
	int (*ptr) (int, int);
	int a, b, c;

	// ptr指向max函数，函数max()必须是带两个int类型的形参，返回值是int类型
	ptr = max;
	scanf("%d%d", &a, &b);
	// ptr指向函数max(), *ptr是对函数max的引用
	c = (*ptr)(a, b);
	printf("a=%d, b=%d, max=%d\n", a, b, c);
	return 0;
}
int max(int x, int y)
{
	return (x > y ? x : y);
}
```
- 实际上ptr和max都指向同一个入口地址，不同的是函数名是一个指针常量，而ptr是一个指针变量
#### 2.函数指针做参数

#### 3.函数指针数组

## 第11章 C预处理
### 11.1预处理器的工作原理
- #include 指令告诉预处理器打开一个特定的文件，将其内容作为正在编译的文件的一部分包含进来。
- 对于 #include<stdio.h> 预处理器通过引入stdio.h 的内容来响应#include指令，预处理器也删除了#define指令，并且替换了改文件中稍后出现在任何位置上的PI。

### 11.2 预处理指令
- 1.常见的预处理指令：  
    - （1）宏定义。 #define定义一个宏，#undef指令删除一个宏定义
    - （2）文件包括。#include导致一个指定文件的内容被包含在程序中
    - （3）条件编译。#if、#ifdef、#ifndef、#elif、#else 和 #endif 指令可以根据测试的条件来确定是将一段文本包含到程序中还是将其排除在程序之外。  
- 2.预处理指令需要遵循以下几条规则  
    - （1）指令都以#开始。#后面是指令名。接着是指令所需要的其他信息
    - （2）在指令的符号之间可以插入任意数量的空格和水平制表符。如 ``` #define    N 10 ```
    - （3）指令总是以换行符作为结尾，除非明确地指出要延续。如果要延续，就必须在当前行的行尾用'\'字符
    - （4）指令可以出现在程序中的任何地方。但是我们通常会把#define 和 #include指令放在文件的开始
    - （5）注释可以与指令放在同一行

### 11.3 #define 预处理指令
- #define预处理命令用来创建符号常量（以符号形式表示的常量）和宏（以符号形式定义的操作）
#### 11.3.1 符号常量
- 格式： ``` #define 标识符 替换文本 ```
- 编译时，预处理器会把程序中的标识符用替换文本来代替
- 习惯上，只用大写字母和下划线来为符号常量命名

```C
// 程序11-1 符号常量的使用
#include <stdio.h>
#define PI 3.14

int main (void)
{
    double r, area, circle;
    
    sanf("%lf", &r);
    area = PI * r * r;
    circle = 2 * PI * r;
    printf("area=%.2f, circle=%.2f\n", area, circle);
    return 0;
}
```
- 用#define来为常量命名的优点：
    - （1）程序会更易读
    - （2）程序便于修改。仅需要改变一个宏定义，就可以改变整个程序中出现的所有的该变量的值
    - （3）可以帮助避免前后不一致或键盘输入错误
    - （4）可以对类型重命名。例如可以通过重命名int来创建一个布尔类型``` #define BOOL int ```（就像数据结构中的ElementType） 

#### 11.3.2 带参数的宏
- 带参数的宏不仅要进行简单的字符串替换，还要进行参数替换 ``` #define   宏名(x1, x2, ... Xn)   替换列表```，宏名也是标识符，要符合标识符的定义。
- 当预处理器遇到带参数的宏时，会将宏定义存储起来以便以后使用。在后面的程序中，如果出现了“宏名(x1, x2, ... Xn)”格式的宏调用，预处理器会使用替换列表替代。
```C
// 程序11-2：输出水仙花数，程序使用了带参宏定义
#include <stdio.h>
#define f(a)  (a) * (a) * (a)   // 有用圆括号将宏的形参括起来

int main (void)
{
    int i, x, y, z;
    
    for(i = 100; i < 1000; i++)
    {
        x = i % 10; y = i / 10 % 10; z = i / 100;
        if(f(x) + f(y) + f(z) == i)
            printf("%d\n", i);
    }
    
    return 0;
}
```

```c
// 11-3 带参数的宏定义实现简单的函数功能
#include <stdio.h>
#define MAX(a, b)  (a) > (b) ? (a) : (b)
#define SQR(x)  (x) * (x)

int main (void)
{
    int x, y;
    scanf("%d%d", &x, &y);
    x = MAX(x, y);  // 宏调用
    y = SQR(x);  // 宏调用
    printf("%d %d\n", x, y);
    return 0;
}
```
- **注意**： 宏与函数是两个不同的概念，宏可以实现简单的函数功能

- 使用带参数宏代替真正函数的优点：
    - 程序可能稍微快点。因为……
    - 良好的可读性
    - 宏更“同样”
- 缺点：
    - 编译后的代码通常会变多
    - 宏参数没有类型检查
    - 宏可能会不止一次的计算它的参数  
- 符号常量和带参数的宏的定义可以用#undef预处理命令来撤销。所以他们的作用域是从它们被定义开始，一直到它们被#undef命令撤销为止，或者到文件末尾为止。

### 11.4 文件包含
#### 11.4.1 多文件程序
- 保存有一部分程序的源文件称为程序文件模块
- 程序分为多个文件的优点：
    - 把相关的函数和变量分组放在同一个文件中，可以使程序的结构清晰。
    - 可以分别对每一个源文件进行编译。当程序很大且需要频繁改变的话，可以节约时间。
    - 把函数分组放在不同的源文件中有利于复用。

#### 11.4.2 include指令
- #include指令告诉预处理器打开指定的文件，并且把该文件的内容插入到当前文件中。
- 按照这种方式包含的文件成为头文件。头文件包含了其中所有函数的函数原型、函数所需的各种数据类型和常量的定义，扩展名为.h
- 两种书写格式：
    - 格式1：#include "需要包含的文件名"
    - 格式2：#include <需要包含的文件名>
- 两种书写的区别在于查找预包含文件的起始位置不同：
    - 格式1：程序员自己定义的头文件通常用格式1。预处理程序会从待编译文件所在的目录开始查找预包含的文件，如果找不到再到预先指定的编译器和系统目录中找
    - 格式2：一般用于标准函数库的头文件。预处理程序会按照一种依赖于系统实现的方式，通常是预先指定的编译器和系统目录中进行查找
    - **注**：自己写的文件用格式2预处理器会找不到

#### 11.4.3 文件之间如何共享
- 叙述看书本，很清晰
- 此外，volumn.c也应该包含volumh.h文件，这是为了编译器可以检查volumn.h文件中的函数原型是否与volumn.c文件中的函数定义相一致

```
求某物体体积：
该物体可能是球体、圆柱体或者圆锥体。根据键盘输入的整数值来决定该物体形状
```
```C
// 自定义头文件：volumn.h
#define PI 3.14
double VolBall(double r);  // 函数原型
double VolCylind(double r, double h);  // 函数原型
double VolCone(double r, double h);  // 函数原型
```
```C
// 自定义源文件：volumn.c
double VolBall(double r)
{
    return (4.0/3.0) * PI * r * r * r;
}
double VolCylind(double r, double h)
{
    return PI * r * r * h;
}
double VolCone(double r, double h)
{
    return PI * r * r * h / 3;
}
```
```C
// 程序11-5：用多个文件构建程序
#include <stdio.h>
#include "volumn.h"
int main(void)
{
    int sel;
    double r, h, vol'
    
    scanf("%d", &sel);
    
    // 根据sel的值来判断物体的形状，调用相应的函数求出物体的体积
    switch(sel) {
        case 1: scanf("%lf", r); vol=VolBall(r); break;
        case 2: scanf("%lf%lf", r, h); vol=VolCylind(r, h);  break;
        case 3: scanf("%lf%lf", r, h); vol=VolCone(r, h);  break;
    }
    
    printf("%.2f\n", vol);
    return 0;
}
```
- 源文件也需要编译

### 11.5 条件编译
- 条件编译预处理是指按照一定的条件有选择地将某个源程序段包括或不包括到源文件中，从而使编译程序能够对用户的源程序有选择的生成满足一定条件的目标程序，利用条件编译功能能方便的编写可移植的程序和便于调试的程序。
#### 1.#if指令
- #if、#elif指令、#else指令和#endif指令用于控制有条件地编译，即仅当特定条件满足时才对源代码块进行编译。
```C
#if 常量表达式
    正文
[#elif 常量表达式 正文]
...
[#else 正文]
#endif
```
- 如果常量表达式没有事先赋值，#if指令会认为它的值是0
##### 试试看：用#if...#endif来帮助调试
- 在程序调试过程中，我们想要程序输出特定变量的值，可以借助这些变量的值来查找错误来源，这是就会将pirntf()函数调用添加到程序中的重要部分。当找到错误后，经常需要保留这些printf函数调用，以备以后使用。可以通过条件编译来保留这些printf调用，但是要让编译器忽略掉它们。
```C
// 例如：要输出变量X和y的值，首先定义一个符号常量并给其一个非零值；
#define FLAG 1;
// 然后，在pirntf函数调用前后加上#if和#endif
#if FLAG
    printf("value of x is %d\n", x);
    printf("value of y is %d\n", y);
#endif
```
- 如果FLAG非零，处理器会保留这两个printf()函数调用(#if和#endif这2行会在预处理中消失)
- 当调试结束后，不需要这两个函数调用时，设FLAG为0，重新编译，预处理器会将这4行都删除

### 2. #ifdef指令和#ifndef指令
- #ifdef指令用于检测标识符**是否已经**被定义为宏。
- #ifndef指令用于检测标识符**是否没有**定义为宏。
```C
#ifdef DEBUG
    代码段
#endif
```
- 仅当DEBUG被定义为宏时，该代码段才会被编译
- 因为头文件自身也可以包含头文件，随着程序的增大，使用的头文件可能越来越多，文件的嵌套可能导致无意间包含同一个头文件多次，这将造成编译错误。
- #ifdef指令和#ifndef指令可防止头文件多次包含
```C
// 头文件：prog1.h
#ifndef DEBUG
    #define PROG 1
#endif
// 头文件正文
```
- 条件编译对于调试非常方便，但其应用并不局限于此。下面是一些常见的应用：
    - 编写在多态机器或多种操作系统之间可移植的程序
    - 编写可以用不同的编译器编译的程序
    - 为宏提供默认定义
    - 临时屏蔽包含注释的代码
    - 保护头文件以避免重复包含   

## 第12章 文件