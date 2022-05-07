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
```C++
// 5.16 textin1.cpp
#include <iostream>
int main()
{
	using namespace std;
	char ch;
	int count = 0;  // use basic input
	cout << "Enter characters; enter # to quit:\n";
	cin >> ch;
	while (ch != '#')
	{
		cout << ch;
		++count;
		cin >> ch;  // 没有这一步，循环将反复处理第一个输入字符
	}
	cout << endl << count << " characters read\n";
	return 0;
}
```
- 为什么输入的空格被省略了？
	- 因为cin读char时，与其他类型一致，cin将忽略空格和换行符。
	- 因此输入的空格没有被回显，也没有被包括在计数内
- 更复杂的是，发送给cin的输入流被缓冲。当用户按下回车时，才被发送给程序。这就是可以在#后输入字符的原因。（讲的真好，真细）

### 5.5.2 使用cin.get(char)进行补救
- 成员函数cin.get(ch)读取输入中的下一个字符（即使它是空格），并将其赋给ch。使用这个函数调用替换cin>>ch，可以修补程序5.16的问题
```C++
// 5.17 textlin2.cpp -- using cin.get(char)
#include <iostream>
int main()
{
	using namespace std;
	char ch;
	int count = 0;  // use basic input

	cout << "Enter characters; enter # to quit:\n";
	cin.get(ch);

	while (ch != '#')
	{
		cout << ch;
		++count;
		cin .get(ch);  // use it again
	}
	cout << endl << count << " characters read\n";
	return 0;
}
```
- 这样就全部字符计算在内了，包括空格。输入扔被缓冲，因此输入的字符个数扔可能比最终到达程序的要多
- 头文件iostream将cin.get(ch)的参数声明为引用类型，因此该函数可以修改其参数的值

### 5.5.3 使用哪一个cin.get()
- cin.get()的一个版本接受两个参数：数组名（字符串（char* 类型）的地址）和ArSize（int类型的整数）
- 函数重载将在第8章介绍

### 5.5.4 文件尾条件
- 如果编程环境能够检测EOF，可以在类似与程序清单5.17的程序中使用重定向的文件，也可以使用键盘输入，并在键盘仔模拟EOF（Windows下用Ctrl+z来模拟）
```C++
// 5.18 textin3.cpp -- reading chars to end of file
#include <iostream>
int main()
{
	using namespace std;
	char ch;
	int count = 0;
	cin.get(ch);  // attempt to read a char
	while (!cin.fail())  // test for EOF
	{
		cout << ch;
		++count;
		cin.get(ch);
	}
	cout << endl << count << " characters read\n";
	return 0;
}
```
- 检测到EOF后，cin将两位（eofbit和failbit）都设置为1.
	- 可以通过eof()来查看eofbit是否被设置；如果检测到EOF，cin.eof()将返回true，否则返回false
	- 同样，如果eofbit或falibit被设置为1， fail()函数返回true，否则返回false
	- 注意，eof()和fail()方法报告最近读取的结果，因此应将cin.eof()或cin.fail测试放在读取后
- [ ] 还不是太理解这个到底怎么搞的

#### 1.EOF结束输入
- 对于文件输入，这也是有道理的，因为程序不应读取超出文件尾的内容。
- 对于键盘输入，可以模拟EOF来结束循环。cin.clear()方法可能清除EOF标记，使输入继续进行。第17章介绍
#### 2.常见的字符输入做法
- cin.get(char)的返回值是一个cin对象。然而，istream类提供了一个可以将istream对象（如cin）转换为bool值的函数。当cin出现在需要bool值的地方（如在while循环的测试条件中）时，该转换函数将被调用。
- 另外，如果最后一次读取成功了，则转换得到bool值为true；否则为false。
```C++
// 这意味着，可以将while测试改写成这样
while (cin)  // while input is successful
// 这比!cin.fail()或!cin.eof()更通用，因为它可以检测到其他失败原因，如磁盘故障。
// 最后，由于cin.get(char)的返回值为cin，因此可以将循环精简成这种格式：
while (cin.get(ch))
{
	...
}
// 这样cin.get(char)调用一次就行了。程序会对返回值cin进行bool转换的。
// 程序5.18直接改成这就行了，nice！
int count = 0;
while (cin.get(ch))
{
	cout << ch;
	++count;
}
```
- 三条指导原则（确定结束条件、对调节进行初始化以及更新条件）**全部被放在循环测试条件中！！！**（牛逼）
### 5.5.5 另一个cin.get()版本
```C++
// 不接受任何参数的cin.get()成员函数返回输入中的下一个字符
ch = cin.get();
cout.put(ch);  // （参见第3章）来显示字符
// 使用显式强制类型转换的原型（如cin.put(char(ch))）可以使用int参数

char ch;
cin.get(ch);
while (cin.fail() == false)  // test for EOF
{
	cout << ch;
	++count;
	cin.get(ch);
}

// 可以使用int ch, 并用cin.get()代替cin.get(char)，用cout.put()代替cout，用EOF测试代替cin.fail()测试：
int ch;
ch = cin.get();
while (ch != EOF)
{
	cout.put(ch);  // cout.put(char(ch)) for some implementations
	++count;
	ch = cin.get();
}  // 需要知道的是，EOF不表示输入中的字符，而是指出没有字符
```
- 关于cin.get()还有一个微妙而重要的问题：EOF表示的不是有效字符编码，因此可能不与char类型兼容。
```C+++
// 5.19 -- textlin4.cpp -- reading chars with cin.get()
#include <iostream>
int main(void)
{
	using namespace std;
	int ch;  // should be int, not char
	int count = 0;

	while ((ch = cin.get()) != EOF)  // test for end-of-file
	{
		cout.put(char(ch));
		++count;
	}
	cout << endl << count << " characters read\n";
	return 0;
}  // 包含了换行符
```
- cin.get(ch) 与 ch=cin.get()，见表
- 到底用哪个？

## 5.6 嵌套循环和二维数组
```C++
// 该声明意味着maxtemps是一个包含4个元素的数组，其中每个元素都是一个由5个整数组成的数组
int maxtemps[4] [5];  

// 打印二维数组
for (int row = 0; row < 4; row++)
{
	for (int col = 0; col < 5; ++col)
		cout << maxtemps[row]pcol] << "\t";
	cout << endl;
}
```
### 5.6.1 初始化二维数组
```C++
int maxtemps[4][5] = 
{
	{98, 100, 100, 134, 112},  // values for maxtemps[0]
	{98, 100, 100, 134, 112},  // values for maxtemps[1]
	{98, 100, 100, 134, 112},  // values for maxtemps[2]
	{98, 100, 100, 134, 112}   // values for maxtemps[3]
};
```

### 5.6.2 使用二维数组
```C++
// 5.20 nested.cpp -- nested loops and 2-D array
#include <iostream>
const int Cities = 5;
const int Years = 4;
int main()
{
	using namespace std;
	const char* cities[Cities] =  // array of pointers
	{  // to 5 strings
		"Gribble city",
		"Gribbletown",
		"New Gribble",
		"San Gribble",
		"Gribble vista"
	};

	int maxtemps[Years][Cities] =
	{
		{96, 100, 87, 101,105},
		{96, 98, 91, 107,104},
		{97, 101, 93, 108, 107},
		{98, 103, 95, 109, 108}
	};

	cout << "Maximum temperatures for 2008 - 2011\n\n";
	for (int city = 0; city < Cities; ++city)
	{
		cout << cities[city] << ":\t";
		for (int year = 0; year < Years; ++year)
			cout << maxtemps[year][city] << "\t";
		cout << endl;
	}
	return 0;
}
```
- 指针数组 和 数组指针有区别吗？
	- 指针数组：是一个数组，每个元素都是一个指针。 ``` int* a[4]; ```
	- 数组指针：指向数组的指针 ``` int (*a)[4]; ```
```C++
#include <iostream>

using namespace std;

int main()
{
	int c[4] = { 1,2,3,4 };
	int* a[4]; //指针数组
	int(*b)[4];  //数组指针
	
	b = &c;
	for (int i = 0; i < 4; i++)  //将数组c中元素赋给数组a
	{
		a[i] = &c[i];
	}
	cout << *a[1] << endl;  //输出2就对
	cout << (*b)[2] << endl;  //输出3就对
	return 0;
}
```
- 1.将一个指针数组初始化为一组字符串常量。
```C++
const char* cities[Cities] =  
{  // to 5 strings
	"Gribble city",
	"Gribbletown",
	"New Gribble",
	"San Gribble",
	"Gribble vista"
};
```
- 2.使用char数组的数组，而不是字符串指针数组
- 从存储空间角度说，使用指针数组更为经济；如果要修改其中的任何一个字符串，二维数组是更好的选择。
```C++
char cities[Cities][25] =  
{  // to 5 strings
	"Gribble city",
	"Gribbletown",
	"New Gribble",
	"San Gribble",
	"Gribble vista"
}; 
```
- 3.使用string类
- 如果希望字符串是可修改的值，则应省略限定符const。
- 使用string对象数组是，初始化列表和显示字符串的for循环与前两种相同
- 在希望字符串是可以修改的情况下，string类是比二维数组更好的选择
```C++
const string cities[Cities] = 
{  // to 5 strings
	"Gribble city",
	"Gribbletown",
	"New Gribble",
	"San Gribble",
	"Gribble vista"
};
```
## 5.7 总结
- 循环部分还行，cin这部分，字符串读取相关的函数和返回值看的有点懵。下次看视频可以再回顾回顾。
## 5.8 复习题
- [ ] 第8题
- [ ] 第9题

```C++
// 1.入口条件：只要满足入口设置的条件，才能进入循环；先判断条件，后执行语句。如：for循环、while循环；出口条件：先执行，后判断条件。
// 2.打印：0\n 1\n 2\n 3\n 4\n
// 3.打印：0369 \n 11 \n
// 4. 68
// 5. k = 8, k = 9, k= 10 ...死循环
// 6. 
for (int i = 1; i <= 64; i *= 2)
	cout << i << endl;
// 7.加一个{}
// 8.取最后以个值为表达式的值，024是八进制，转换为十进制是20，所以x = 20.逗号表达式优先级最低，所以y = 1;
// 9.cin>>ch。把一个字符赋值给ch，包含回车。cin.get(ch)把下一个字符赋给ch，返回一个对象。ch=cin.get()返回一个对应字符ASCII编码的整数
```
## 5.9 编程练习
- 1
```C++
#include <iostream>
using namespace std;
int main()
{
	int min, max, sum;
	
	cout << "Enter two numbers: ";
	cin >> min >> max;
	
	for (sum = 0; min <= max; ++min)
		sum += min;
	cout << "The sum is " << sum << endl;
	return 0;
}
```
- 2
```C++
// 5.4 formore.cpp -- more looping with for
#include <iostream>
#include <array>
using namespace std;
const int AriSize = 16;   // example of external declaration
int main()
{
	array<long double, AriSize> factorials;
	factorials[0] = factorials[1] = 1;

	for (int i = 2; i < AriSize; ++i)
		factorials[i] = i * factorials[i - 1];
	for (int i = 0; i < AriSize; ++i)
	{
		cout << fixed;
		cout.precision(0);
		cout << i << "! = " << factorials[i] << endl;
	}
		
	return 0;
}
```
- 3
```C++
#include <iostream>
int main()
{
	using namespace std;
	long long num;
	long long sum = 0LL;
	
	cin >> num;
	while (num != 0)
	{
		sum += num;
		cout << "sum = " << sum  << endl;
		cin >> num;
	}
	return 0;
}
```

- 4
```C++
#include <iostream>
using namespace std;
int main()
{
	double D_money, C_money;
	int year;

	D_money = C_money = 100.0;
	for (year = 0; C_money <= D_money; ++year)
	{
		D_money += 0.1 * 100;
		C_money += 0.05 * C_money;
		cout << "C_money: " << C_money << endl;
	}
	cout << "Need years: " << year << endl;  // 27
	cout << "Daphne has $" << D_money
		<< "\tCleo has $" << C_money << endl;
	return 0;
}
```

- 5
```C++
#include <iostream>
#include <string>
using namespace std;
const int Size = 12;
int main()
{
	string months[Size] =
		{
			"January", "February", "March", "April",
			"May", "June", "July", "August",
			"September", "October", "November", "December"
		};
	int sales_volume[Size];
	int sum = 0;

	for (int i = 0; i < Size; ++i)
	{
		cout << "Enter volumes of " << months[i] << ": ";
		cin >> sales_volume[i];
		sum += sales_volume[i];
	}
	cout << "This year's sales are " << sum << endl;
}
```

- 6
```C++
#include <iostream>
#include <string>
using namespace std;
const int Size = 12;
int main()
{
	string months[Size] =
		{
			"January", "February", "March", "April",
			"May", "June", "July", "August",
			"September", "October", "November", "December"
		};
	int sales_volume[3][Size];
	int y_sum, sum;
	y_sum = 0;

	for (int i = 0; i < 3; ++i)
	{
		sum = 0;
		cout << i+1 << "th year:\n";
		for (int j = 0; j < Size; ++j)
		{
			cout << "Enter volumes of " << months[j] << ": ";
			cin >> sales_volume[i][j];
			sum += sales_volume[i][j];
		}
		cout << "This year's sales are " << sum << endl;
		y_sum += sum;
	}

	cout << "y_sum is " << y_sum;
	
}
```

- 7
```C++
#include <iostream>
#include <string>
using namespace std;
struct car {
	string producer;  // 生产商
	int year;  // 生产年份
};
int main()
{
	int Size = 0;
	cout << "How many cars do you wish to catalog? ";
	cin >> Size;
	car* cars = new car [Size];
	cin.get();  // eat '\n'
	for (int i = 0; i < Size; ++i)
	{
		cout << "Car #" << i + 1 << endl;
		cout << "Please enter the make: ";
		getline(cin, cars[i].producer);
		//cin >> cars[i].producer;

		cout << "Please enter the year made: ";
		cin >> cars[i].year;
		cin.get();  // eat '\n' 
	}

	cout << "Here is your collection:\n";
	for (int i = 0; i < Size; ++i)
		cout << cars[i].year << " " << cars[i].producer << endl;
	
	delete[] cars;
	return 0;
}
```

- 8
```C++
#include <iostream>
#include <cstring>
const int ArSize = 20;

int main()
{
	using namespace std;
	char word[ArSize];
	int n = 0;
	
	cout << "Enter words (to stop, type the word done):" << endl;
	cin >> word;
	while (strcmp(word, "done") != 0)
	{
		++n;
		cin >> word;
	}

	cout << "You entered a total of "<< n << " words" << endl;
	return 0;
}
```

- 9
```C++
#include <iostream>
#include <string>

int main()
{
	using namespace std;
	string word;
	int n = 0;
	
	cout << "Enter words (to stop, type the word done):" << endl;
	cin >> word;
	while (word != "done")
	{
		++n;
		cin >> word;
	}

	cout << "You entered a total of "<< n << " words" << endl;
	return 0;
}
```

- 10
```C++
#include <iostream>


int main()
{
	using namespace std;
	int i, j, n;
	cout << "Enter number of rows:";
	cin >> n;
	
	for (i = 1; i <= n; ++i)
	{
		for (j = n - i; j > 0; --j)
			cout << ".";
		for (j = i; j > 0; --j)
			cout << "*";
		cout << endl;
	}
	return 0;
}
```















