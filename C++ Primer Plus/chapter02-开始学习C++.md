## 第 2 章 开始学习C++
### 2.1进入C++
```C++
// 2.1 myfirst.cpp -- displays a message

#include <iostream>
using namespace std;  // 名称空间

int main()
{  // start of function body
    cout << "Come up and C++ me some time.";
    cout << endl;
    cout << "Your won't regret it!" << endl;
    cin.get();
    
    return 0;  // terminate main()
}  // end of function body
```
#### 2.1.1 函数头
- 函数头描述了函数与调用它的函数之间的接口
- main()通常被启动代码调用，而启动代码是由编译器添加到程序中的，是程序和操作系统之间的桥梁。事实上，该函数体描述的是main()和操作系统之间的接口。
- 空括号意味着main()不接受任何参数
- int main(void) 与 int main() 在C++中等效，在C中，让括号空着意味着对是否接受参数保存沉默
- main() 末尾没有返回语句时，默认main()函数以 return 0; 结尾。这只适用于main()函数
- 函数体指出函数应做什么样的计算机指令
- 函数名其实也是一个地址


#### 2.1.3 C++预处理器和iostream文件
- 预处理器的典型操作：在源代码被编译之前，替换或添加文本
- 原始文件没有被修改，而是将源代码文件和iostream组合成一个复合文件，编译的下一阶段将使用该文件

#### 2.1.4 头文件名
- C++头文件没有扩展名。
- 有些C头文件被转化为C++头文件。如C++版本的 math.h 为 cmath
- C++新式风格，使用 namespace std

#### 2.1.5 名称空间
```C++
using namespace std;  // using编译指令
```
- cout变量实际上是 std::cout, endl实际上是std::endl;
- using 编译指令使得std命名空间中的所有名称都可用，这是一种偷懒的做法。

#### 2.1.6 使用cout进行C++输出
- <<符号表示该语句把这个字符串发送给cout；该符号指出了信息流动的路径。
- cout是一个预定义的对象，知道如何显示字符串、数字和单个字符等（对象是类的特点实例，而类定义了数据的存储和使用方式）
- 插入运算符 ( << )，它可以将其右侧的信息插入到流中。

```C++
cout << "Come up and C++ me some time.";  // 它 (<<) 将一个字符串插入到数据流中
```
- 插入运算符看上去像一个左移运算符。这是一个运算符重载的例子。
- endl(end line)是一个特殊的C++符号，表示一个重要的概念：重起一行；导致屏幕光标移动到下一行开头。
- endl 等对于cout来说有特殊含义的特殊符号被称为控制符(manipulator)

```C++
cout << "pluto is a drawf planet.\n";
cout << "pluto is a drawf planet" << endl;
```
- 一个差别：endl确保程序运行前刷新输出（将其立即显示在屏幕上）；而"\n"不能提供这样的保证

#### 2.1.7 C++源代码的格式化
- 每条语句占一行
- 每个函数都有一个开始花括号和一个结束花括号，这两个花括号各占一行
- 函数中的语句都相对花括号缩进。
- 与函数名词相关的圆括号周围没有空白

### 2.2 C++语句
#### 2.2.1 声明语句和变量
```C++
#include <iostream>
using namespace std;

int main()
{
    int carrots;  // 编译器负责分配int大小的内存，并标记为carrots

    carrots = 25;
    cout << "I have";
    cout << carrots;
    cout << "carrots";
    cout << endl;
    carrots = carrots - 1;
    cout << "Crunch, crunch. Now I hava " << carrots << " carrots." << endl;

    return 0;
}
```
- C++通常的做法是：在首次使用之前声明它。缺点就是无法对函数使用了那些变量一目了然

#### 2.2.2 赋值语句
- C++ 与 C 不同的特性在于——可以连续使用赋值运算符（python也可以）。
```C++
// 以下语句是合法的
int steinway;
int baldwin;
int yammaha;
yamaha = baldwin = steinway = 88;
```
#### 2.2.3 cout新花样
- 对于 cout << carrots;实际上，这将两个操作合二为一了。首先，cout将carrots替换为当前值25；然后，把值转换为合适的输出字符。
- 在打印之前，cout必须将整数形式的数字转换为字符串形式。cout很聪明，知道carrots是一个需要转换的整数。
- 相比于老式C语言的printf，cout更聪明。这源自于C++面向对象的特性。实际上，C++插入运算符(<<)将根据其后的数据类型相应地调整其行为，这是一个运算符重载的例子。

### 2.3 其他 C++语句
```C++
#include <iostream>
using namespace std;

int main()
{
    int carrots;

    cout << "How many carrots do you have?" << endl;
    cin >> carrots;  // C++ input
    cout << "Here are too more. ";

    carrots = carrots + 2;

    cout << "Now you have " << carrots << " carrots." << endl;
    return 0;
}
```
#### 2.3.1 使用 cin 
- cin >> carrots; 从这条语句可知，信息从cin流向carrots。可以看做流入程序的字符流

#### 2.3.1 使用cout进行拼接
```C++
cout << "Now you have " << carrots << " carrots." << endl;

cout << "Now you have " 
     << carrots 
     << " carrots." 
     << endl;
```
- 这是由于C++的自由格式规则将标记间的换行符和空格看作是可相互替换的。当代码很长，限制输出的风格时，最后一种技术很方便。

#### 2.3.3 类简介
- 类是用户定义的一种数据类型。要定义类，需要描述它能够表示什么信息和可对数据执行哪些操作。
- 类之于对象就像类型之于变量。
- 也就是说，类定义描述的是数据格式及其用法，而对象则是根据数据格式规范创建的实体。
- cout是一个ostream类对象。ostream类定义(iostream文件的另一个成员)描述了ostream对象表示的数据以及可以对它执行的操作，如将数字或字符串插入到输出流中。同样，cin是一个istream类对象，也是在iostream中定义的。

- 类描述了一种数据类型的全部属性（包括可使用它执行的操作），对象是根据这些描述创建的实体

- 类描述了可对类对象执行的所有操作。要对特定对象执行这些允许的操作，需要给该对象发送一条消息。例如，如果希望cout对象显示一个字符串，应向它发送一条消息，告诉它，“对象！显示这些内容！”

- C++提供两种发送消息的方式：
    - 一种方式是使用类方法（本质上就是稍后将介绍的函数调用）  
    - 另一种是重新定义运算符，cin，cout采用的就是这种发生


### 2.4 函数
#### 2.4.1 使用有返回值的函数
- 参数是发送给函数的信息，返回值是从函数中发送回去的值
- 在使用函数之前，C++编译器必须知道函数的参数类型和返回值类型。
- C++程序中应当为程序中使用的每个函数提供原型。函数原型之于函数，就像变量声明之于变量。
- 不要混淆函数原型和函数定义。原型只描述函数接口。也就是说，它描述的是发送给函数的信息和返回的信息。
- C和C++中将原型和定义这两项特性分开了。库函数包含了函数的编译代码，而头文件中则包含了原型。

```C++
// 2.4 sqrt.cpp -- using the sqrt() function
#include <iostream>
#include <cmath>
using namespace std;

int main()
{
	double area;
	cout << "Enter the floor area, in square feet, of your home: ";
	cin >> area;

	double side;
	side = sqrt(area);
	cout << "That's the equivalent of a square " << side
		<< " feet to the side." << endl;
	cout << "How fascinating!" << endl;
	return 0;
}
```
- 编译器编译程序时，必须在库文件搜索您使用的函数。
```C++
// C++运行在创建变量时对她进行赋值，因此可以这样做
double side = sqrt(area);
```
#### 2.4.2 函数变体
- 一些函数不接收任何参数。有一个C库，包含一个rand()函数，该函数不接受任何参数，并返回一个随机整数。该函数原型如下：``` int rand(void); ```
- 关键字void明确指出，该函数不接收任何参数。如果省略void，让括号为空，则C++将其解释为一个不接受任何参数的隐式声明。可以这样使用函数：``` myGuess = rand(); ```

#### 2.4.3 用户定义的函数
- 对于库函数，在使用之前必须提供其原型，通常把原型放到main()定义之前。
- 自己定义的简单方法就是放到main()前面
```C++
// 程序清单2.5 ourfunc.cpp -- defining your own function
#include <iostream>
void simon(int);  // function prototype for simon()
using namespace std;

int main()
{
	simon(3);  // call the simon() function
	cout << "Pick an integer: ";
	int count;
	cin >> count;
	simon(count);
	cout << "Done!"  << endl;
	return 0;
}

void simon(int n)  //   define the simon() function
{
	cout << "Simon says touch your toes " << n << " times."  << endl;
}
```
- 和C一样，C++不允许将函数定义嵌套在另一个函数中。每个函数定义都是独立的，所有函数的创建都是平等的
- 不能将关键字用作变量名和函数名。在一个程序中将同一个名称(比如cout)用作对象名和变量名会把编译器搞糊涂。也就说在不使用cout对象进行输出的函数中，可以将cout用作变量名，但不能在同一个函数中同时将cout用作对象名和函数名。

#### 2.4.4 用户定义的有返回值的函数
- 略

#### 2.4.5 在多函数程序中使用using编译指令(需要记忆！！！)
- 就是把编译指令放在函数外边，没啥新奇的。
- 函数中使用了cout，因此需要能够访问于名称空间std中的cout定义
- 总之，让程序能够访问名称空间std的方法有多种，下面就是其中的4种
	- 将 ``` using namespace std; `` 放在函数定义之前，让文件中所有的函数都能使用名称空间std中所有的元素
	- 将 ``` using namespace std; `` 放在特定的函数定义中，让该函数能够使用名称空间std中的所有元素。
	- 在特定的函数中使用类似 ``using std::cout``` 这样的编译指令，而不是 ``` using namespace std;``` ，让该函数能够使用指定的元素，如cout。
	- 完全不使用编译指令using，而在需要使用名称空间std中的元素时，使用前缀std::，如下所示：``` std::cout  << "I'm using cout and end1 from the std namespace"  << std:: endl; ```

### 2.5 总结
- 声明语句：定义函数中使用的变量的名称和类型。
- 赋值语句：使用赋值运算符（=）给变量赋值。
- 消息语句：将消息发送给对象，激发某种行动。
- 函数调用：执行函数。被调用的函数执行完毕后，程序返回导函数调用语句后面的语句。
- 函数原型：声明函数的返回值类型、函数接受的参数数量和类型。
- 返回语句：将一个值从被调用的函数那里返回到调用函数中。
- ostream 类定义的插入运算符(<<)，istream定义的抽取运算符(>>)

