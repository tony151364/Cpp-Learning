
## 6.1 if 语句
- 和循环测试条件一样，if测试条件也将被强制转换为bool值，因此0将被转换为false，非零为true。整个if语句被视为一条语句。

```C++
// 6.1 if.cpp -- using the if statement
#include <iostream>
int main()
{
	using std::cin;  // using declarations
	using std::cout;
	char ch;
	int spaces = 0;
	int total = 0;
	cin.get(ch);
	while (ch != '.')
	{
		if (ch == ' ')
			++spaces;
		++total;
		cin.get(ch);
	}
	cout << spaces << " spaces, " << total;
	cout << " characters total in sentence\n";
	return 0;
}
```
- 注意字符总数中包括按回车键生成的换行符

### 6.1.1 if else 语句
```C++
// 6.2 ifelse.cpp -- using the if else statement
#include <iostream>
int main()
{
	char ch;

	std::cout << "Type, and I shall repeat.\n";
	std::cin.get(ch);
	while (ch != '.')
	{
		if (ch == '\n')
			std::cout << ch;
		else
			std::cout << ++ch;
		std::cin.get(ch);
	}
	// try ch + 1 instead of ++ch for interrsting effect
	std::cout << "\nPlease excuse the slight confusion.\n";
		// std::cin.get();
		// std::cin.get();
	return 0;
}
```
- cin.get()的一些说明（参考：4.2.4节）：
  - cin.get(); 读取一个字符，包括换行符;**用户按完回车后才开始执行**，下同。
  - cin.get(ch); 把读取的字符存储到ch变量所在的内存中
  - cin.get(name, ArSize); 读取一行字符，最大为ArSize-1个字符，最后一个位置为'\0'.且不主动读取换行符。也就是说换行符还在输入流中，会被下一个cin.get()读取（如果有这个操作的话）。
  - cin >> name; 只读取一个单词，遇到空白符就停止了。（name是个字符数组名，上同）
### 6.1.2 格式化if else语句
- 第一种格式：强调的是语句的块结构
```C++
if (ch == 'z')
{
	zorro++;
	cout << "Another zorro candidate\n";
}
else
{
	dull++;
	cout << "Not a Zorro condidate\n";
}
```
- 第二种格式：将语句块与关键字if和else更紧密地结合在一起。
```C++
if (ch == 'z') {
	zorro++;
	cout << "Another zorro candidate\n";
	}
else {
	dull++;
	cout << "Not a Zorro condidate\n";
	}
```
### 6.1.2 if else if else 结构
```C++
// 6.3 ifelseif.cpp -- using if else if else
#include <iostream>
const int Fave = 27;
int main()
{
	using namespace std;
	int n;

	cout << "Enter a number in the range 1-100 to find ";
	cout << "my favorite number: ";
	do
	{
		cin >> n;
		if (n < Fave)
			cout << "Too low -- guess again: ";
		else if (n > Fave)
			cout << "Too high -- guess again: ";
		else
			cout << Fave << " is right!\n";
	} while (n !=  Fave);
	return 0;
}
```
- 编写让编译器更容易发现错误的代码：
```C++
if ( 3 == myNumber)   // 可以正常工作
if ( 3 = myNumber)   // 错误，但编译器很容易发现，因为程序员尝试将一个值赋给一个字面值
if ( myNumber = 3)  // 错误，但是编译器发现不了，因为这符合语法。
```

## 6.2 逻辑表达式
- 三种逻辑运算符：逻辑OR(||)、逻辑AND(&&)、逻辑NOT(!)

### 6.2.1 逻辑OR运算符：||
- 两边只要有一个为true，表达式的值就为true。否则为false，即两边都为false，结果为false。
- ``` 5 == 5 || 5 == 9; ``` 对于←来说，||的优先级比关系运算符低，因此不需要在表达式中使用括号
- C++规定，||运算符是个顺序点(sequence pint)。也就是先修改左侧的值，再运算右侧的表达式。当左侧为true，右侧就不运算了（这种现象也称为“**短路**”）

```C++
// 6.4 or.cpp
#include <iostream>
int main()
{
	using namespace std;
	cout << "This program may format your hard disk\n"
		"and destroy all your data.\n"
		"Do you wish to continue? <y/n> ";
	char ch;
	cin >> ch;
	if (ch == 'y' || ch == 'Y')
		cout << "You were warned!\a\a\n";
	else if (ch == 'n' || ch == 'N')
		cout << "A wise choice ... bye\n";
	else
		cout << "That wasn't a y or n! Apparently you "
			"can't follow\ninstructions, so "
			"I'll trash your disk anyway.\a\a\a\n";
	return 0;
}
```
### 6.2.2 逻辑AND运算符：&&
- 仅当两侧都为true时，结果才为true
- 和||运算符一样，&&也是顺序点，先左后右。左侧为false是，右侧不判断了（**短路**）
```C++
// 6.5 and.cpp -- using the logical AND opeartor
#include <iostream>
const int ArSize = 6;
int main()
{
	using namespace std;
	float naaq[ArSize];
	cout << "Enter the NAAQs {New Age Awareness Quotients} "
		<< "of\nyour neighbors. Program terminates "
		<< "when you make\n" << ArSize << " entries "
		<< "or enter a negative value.\n";
	
	int i = 0;
	float temp;
	cout << "First value: ";
	cin >> temp;
	while (i < ArSize && temp >= 0)
	{
		naaq[i] = temp;
		++i;
		if (i < ArSize)
		{
			cout << "Next value: ";
			cin >> temp;  // so get next value
		}
	}
	if (i == 0)
		cout << "No data--bye\n";
	else
	{
		cout << "Enter your NAAQ: ";
		float you;
		cin >> you;
		int count = 0;
		for (int j = 0; j < i; j++)
			if (naaq[j] > you)
				++count;
		cout << count;
		cout << " of your neighbors have greater awareness of\n"
			<< "the New Age than you do.\n";
	}
	return 0;
}
```
### 6.2.3 用&&来设置取值范围
```C++
// 6.6 more_and.cpp -- using the logical AND operator
#include <iostream>
const char* qualify[4] =
{
	"10,000-memter race.\n",
	"mud tug-of-war.\n",
	"masters canoe jousting.\n",
	"pie-throwing festival.\n"
};
int main()
{
	using namespace std;
	int age;
	cout << "Enter your age in years: ";
	cin >> age;
	int index;

	if (age > 17 && age < 35)
		index = 0;
	else if (age >= 35 && age < 50)
		index = 1;
	else if (age >= 50 && age < 65)
		index = 2;
	else
		index = 3;

	cout << "You qualify for the " << qualify[index];
	return 0;
}
```
- **取值范围测试**：
```C++
if ( 17 < age < 35)  // 不要使用数学符号将表示为这样，编译器不会捕获这种错误，因为它仍然是有效的语法
if ( (17 < age) < 35)  // 根据<运算符从左向右结合，上述表达式含义如←
// 17 < age的值要么为true(1)，要么为false(0)。不管那种都小于35，结果总是true
```
### 6.2.4 逻辑NOT运算符：!
- !运算符将它后面的表达式的真值取反

```C++
// 6.7 not.cpp -- using the not operator
#include <iostream>
#include <climits>
bool is_int(double);
int main()
{
	using namespace std;
	double num;

	cout << "Yo, dude!Enter an integer value: ";
	cin >> num;
	while (!is_int(num))  // continue while num is not int-able
	{
		cout << "Out of range -- please try again: ";
		cin >> num;
	}
	int val = int(num);  // type cast
	cout << "You've enterd the integer " << val << "\nBye\n";
	return 0;
}
bool is_int(double x)
{
	if (x <= INT_MAX && x >= INT_MIN)
		return true;
	else
		return false;
}
```
- 程序先是用double存储的，因为double比int的范围大的多，当输入的值超出int范围时也能打印出来。用long long其实也可以。

### 6.2.5 逻辑运算符细节
- C++逻辑OR和逻辑AND运算符的优先级都低于关系运算符。``` x > 5 && x < 10``` -> ``` (x > 5) && (x < 10)```
- !运算符的优先级高于所有的关系运算符和算术运算符。因此要对表达式求反必须用括号括起来``` !(x > 5)```
- 逻辑AND运算符的优先级高于逻辑OR运算符。因此，表达式:
```C++
age > 30 && age < 45 || weight > 300  // 被解释为：
(age > 30 && age < 45) || weight > 300
(age > 50 || weight > 300) && donation > 100   // 这种情况就必须要用括号括起来了
```
- 一般来说，还是用括号括一下，即便是第一行那种也是。这样也便于阅读，避免忘了优先级而出错或理解错误。
- C++确保程序从左向右进行计算逻辑表达式，并在知道答案后立刻停止。

### 6.2.6 其他表示方式
- 当键盘不提供逻辑符号时，可以用C++标准提供的而另一种表示方式——标识符and、or和not来替代。
- [ ] 标识符and、or和not都是C++保留字，这意味着不能将它们用作变量名等。它们不是关键字，因为它们都是已有语言特性的另一种表示方式。它们不是C语言的保留字，若要使用则需要包含头文件iso646.h。C++不要求使用头文件
- 一些区分：
	- 保留字：语言中已经定义过的字，使用者不能再将这些字作为变量名或过程名使用。在一些语言中，一些保留字可能并没有应用于当前的语法中。，这就成了保留字与关键字的区别
	- 关键字：在语言中有特定含义，称为语法中的一部分的那些字。关键字一定是保留字，反过来也差不多。（在Java中，goto是个保留字不是关键字，因此你不能使用它）
	- 标识符：关键字和变量名都算标识符。只不过程序员在遵循一定规则下，可以自己定义变量名。
	- 变量名：是标识符的实例
	- 我觉得：最终还是变量名和关键字这俩，那两个的概念解释实在太少，暂时就这么理解吧。

## 6.3 字符函数库cctype
- C++从C语言继承了一个与字符相关的、非常方便的函数软件包——cctype
- isalpha(ch); ch是一个字符返回一个非零值，否则返回0
- ispunct(ch); ch是标点符号函数返回true
- isspace()来测试字符是否为空白
```C++
// cctypes.cpp -- using the ctype.h library
#include <iostream>
#include <cctype>  // prototypes for character functions
int main()
{
	using namespace std;
	cout << "Enter text for analysis, and type @"
		"to terminate input.\n";
	char ch;
	int whitespace = 0;
	int digits = 0;
	int chars = 0;
	int punct = 0;
	int others = 0;

	cin.get(ch);  // get first character
	while (ch != '@')
	{
		if (isalpha(ch))
			chars++;
		else if (isspace(ch))
			whitespace++;
		else if (isdigit(ch))
			digits++;
		else if (ispunct(ch))
			punct++;
		else
			others++;
		cin.get(ch);
	}
	cout << chars << " letters, "
		<< whitespace << " whitespace, "
		<< digits << " digits, "
		<< punct << " punctuations, "
		<< others << " others.\n";
	return 0;
}
```
## 6.4 ?:运算符
- 三元运算符，常用来替代if else
```C++
// 6.9 condit.cpp -- using the conditional operator
#include <iostream>
int main()
{
	using namespace std;
	int a, b;
	cout << "Enter two integers: ";
	cin >> a >> b;  // 可以啊，一次读两个
	cout << "The larger of " << a << " and " << b;
	int c = a > b ? a : b;
	cout << " is " << c << endl;
	return 0;
}
```
- 与if else相比，条件运算符生成一个表达式，因此是一个值，可以将其赋给变量或将其放到一个更大的表达式中。
- 它也可以嵌套，当代码变复杂是，使用if else可能更清晰。

## 6.5 switch语句
- switch语句能够更容易地从大型列表中进行选择；switch就像指路牌，告诉计算机接下来应该执行哪行代码。
- 每个标签都必须是整数常量表达式，常见的是int或char常量，也可以是枚举量
```C++
// 6.10 switch.cpp -- using the switch statement
#include <iostream>
using namespace std;
void showmenu();  // function prototypes
void report();
void comfort();
int main()
{
	showmenu();
	int choice;
	cin >> choice;
	while (choice != 5)
	{
		switch (choice)
		{
		case 1: cout << "\a\n";
			break;
		case 2: report();
			break;
		case 3: cout << "The boss was in all day.\n";
			break;
		case 4: comfort();
			break;
		default: cout << "That's not a choice.\n";
		}
		showmenu();
		cin >> choice;
	}
	cout << "Bye!\n";
	return 0;
}

void showmenu()
{
	cout << "Please enter 1, 2, 3, 4, or 5:\n"
		"1) alarm			2) report\n"
		"3) ablibi			4) comfort\n"
		"5) quit\n";
}

void report()
{
	cout << "It's been an excellent week for business. \n"
		"Sales are up 120%. Expenses are down 35%.\n";
}

void comfort()
{
	cout << "Your employees think you are the finest CEO\n" 
		"in the industry. The board of directors think\n" 
		"you are the finest CEO in the industry.\n";
}
```
### 6.5.1 将枚举量用作标签
```C++
// 6.11 enum.cpp -- using enum
#include <iostream>
enum {red, orange, yellow, green, blue, violet, indigo};
int main()
{
	using namespace std;
	cout << "Enter color code (0-6): ";
	int code;
	cin >> code;
	while (code >= red && code <= indigo)
	{
		switch (code)
		{
			case red: cout << "Her lips were red.\n"; break;
			case orange: cout << "Her hair were orange.\n"; break;
			case yellow: cout << "Her shoes were yellow.\n"; break;
			case green: cout << "Her nails were green.\n"; break;
			case blue: cout << "Her sweatsuit were blue.\n"; break;
			case violet: cout << "Her eyes were violet.\n"; break;
			case indigo: cout << "Her mood were indigo.\n"; break;
		}
		cout << "Enter color code (0-6): ";
		cin >> code;
	}
	cout << "Bye\n";
	return 0;
}
```
- 通常，cin无法识别枚举类型（它不知道程序员是如何定义它们的），因此该程序要求用户选择选项时输入一个整数。当switch语句将int值和枚举量标签进行比较时，将枚举量提升为int。
- while循环测试条件中，也会将枚举量提升为int类型

### 6.5.2 switch 和 if else
- switch并不是为处理取值范围而设计的。switch语句中的每一个case标签都必须是一个单独的值。另外，这个值必须是整数（包括char）。另外case标签值还必须是常量。相比之下，if else 更通用。
- **提示**：如果既可以用 if else if语句，也可用switch语句，则当选项不少于3个时，应使用switch语句。

### 6.6 break 和 continue语句
```C++
// 6.12 jump.cpp -- using continue and break
#include <iostream>
const int ArSize = 80;
int main()
{
	using namespace std;
	char line[ArSize];

	int spaces = 0;

	cout << "Enter a line of text:\n";
	cin.get(line, ArSize);
	cout << "Complete line:\n" << line << endl;
	cout << "Line through first period:\n";
	for (int i = 0; line[i] != '\0'; i++)
	{
		cout << line[i];
		if (line[i] == '.')
			break;
		if (line[i] != ' ')
			continue;
		spaces++;
	}
	cout << "\n" << spaces << " spaces\n";
	cout << "Done.\n";
	return 0;
}
```
- 在for循环中，continue语句使程序直接跳到更新表达式处，然后跳到测试表达式处。
- 对于while来说，continue将使程序直接跳到测试表达式处
## 6.7 读取数字的循环
```C++
int n;
cin >> n;
```
- 如果用户输入一个单词，而不是数字，将发生4种情况：
	- n的值保持不变；
	- 不匹配的输入将被留在输入队列中
	- cin对象将设置一个错误标记
	- cin方法返回false（如果被转换为bool类型） 
- 方法返回false意味着可以用非数字输入来结束读取数字的循环

```C++
// 6.13 cinfish.cpp -- non-numeric input terminates loop
#include <iostream>
const int Max = 5;
int main()
{
	using namespace std;
	double fish[Max];
	cout << "You may enter up to " << Max
		<< " fish <q to terminate>.\n";
	cout << "fish #1: ";
	int i = 0;
	while (i < Max && cin >> fish[i])  // 需要注意
	{
		if (++i < Max)  // 需要注意
			cout << "fish #" << i + 1 << ": ";
	}
	double total = 0.0;
	for (int j = 0; j < i; j++)
		total += fish[j];
	if (i == 0)
		cout << "No fish\n";
	else
		cout << total / i << " = average weight of "
		<< i << " fish\n";
	cout << "Done.\n";
	return 0;
}
```
- i < Max放到&&左边是有讲究的，运用&&的“短路”现象以防止数组越界
- 6.13中,的表达式cin >> fish[i] 实际上是一个cin方法调用，该函数返回cin。如果cin位于测试条件中，则将被转换为bool类型。如果输入成功，则转换后的值为true，否则为false。
```C++
// 6.14 cingolf.cpp -- non-numeric input skipped
#include <iostream>
const int Max = 5;
int main()
{
	using namespace std;
	// get data
	int golf[Max];
	cout << "Please enter your golf scores.\n";
	cout << "You must enter " << Max << " rounds.\n";
	int i;
	for (i = 0; i < Max; i++)
	{
		cout << "round #" << i + 1 << ": ";
		while (!(cin >> golf[i])) {  // 若输入的不满足golf[i]的要求，返回false
			cin.clear();  // 1.重置cin以接受新的输入
			while (cin.get() != '\n')  // 2.删除错误输入
				continue; // get rid of bad input
			cout << "Please enter a number: "; // 3.提示用户再输入
		}
	}
	// calculate average
	double total = 0.0; 
	for (i = 0; i < Max; i++)
		total += golf[i];
	// report results
	cout << total / Max << " = average score "
		<< Max << " rounds\n";
	return 0;
}
```
- 注意：程序必须先重置cin，才能删除错误输入

## 6.8 简单文件输入/输出
- 控制台输入/输出；文件输入/输出

### 6.8.1 文本I/O和文本文件
-[ ] 阅读cin的原理
- 本章讨论的文件I/O相当于控制台I/O，因此仅适用于文本文件(txt等)
### 6.8.2 写入到文本文件
- cout用于控制台的一些内容：
	- 必须包含头文件iostream
	- 头文件iostream定义了一个用于处理输出的ostream类
	- 头文件iostream声明了一个名为cout的ostream变量（对象）
	- 必须指明名称空间std；例如，为引用元素cout和endl，必须使用编译指令using或前缀std::。
	- 可以结合使用cout和运算符<<来显示各种类型的数据
- 文件输出与其极其相似：
	- 必须包含头文件fstream
	- 头文件fstream定一个了一个用于处理输出的ofstram类
	- 需要声明一个或多个ofstream变量（对象），并以自己喜欢的方式对其进行命名，条件是遵守常用的命名规则。
	- 必须指明名称空间std;例如，为引用元素ofstream，必须使用编译指令using或前缀std::。
	- 需要将ofstream对象与文件关联起来。为此，方法之一就是使用open()方法
	- 使用完文件后，应使用方法close()将其关闭
	- 可结合使用ofstream对象和运算符来输出各种类型的数据

- 注意：虽然头文件iostream提供了一个预先定义好的名为cout的ostream对象，但您必须声明自己的ofstream对象，为其命名，并将其同文件关联起来。
- 总之，文件输出的主要步骤如下。
	- 1.包含头文件fstream
	- 2.创建一个ofstream对象
	- 3.将该ofstream对象同一个文件关联起来
	- 4.就像使用cout那样使用该ofstream对象
```C++
// 6.15 -- outline.cpp
#include <iostream>
#include <fstream>
int main()
{
	using namespace std;

	char automobile[50];
	int year;
	double a_price;
	double d_price;

	ofstream outFile;  // create object for output
	outFile.open("carinfo.txt");  // associate with file

	cout << "Enter the make and model of automobile: ";
	cin.getline(automobile, 50);
	cout << "Enter the model year: ";
	cin >> year;
	cout << "Enter the original asking price: ";
	cin >> a_price;
	d_price = 0.913 * a_price;

	// display information on screen with cout
	cout << fixed;  // 用一般的方式输出浮点数，而不是科学计数法
	cout.precision(2);  // 设置精确度为2，并返回上一次的设置
	cout.setf(ios_base::showpoint);  // 显示浮点数小数点后的0
	cout << "Make and model: " << automobile << endl;
	cout << "Year: " << year << endl;
	cout << "Was asking $" << a_price << endl;
	cout << "Now asking $" << d_price << endl;

	// now do exact same things using outFile instead of cout
	outFile << fixed;
	outFile.precision(2);
	outFile.setf(ios_base::showpoint);
	outFile << "Make and model: " << automobile << endl;
	outFile << "Year: " << year << endl;
	outFile << "Was asking $" << a_price << endl;
	outFile << "Now asking $" << d_price << endl;

	outFile.close();   // done with file
	return 0;
}
```
- close(): 如果你忘记关文件，程序正常终止时将自动关闭它
- 如果文件已经存在，open()将首先截断该文件，即将其长度截短到零——丢其原有的内容，然后将新的输入加入到该文件中。（第17章介绍如何修改这种行为）

### 6.8.3 读取文本文件















