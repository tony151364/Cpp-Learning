## 5.1 for循环
```C++
// 5.1 forloop.cpp -- introducing the for loop
#include <iostream>
int main()
{
	using namespace std;
	int i;
	for (i = 0; i < 5; i++)
		cout << "C++ knows loops.\n";
	cout << "C++ knows when to stop.\n";
	return 0;		
}
```
### 5.1.1 for循环的组成部分
```C++
for (initialization; test-expression; update-expression)
  body;
```
- C++并没有将test-expression的值限制为只能为真或假。可以使用任意表达式，C++将把结果强制转换为bool类型。因此，值为0的表达式将被转换为bool值false，导致循环结束。如果表达式的值为非零，则被强制转换为bool值true，循环将继续进行。
```C++
// 5.2 num_test.cpp -- use numeric test in for loop
#include <iostream>
int main()
{
	using namespace std;
	cout << "Enter the starting countdown value: ";
	int limit;
	cin >> limit;
	int i;
	for (i = limit; i; i--)  // quits when i is 0
		cout << "i = " << i << "\n";
	cout << "Done now that i = " << i << "\n";
	return 0;		
}
```
- **提示**：C++常用方式是，在for和括号之间加上一个空格，而省略函数名与括号之间的空格。其他语句也是这样，这是从视觉上将控制语句与函数调用区分。
#### 1.表达式和语句
- 任何值 或 任何有效的值 和 运算符的组合都是表达式。在C++中每个表达式都有值，表达式是值或值与运算符的组合
  - 10是值为10的表达式；28 * 20是值为560的表达式。x = 20是值为20的表达式
  - 由于赋值表达式有值，可以编写这样的语句``` maids = (cooks = 4 ) + 3; ```。maids的值为7 
```C++
// 5.3 express.cpp -- values of expressions
#include <iostream>
int main()
{
	using namespace std;
	int x;

	cout << "The expression x = 100 has the value ";
	cout << (x = 100) << endl;
	cout << "Now x = " << x << endl;
	cout << "The expression x < 3 has the value ";
	cout << (x < 3) << endl;
	cout << "The expression x > 3 has the value ";
	cout << (x > 3) << endl;
	cout.setf(ios_base::boolalpha);  // a newer C++ feature
	cout << "The expression x < 3 has the value ";
	cout << (x < 3) << endl;
	cout << "The expression x > 3 has the value ";
	cout << (x > 3) << endl;
	return 0;
}
```
- << 运算符的优先级比表达式中使用的运算符高，因此代码使用括号来获得正确的运算顺序
- 通常，cout在显示bool值之前将它们转换为int，但cout.setf(ios::boolalpha)函数调用设置了一个标记，该标记命令cout显示true和false，而不是1和0；
- **从表达式到语句的转变很容易，只要加分号即可**。所有的表达式都可以成为语句，但不一定有编程意义。```C++ rodents + 1;   // vaild but useless, statement```（智能编译器甚至能跳过这条语句）

#### 2.非表达式和语句
- 任何表达式加上分号都可以成为语句，但是从语句中删除分号，不一定能将它转换为表达式。
```C++
int toad;  // 去掉分号:int toad并不是表达式，因为它没有值
eggs = int toad * 1000;  // invalid, not an expression
cin >> int toad;  // can't combine declaration with cin
// 同样不能把for循环赋给变量，因为for循环不是表达式
int fx = for (i = 0; i < 4; i++)
     cout >> i;  // not possible
```
#### 3.修改规则
- C++增加了一些新特性，如在初始化部分声明变量。但是与原来定义冲突了。于是for语句语法改了
```C++
for (int i = 0; i < 5; i++)

for (for-init-statement condition; expression)
  statement
```
### 5.1.2 回到for循环
- 阶乘：
```C++
// 5.4 formore.cpp -- more looping with for
#include <iostream>
const int AriSize = 16;   // example of external declaration
int main()
{
	long long factorials[AriSize];
	factorials[1] = factorials[0] = 1LL;  // 注意后面有LL哦
	for (int i = 2; i < AriSize; i++)
		factorials[i] = i * factorials[i - 1];
	for (int i = 0; i < AriSize; i++)
		std::cout << i << "! = " << factorials[i] << std::endl;
	return 0;
}
```
- const的好处：当我们需要改变长度时，如把16改为20，只需要修改第3行，其他都随之改变。所以用const值表示数组中的元素是个好办法

### 5.1.3 修改步长
- 更新表达式可以是任何有效的表达式
```C++
// 5.5 bigstep.cpp -- count as directed
#include <iostream>
int main()
{
	using std::cout;  // a using declaration
	using std::cin;
	using std::endl;
	cout << "Enter an integer: ";
	int by;
	cin >> by;
	cout << "Counting by " << by << "s:\n";
	for (int i = 0; i < 100; i = i + by)
		cout << i << endl;
	return 0;
}
```
- 这个例子用了using声明，而不是using编译指令

### 5.1.4 使用for循环访问字符串

```C++
// 5.6 forstr1.cpp -- using for with a string
#include <iostream>
#include <string>
int main()
{
	using namespace std;
	cout << "Enter a word: ";
	string word;
	cin >> word;

	// display letters in reverse order
	for (int i = word.size() - 1; i >= 0; i--)
		cout << word[i];
	cout << "\nBye.\n";
	return 0;
}
```
### 5.1.5 递增运算符(++) 和递减运算符(--)
- 这两个运算符都有两种变体：前缀(prefix) 和 后缀(postfix)
- 略

### 5.1.6 副作用和顺序点
- **副作用**(side effect)指的是在计算表达式时对某些东西（如存储在变量中的值）进行了修改；
- **顺序点**(dequence pointer)是程序执行过程中的一个点，在这里，进入下一步之前将确保对所有的副作用都进行了评估。C++中分号就是一个顺序点，这意味着程序处理下一条语句之前，赋值运算符、递增运算符和递减运算符执行的所有修改都必须完成。任何完整的表达式末尾都有一个顺序点
```C+++
while (guests++ < 10)
	cout << guests << endl;
// while (guests++ < 10) 是while循环的测试条件，因此该表达式末尾有一个顺序点，所以比较完在对guests加1


y = (4 + x++) + (6 + x++); 
// 4 + x++不是一个完整的表达式，因此C++不保证x的值在计算完4 + x++ 后立刻增加1
// 整条语句是一个完整的表达式，分号标识了顺序点，因此C++只保证执行到下一条语句之前，x的值被递增两次。
```
- C++11不再使用顺序点概念，因为这个概念难以用于讨论多线程执行。而是使用术语“顺序”，它表示有些事件在其他事件前发生。

### 5.1.7 前缀格式和后缀格式
```C++
for (n = lim; n > 0; --n)  // 前缀格式
	...;
for (n = lim; n > 0; n--)  // 后缀格式
	...;
```
- 在效果上，二者没有差别。但是在**性能上，有区别**。
- C++运行您针对类定义这些运算符，在这种情况下：
	- 前缀：将值加1，然后返回结果。
	- 后缀：先赋值一个副本，将其加1，然后将复制的副本返回。
	- 因此，对于类而言，前缀版本的效率比后缀高

### 5.1.8 递增/递减运算符和指针 
- 将递增运算符用于指针时，将把指针的值增加其指向的数据类型占用的字节数
- 将* 和++结合使用可以修改指针指向的值。但是要考虑优先级问题。
	- 前缀递增、前缀递减和解除引用运算符的优先级相同，以从右到左的方式结合
	- 后缀递增、后缀递减的优先级相同，但比前缀运算符优先级高。这两个以从左到右方式进行结合
```C++
double x = *++pt;  // *被用于递增后的指针
double y = ++*pt;  // 先取得pt指向的值，然后将这个值加1；指针指向的位置没变
(*pt)++;  // 和上面的效果一样
x = *pt++;  // 后缀运算符++的优先级更高，++用于pt而不是*pt。然后后缀运算符意味着将对原来的地址而不是递增后的新地址接触引用。*pt++的值还是原来的，但是语句执行完毕后，pt指向下一个地址。 
```
### 5.1.9 组合运算符
- -=、+=、\*=、/=、%=

### 5.1.10 复合语句（语句块）
```C++
// 5.8 block.cpp -- use a block statement
#include <iostream>

int main()
{
	using namespace std;
	cout << "The Amazing Accounto will sum and average ";
	cout << "five numbers for you.\n";
	cout << "Please enter five values:\n";
	double number;
	double sum = 0.0;
	for (int i = 1; i <= 5; i++)
	{
		cout << "Value " << i << ": ";
		cin >> number;
		sum += number;
	}
	cout << "Five exquisite choices indeed! ";
	cout << "They sum to " << sum << endl;
	cout << "and average to " << sum / 5 << ".\n";
	cout << "The Amazing Accounto bids you adieu!\n";
	return 0;
}
```
- 如果在一个语句块中声明一个变量，而外部语句块中也有一个这种名称的变量，那么在声明位置到内部语句块结束的范围之内，新变量将隐藏旧变量；然后旧变量再次可见，如下例所示：
```C++
#include <iostream>
int main()
{
	using std::cout;
	using std::endl;
	int x = 20;  // original x
	{
		cout << x << endl;  // 20; use orginal x;
		int x = 100;
		cout << x << endl;  // 100; use new x;
	}
	cout << x << endl;  // 20; use origianl x;
	return 0;
}
```
### 5.1.11 其他语法技巧——逗号运算符
- 逗号运算符允许将两个表达式放到C++语法只允许放一个表达式的地方。
- [ ] 为什么i = word.size()后出现h消失，不应该一个都不打印么
```C++
// 5.9 forstr2.cpp -- reversing an array
#include <iostream>
#include <string>
int main()
{
	using namespace std;
	cout << "Enter a word: ";
	string word;
	cin >> word;

	// physically modify string object
	char temp;
	int i, j;
	for (j = 0, i = word.size()-1; j < i; --i, ++j)
	{
		temp = word[i];
		word[i] = word[j];
		word[j] = temp;
	}
		
	cout << word << "\nDone\n";
	return 0;
}
```
- 在反转字符串，string类提供了更简洁的方式，在第16章介绍。
```C++ 
for(int j = 0, i = word.size() - 1; j < i; --i, ++j);
```
- 在↑这种情况下，逗号只是一个**列表分隔符，而不是逗号运算符**，因此该表达式对j和i进行声明和初始化。然而，看上去好像只声明了j；

#### 2. 逗号运算符花絮
- 逗号运算符的两个特性：
	- 首先，它确保先计算第一个表达式，然后计算第二个表达式（换句话说，逗号运算符是一个顺序点）
	- 其次,其次逗号表达式的值是第二部分的值。在所有运算符中，逗号运算符的优先级是最低点

```C++
i = 20, j = 2 * i;   // i set to 20, then j set to 40;表达式的值为40
cats = 17, 240;  // 被解释为 (cats = 17), 240;即将cat设置为17
cats = (17, 240);  // 把cat设置为240——逗号右侧的表达式值
```

### 5.1.12 关系表达式
- C++提供6种运算符来对数字比较：<、<=、== 、> 、>= 、!=
- 由于字符用其ASCII码表示，可以将这些运算符用作字符。不能将它们用于C-风格字符串，但可以用于string对象。
- 对所有的表达式，结果为真，则值为true，否则为false
```C++
// 关系运算符的优先级比算术运算符第。这意味着表达式
x + 3 > y - 2;
// 对应于：
(x + 3) > (y - 2);
// 而不是
x + (3 > y) - 2;
```
- 由于将bool提升为int后，表达式(3>y)要么为1，要么为0，因此第二个表达式和第三个表达式都是有效的。

### 5.1.13 赋值、比较和可能犯的错误
- 不要混淆等于运算符(==)和赋值运算符(=)
- musicians = 4; 表达式的值为4，bool值为true

```C++
// 5.10 equal.cpp -- equality vs assignment
#include <iostream>
int main()
{
	using namespace std;
	int quizscores[10] =
		{ 20, 20, 20, 20, 20, 19, 20, 18, 20, 20 };

	cout << "Doing it right:\n";
	int i;
	for (i = 0; quizscores[i] == 20; i++)
		cout << "quiz " << i << " is a 20\n";

	// It's wrong! 因为测试表达式的值始终为true，数组也越界了
	for (i = 0; quizscores[i] = 20; i++)  
		cout << "quiz " << i << " is a 20\n";
	return 0;
}
```
- 对于C++类，可以设计一种保护数组类型来防止**数组越界**，第13章提供了一个这样的例子。

### 5.1.14 C-风格字符串的比较
```C++
// 如果word是数组名，要知道字符数组中的字符串是不是mate，进行了这样的操作：
word == "mate";
// 因为数组名是地址，引号括起来的字符串常量也是地址，所以↑其实是比较它们是否存储在相同的地址上
// 而不是判断两个字符串相同。显然它们的地址也不相同
```
- C-风格字符串比较一般都用strcmp()
- C-风格字符串是通过结尾的空值字符定义的；即便数组定义的长度不同，但存储的字符相同那么它们就是相同的，
```C
char big[80] = "Daffy";  // 5 letters plus \0
char little[6] = "Daffy";  // 5 letters plus \0
```
```C++
// 5.11 compstr1.cpp -- comparing strings using arrays
#include <iostream>
#include <cstring>
int main()
{
	using namespace std;
	char word[5] = "?ate";
	for (char ch = 'a'; strcmp(word, "mate"); ch++)
	{
		cout << word << endl;
		word[0] = ch;
	}
	cout << "After loop ends, word is " << word << endl;

	// addition
	for (char ch = 'a'; ch <= 'z'; ++ch)
		cout << ch;
	return 0;
}
```

### 5.1.15 比较string类字符串
- 对于string字符串，可以使用比较运算符比较，因为类函数重载（重定义）了这些运算符
```C++
// 5.12 compstr2.cpp -- comparing strings using arrays
#include <iostream>
#include <string>
int main()
{
	using namespace std;
	string word = "?ate";
	for (char ch = 'a'; word != "mate"; ch++)
	{
		cout << word << endl;
		word[0] = ch;
	}
	cout << "After loop ends, word is " << word << endl;
	return 0;
}
```
- **注意1**：用关系运算符比较时：至少有一个操作数为string对象，另一个操作数可以是string对象，也可是是C-风格字符串
- **注意2**：此循环不是计数循环，根据情况(word为"mate")来确定是否停止。对于这种情况，通常使用while循环

## 5.2 while循环
```C++
// 5.13 -- introducing the while loop
#include <iostream>
const int ArSize = 20;
int main()
{
	using namespace std;
	char name[ArSize];
	cout << "Your first name, please: ";
	cin >> name;
	cout << "Here is your name, verticalized and ASCIIized:\n";
	int i = 0;
	while (name[i] != '\0')
	{
		cout << name[i] << ": " << int(name[i]) << endl;
		i++;
	}
	return 0;
}
```
- 不同于C-风格字符串，string对象不使用空字符来标记字符串末尾；但是要将程序5.13转换为使用string类的版本，只需要用string对象替换char数组即可。第16章讨论可用于标识string对象中最后一个字符的技术

### 5.2.1 for 与 while
- 本质上，for 和 while是相同的
	- 程序员使用for循环为循环计数
	- 当无法预先知道循环将执行的次数时，程序员常使用while循环 
- for与while的三个差别：
	- for省略了测试条件时，将认为条件为true
	- 在for循环中，可使用初始化语句声明一个局部变量，但while循环不能这样做
	- 如果循环体包含continue语句，情况将稍有不同。 
- 设计循环的规则
	- 指定循环终止条件
	- 在首次测试之前初始化条件
	- 在条件被再次测试之前更新条件
- for循环的一个优点是，结构提供了一个可实现上述3条指导原则的地方 

### 5.2.2 等待一段时间：编写延时循环
```C++
// 早期用这种方法
long wait = 0;
while (wait < 10000)
	wait++;
```
- ANSI C和C++库有个clock()函数，可以完成这样的工作。clock()返回的函数不是秒，其次返回类型有的系统是long，另一些系统是unsigned long
- ctime提供了解决方案。它定义一个符号常量——CLOCKS_PER_SEC,该常量等于每秒包含的系统时间单位数。系统时间除以这个数可以得到秒数。其次，ctiem将clock_t作为clock()返回类型的别名（参见本章后面的注释“类型别名”）,这意味着可以将变量声明为clock_t类型，编译器把它转换为long、unsigned int或适合系统的其他类型。
```C++
// 5.14 waiting.cpp -- using clock() in a time-delay loop
#include <iostream>
#include <ctime>  // describes clock() function, clock_t type
int main()
{
	using namespace std;
	cout << "Enter the delay time, in seconds: ";
	float secs;
	cin >> secs;
	clock_t delay = secs * CLOCKS_PER_SEC;  // convert to clock ticks
	cout << "starting\a\n";
	clock_t start = clock();
	while (clock() - start < delay)  // wait until time elapses
		;  // note the semicolon
	cout << "done\a\n";
	return 0;
}
```
- 该程序以系统时间单位为单位（而不是以秒为单位）计算延迟时间，避免了在每轮循环中将系统时间转换为秒
#### 类型别名
- C++为类型创建别名的方式有两种：
	- 1.使用预处理器：``` define BYTE char ```
	- 2.用关键字typedef来创建：``` typedef char byte ``` （**常用**）

```C++
typedef char * byte_pointer;  // 让byte_pointer成为char指针的别名

#define FLOAT_POINTER float *  // 也可以用#define，不过沉迷一些列变量时，这种方法不使用
FLOAT_POINTER pa, pb;
float * pa, pb;  // 预处理器将其替换为这样，只有pa是指针，pb只是一个float型变量，typedef就不会有这样的问题
```

## 5.3 do while 循环
- do while 循环是出口条件(exit condition)循环，意味着它先执行循环体，再判定测试表达式。至少执行一次。
- 通常入口条件循环比出口条件循环好，因为入口条件循环在循环开始前就对条件进行检查
- 有时do while更合理。例如，请求用户输入时，程序必须先获得输入，然后对它进行测试
```C++
// 5.15 dowhile.cpp -- exit-condition loop
#include <iostream>
int main()
{
	using namespace std;
	int n;

	cout << "Enter numbers in the range 1-10 to find ";
	cout << "my favorite number\n";
	do
	{
		cin >> n;
	} while (n != 7);
	cout << "Yes, 7 is my favorite.\n";
	return 0;
}
```
## 5.4 基于范围的for循环 (C++11)
- C++新增的基于范围(range-based)的for循环简化了循环任务：对数组（或容器类，如vector和array）的每个元素执行相同的操作
```C++
#include <iostream>
int main()
{
	using namespace std;
	double prices[5] = { 4.99, 10.99, 6.87, 7.99, 8.49 };

	// x最初表示数组的第一个元素，然后依次表示数组的其他元素
	for (double x : prices)
		cout << x  << endl;

	// &表明x是一个引用变量，这种声明能让代码修改x的内容
	for (double& x : prices)
		x *= 0.8;  // 修改数组的元素

	// 可以和初始化列表结合使用
	for (int x : {3, 4, 5, 3, 2, 1})
		cout << x << " ";
	cout << '\n';
	return 0;
}
```
- 像Java中的for-each

## 5.5 循环和文本输入
- cin对象支持3种不同模式的单字符输入，其用户接口各不相同

### 5.5.1 使用原始的cin进行输入
- 选择某个字符——有时称为哨兵字符(sentinel character)作为停止标记


