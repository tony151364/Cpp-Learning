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









