
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














