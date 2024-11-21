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
- [ ] 移动构造函数

```C++
// string的构造函数可以使用初始化列表：(用处不大)
string piano_man = { 'L', 'i', 's', 'z', 't'};
```

### 16.1.2 string类输入
- 对于类，很有帮助的另一点是，知道有哪些输入方式可用。对于C-风格字符串，有3种方式：
```C++
char info[100];

cin >> info;  // read a word
cin.getline(info, 100);  // read a line, discard \n
cin.get(info, 100);  // read a line, leave \n in queue
```

- 对于string对象，有两种方式：
```C++
string stuff;

cin >> stuff;  // read a word
getline(cin, stuff);  // read a line, discard \n
```

- 两个人版本的getline()都有一个可选参数，用于指定使用哪个字符来确定输入的边界：
```C++
cin.getline(info, 100, ':');  // read up to :, discard :
getline(stuff, ':');  // read up to :, discard :
```

- 在功能上，它们之间的主要区别在于，string版本的getline()将自动调整目标string对象的大学，使之刚好能够存储输入的字符：
```c++
char fname[10];
string lname;
cin >> fname;  // could be a problem if input size > 9 characters
cin >> lname;  // can read a very very long word
cin.getline(fname, 10); // may truncate input
getline(cin, fname);  // no truncation

```

- string的读取存在一些限制。第一个限制因素是string对象的最大长度，由常量string::npos指定。这通常是最大的unsigned int值，因此对于普通的交互式输入，这不会带来实际的限制；但如果您试图将整个文件的内容读取到单个string中，这可能成为限制因素。第二个限制因素是程序可以使用的内存里。

- string版本的getline()函数从输入中读取字符，并将其存储到目标string中，知道发生下列三种情况之一：
	- 到达文件尾，在这种情况下，输入流的eofbit将被设置，这意味着方法fail()和eof()都将返回true
	- 遇到分界字符（默认为\n），在这种情况下，将把分界字符从输入流中删除，但不存储它
	- 读取的字符达到最大允许值（string::npos和可供分配到内存字节数中较小的一个），在这种情况下，将设置输入流的failbit，这意味着方法fail()将返回true。
```C++
// 16.2 strfile.cpp -- read strings from a file
#include <iostream>
#include <fstream>
#include <string>
#include <cstdlib>

int main()
{
	using namespace std;

	ifstream fin;
	fin.open("tobuy.txt");
	if (fin.is_open() == false)
	{
		cerr << "Can't open file. Bye.\n";
		exit(EXIT_FAILURE);
	}

	string item;
	int count = 0;
	getline(fin, item, ':');
	while (fin)  // while input is good
	{
		++count;
		cout << count << ": " << item << endl;
		getline(fin, item, ':');
	}

	cout << "Done\n";
	fin.close();
	return 0;
}
```
- 注意文件中的换行符被视为后面字符的第一个字符。

### 16.1.3 使用字符串
- string类对全部6个关系运算符都进行了重载。对于每个关系运算符，都以三种方式重载，以便能够将string对象宇另一个string对象、C-风格字符串进行比较。
```C++
string snake1("cobra");
char snake3[20] = "anaconda";

if (snake1 == snake3)  // operator==(const string&, const char*)
{
	...
};

```

- size()和length()成员函数都将返回字符串中的字符数。
- 有两个功能相同函数的原因：length()成员来自较早版本string类，而size()则是为提供STL兼容性而添加的。
```C++
if (snake1.length() == snake2.size())
{
	cout << "Both strings have the same length.\n";
};
```

- `find_last_not_of()`方法在字符串中查找第一个不包含在参数中的字符。
```C++
// hangman.cpp -- some string methods
#include <iostream>
#include <string>
#include <cstdlib>
#include <ctime>
#include <cctype>
using std::string;
const int NUM = 26;
const string wordlist[NUM] = {"apiary", "beetle", "cereal",
    "danger", "ensign", "florid", "garage", "health", "insult",
    "jackal", "keeper", "loaner", "manage", "nonce", "onset",
    "plaid", "quilt", "remote", "stolid", "train", "useful",
    "valid", "whence", "xenon", "yearn", "zippy"};

int main()
{
    using std::cout;
    using std::cin;
    using std::tolower;
    using std::endl;
    
    std::srand(std::time(0));
    char play;
    cout << "Will you play a word game? <y/n> ";
    cin >> play;
    play = tolower(play);
    while (play == 'y')
    {
        string target = wordlist[std::rand() % NUM];
        int length = target.length();
        string attempt(length, '-');
        string badchars;
        int guesses = 6;
        cout << "Guess my secret word. It has " << length
            << " letters, and you guess\n"
            << "one letter at a time. You get " << guesses
            << " wrong guesses.\n";
        cout << "Your word: " << attempt << endl;
        while (guesses > 0 && attempt != target)
        {
            char letter;
            cout << "Guess a letter: ";
            cin >> letter;
            if (badchars.find(letter) != string::npos
                || attempt.find(letter) != string::npos)
            {
                cout << "You already guessed that. Try again.\n";
                    continue;
            }
            int loc = target.find(letter);
            if (loc == string::npos)
            {
                cout << "Oh, bad guess!\n";
                --guesses;
                badchars += letter; // add to string
            }
            else
            {
                cout << "Good guess!\n";
                attempt[loc]=letter;
                // check if letter appears again
                loc = target.find(letter, loc + 1);
                while (loc != string::npos)
                {
                    attempt[loc]=letter;
                    loc = target.find(letter, loc + 1);
                }
           }
            cout << "Your word: " << attempt << endl;
            if (attempt != target)
            {
                if (badchars.length() > 0)
                    cout << "Bad choices: " << badchars << endl;
                cout << guesses << " bad guesses left\n";
            }
        }
        if (guesses > 0)
            cout << "That's right!\n";
        else
            cout << "Sorry, the word is " << target << ".\n";

        cout << "Will you play another? <y/n> ";
        cin >> play;
        play = tolower(play);
    }

    cout << "Bye\n";

    return 0; 
}
```
- （这种例子太麻烦了，不再手敲了）

### 16.1.4 string还提供了哪些功能
- string库还有以下功能的函数：
	- **删除**字符串的部分或全部内容
	- 用一个字符串的部分或全部内容**替换**另一个字符串的部分或全部内容
	- 将数据**插入**到字符串中**或删除**字符串中的数据、
	- 将一个字符串的部分或全部内容与另一个字符串的部分或全部内容**进行比较**
	- 从字符串中提取**子字符串**
	- 将一个字符串中的内容**复制**到另一个字符串中
	- **交换**两个字符串的内容
- 这些函数中的大多数都被重载，以便能够同时处理C-风格字符串和string字符串。
- 附录F简要地介绍了string库中的函数。（不用看，大概率用不上，看UE的FString就行了）
```C++
// 16.4 str2.cpp -- capacity() and reserve()
#include <iostream>
#include <string>
int main()
{
    using namespace std;
    string empty;
    string small = "bit";
    string larger = "Elephants are a girl's best friend";

    cout << "Sizes:\n";
    cout << "\tempty: " << empty.size() << endl;  // 0
    cout << "\tsmall: " << small.size() << endl;  // 3
    cout << "\tlarger: " << larger.size() << endl;  // 34
    cout << "Capacities:\n";
    cout << "\tempty: " << empty.capacity() << endl; // 15
    cout << "\tsmall: " << small.capacity() << endl;  // 15
    cout << "\tlarger: " << larger.capacity() << endl;   // 47

    empty.reserve(50);
    cout << "Capacity after empty.reserve(50): " 
         << empty.capacity() << endl;  // 63
	// cin.get();
    return 0;
} 
```
- 超过内存块的大小，程序将分配一个大小为原来两倍的新内存块。（这也是为什么引擎要写自己的string，分配算法太简单粗暴）
- 方法`capacity()`返回当前分配给字符串的内存块的大小
- 方法`reserve()`让您能够请求内存块的最小长度
- 常见的策略是 2 倍增长 或 以某个倍数进行对齐。当你调用 reserve(50) 时，标准库可能预见到你将需要更大的空间，因此在分配时使用了某种倍增或对齐策略，结果分配的空间达到了 63 个字符，而不是恰好 50 个字符。


### 16.1.5 字符串种类
- 本节将string看作是基于char类型的。事实上，它是一个模板类。
- 模板`basic_string`有4个具体化，每个具体化都有一个`typedef`名称：
```C++
typedef basic_string<char> string;
typedef basic_string<wchar_t> wstring;
typedef basic_string<char16_t> u16string;  // C++11 表示UTF-16编码的16位宽字符。它能够表示大部分常见的Unicode字符
typedef basic_string<char32_t> u32string;  // C++11 表示UTF-32编码的32位宽字符。它能够表示所有Unicode字符，因为每个字符占用32位（4个字节）
```
- 后两种主要是为了支持Unicode字符集的编码和处理。主要用于需要处理Unicode字符的应用程序，特别是需要支持多语言或国际化的场景

- 想要详细了解更多，可以直接看源码并询问ChatGPT。

## 16.2 智能指针模板类
- 指针如果是一个对象，那么它就可以有一个析构函数，析构函数就可以在其过期的时候释放它指向的内存。这就是智能指针背后的思想。(过期的地方一般是：本地变量从栈中删除)
- 三种智能指针：
	- auto_ptr：C++98，已被抛弃
	- unique_ptr
	- shared_ptr

### 16.2.1 使用智能指针
- 这三个智能指针模板都定义了类似指针的对象，可以将new获得（直接或间接）的地址赋给这种对象。当智能指针过期时，其析构函数将使用delete来释放内存，也就是说这些内存将被**自动释放**。
- 要创建智能指针类，必须包含头文件```<memory>```
- 每个智能指针都放在一个代码块内，这样离开代码块时，智能指针将过期。
```C++
// 16.5 smrtptrs.cpp -- using three kinds of smart pointers
// requires support of C++11 shared_ptr and unique_ptr
#include <iostream>
#include <string>
#include <memory>

class Report
{
private:
	std::string str;
public:
	Report(const std::string s) : str(s) { std::cout << "Object created!\n"; }
	~Report() { std::cout << "Object deleted!\n"; }
	
	void comment() const { std::cout << str << "\n"; }
};

int main()
{
	{
		std::auto_ptr<Report> ps(new Report("using auto_ptr"));
		ps->comment();
	}
	
	{
		std::shared_ptr<Report> ps(new Report("using shared_ptr"));
		ps->comment();
	}
	
	{
		std::unique_ptr<Report> ps(new Report("using unique_ptr"));
		ps->comment();
	}

	return 0;
}
```
- 所有智能指针类都有一个```explicit```构造函数，该构造函数将指针作为参数。因此不需要自动将指针转换为智能指针对象
- ```explicit```禁止隐式类型转换
- 智能指针的很多方面都类似于常规指针。例如：如果ps是一个智能指针对象：
	- 则可以对它执行解除引用操作(*ps)
	- 用它来访问结构成员（ps->puffIndex)
	- 将它赋给指向相同类型的常规指针。
	- 还可以将一个智能指针赋值给另一个智能指针。但这将引发一个问题，下节讨论。

- 全部的智能指针都应该避免下面一点：
```C++
string vacation("I wandered lonely as a cloud.");
shared_ptr<string> pvac(&vacation);  // NO!
```
- pvac过期时，程序将把delete运算符应用于非堆内存，这是错误的。

### 16.2.2 有关智能指针的注意事项
- 两个智能指针指向同一个对象时，该对象会被析构两次。解决方法有多种：
	- 定义赋值运算符，使之执行深复制。这样两个指针将指向不同的对象，其中的一个对象是另一个对象的副本。
	- 建立所有权（ownership）概念，对于特定的对象，只能有一个指针指针可拥有它，这样只有拥有对象的智能指针的构造函数会删除该对象。然后，赋值操作转让所有权。这就是用于```auto_ptr```和```unique_ptr```的策略，但```unique_ptr```的策略更严格。
	- 创建智能更高的指针，跟踪引用特定对象的智能指针数。这称为引用计数（reference counting）。例如，赋值时，计数将加1，而指针过期时，计数将减1.仅当最后一个指针过期时，才调用delete。这是```shared_ptr```采用的策略。
- 当然，同样的策略也适用于复制构造函数
```C++
// 16.6 fowl.cpp -- auto_ptr a poor choice
#include <iostream>
#include <string>
#include <memory>

using namespace std;

int main()
{
	auto_ptr<string> films[5] =
	{
		auto_ptr<string>(new string("Flow Balls")),
		auto_ptr<string>(new string("Dulk Walks")),
		auto_ptr<string>(new string("Chichken Runs")),
		auto_ptr<string>(new string("Turkey Errors")),
		auto_ptr<string>(new string("Goose Eggs"))
	};

	auto_ptr<string> pwin;
	pwin = films[2];  // films[2] loses ownership

	cout << "The nominess for best avian baseball film are\n";

	for (int i = 0; i < 5; i++)
	{
		cout << *films[i] << endl;
	}

	cout << "The winner is " << *pwin << "!\n";
	cin.get();
	return 0;
}  // 程序报错
```
- 问题在于```pwin = films[2];```将所有权从```films[2]```转让给```pwin```。这导致```films[2]```不再引用该字符串。当程序打印时发现是个空指针，然后就报错了。
- 用```shared_ptr```就不会报错了。因为```shared_ptr```里面是计数原则
- 用```unique_ptr```也会报错，只不过赋值需要改一下。（原作者意思是编译阶段就会报错，因为```unique_ptr```不能直接赋值）
```C++
// unique_ptr 赋值需要用到std::move()函数，不过最终结果和auto_ptr是一样的，都报错。
pwin = std::move(films[2]);
```

### 16.2.3 unique_ptr为何优于auto_ptr
- ```unique_ptr```比```auto_ptr```更安全。因为编辑阶段错误比潜在的程序崩溃更安全。
- 编译器却允许这样的赋值：
```C++
unique_ptr<string> demo(const char* s)
{
	unique_ptr<string> temp(new string(s));
	return temp;
}

unique_ptr<string> ps;
ps = demo("Uniquely special");
```
- demo()返回一个临时unique_ptr，然后ps接管了原本归返回的unique_ptr所有的对象，而返回的unique_ptr被销毁。这没有问题，因为ps拥有了string对象的所有权。但这里的另一个好处是，demo()返回的临时unique_ptr很快被销毁，没有机会使用它来访问无效的数据。换句话说，没有理由禁止这种赋值。神奇的是，编译器确实允许这种赋值！
- 总之，程序试图将一个unique_ptr赋给另一个时，如果源unique_ptr是一个临时右值，编译器允许这样做；如果源unique_ptr将存在一段时间，编译器将禁止这样做。

```C++
using namespace std;
unique_ptr<string> pu1(new string("Hi ho!"));
unique_ptr<string> pu2;
pu2 = pu1;  // #1 not allowed
unique_ptr<string> pu3;
pu3 = unique_ptr<string>(new string("Yo!"));  // #2 allowed
```
- 语句#1将留下悬挂的```unique_ptr(pu1)```，这可能导致危害。语句#2不会留下悬挂的```unique_ptr```。
- 这种随情况而异的行为表明，```unique_ptr```优于允许两种赋值的```auto_ptr```。这也是禁止（只是一种建议，编译器并不禁止）```auto_ptr```的原因。
- [ ] 仅当以非智能的方式使用遗弃的智能指针...
- ```unique_ptr```相比于```auto_ptr```还有一个优点，它有一个可用于数组的变体。auto_ptr是new与delete，而unique_ptr是new[ ] 与delete[ ]

- 警告：
	- 使用new分配内存时，才能使用auto_ptr和shared_ptr，使用new[ ] 分配内存时，不能使用它们
	- 不使用new分配内存时，不能使用auto_ptr和shared_ptr
	- 不使用new或new[ ] 分配内存时，不能使用unique_ptr

### 16.2.4 选择智能指针
share_ptr:
- 如果程序要使用多个指向同一对象的指针，应选择shared_ptr。
- STL容器包含指针。很多STL算法都支持复制和赋值操作，这些操作可用于shared_ptr，但不能用于unique_ptr（编译器发出警告）和auto_ptr(行为不确定。)
unique_ptr:
- 如果程序不需要多个指向同一个对象的指针，则可使用unique_ptr。如果使用函数使用new分配内存，并返回指向该内存的指针，将其返回类型声明为unique_ptr是一个不错的选择。
- 可将unique_ptr存储到STL容器中，只要不调用将一个unique_ptr复制或赋给另一个的方法或算法（如sort()）

```C++
unique_ptr<int> make_int(int n)
{
	return unique_ptr<int>(new int(n));
}

void show(unique_ptr<int>& pi)  // pass by reference
{
	cout << *a << ' ';
}

int main()
{
...
	vector<unique_ptr<int>> vp(size);
	for (int i = 0; i < vp.size(); i++)
		vp[i] = make_int(rand() % 1000);  // copy temporary unique_ptr
	vp.push_back(make_int(rand() % 1000));  // ok because arg is temporary
	for_each(vp.begin(), vp.end(), show);  // use for_each()
...
}
```

- 如果按值而不是按引用给show()传递对象，for_each()语句将非法，因为这将导致使用一个来自vp的非临时unique_ptr初始化pi，而这是不允许的。
- 在```unique_ptr```为右值时，可将其赋给```shared_ptr```。这与将一个unique_ptr赋给另一个需要满足的条件相同。与前面一样，下面的代码中，make_int()的返回类型为```unique_ptr<int>```
```C++
unique_ptr<int> pup(make_int(rand() % 1000));  // ok
shared_ptr<int> spp(pup);  // not allowed pup an left value
shared_ptr<int> spr(make_int(rand() % 1000));  // ok
```
- 模板shared_ptr包含一个显式构造函数，可用于将右值unique_ptr转换为shared_ptr。shared_ptr将接管原来归unique_ptr所有的对象。
- 在满足unique_ptr要求的条件时，也可使用auto_ptr，但unique_ptr是更好的选择


## 16.3 标准模板库
- STL提供了一组表示容器、迭代器、函数对象和算法的模板。
	- **容器**是一个数组类似的单元，可以存储若干个值。STL容器是同质的，即存储的值的类型相同；
	- **算法**是完成特定任务（如对数组进行排序或在链表中查找特定值）的处方
	- **迭代器**能够用来遍历容器的对象，与能够遍历数组的指针类似，是广义指针。
	- **函数对象**是类似于函数的对象，可以是类对象或函数指针（包括函数名，因为函数名被用作指针）
- STL不是面向对象的编程，而是不同的编程模式——泛型编程（generic programming）。
- 附录G对各种STL方法和函数进行了总结。

### 16.3.1 模板类vector
- 要使类成为通用的，应将它设计为模板类，STL正是这样做的——在头文件vector（以前为vector.h）中定义了vector模板。
- vector模板使用动态内存分配，因此可以用初始化参数来指出需要多少vector。

#### 分配器
- 与string类相似，各种STL容器模板都接受一个可选的模板参数，该参数指定使用那个分配器对象来管理内存。例如，vector模板的开头与下面类似：
```C++
template <class T, class Allocator = allocator<T> >
class vector 
{
	...
}
```
- 如果省略改模板参数的值，则容器模板将默认使用allocator<T>类。这个类使用new和delete。
- **ChatGPT解释**：分配器（Allocator）在C++ STL（标准模板库）中用于管理容器内存的分配和释放。每种 STL 容器（如 vector、list、map 等）都可以接受一个可选的模板参数，用于指定自定义的分配器。默认情况下，这个参数是 allocator<T>，它使用标准的 new 和 delete 来分配和释放内存。

```C++
// 16.7 vect1.cpp -- introducing the vector template
#include <iostream>
#include <string>
#include <vector>

constexpr int NUM = 5;

int main()
{
	using std::cin;
	using std::cout;
	using std::endl;

	std::vector<int> ratings(NUM);
	std::vector<std::string> titles(NUM);

	cout << "You will do exactly as told. You will enter\n"
		<< NUM << " book titles and your ratings (0-10).\n";
	int i;
	for (i = 0; i < NUM; i++)
	{
		cout << "Enter title #" << i + 1 << ":";
		getline(cin, titles[i]);
		cout << "Enter your rating(0-10):";
		cin >> ratings[i];
		cin.get();
	}

	cout << "Thank you. You entered the following:\n"
		<< "Rating\tBook\n";

	for (i = 0; i < NUM; i++)
	{
		cout << ratings[i] << "\t" << titles[i] << endl;
	}
		
	return 0;
}
```

### 16.3.2 可对vector执行的操作
- 所有的 STL 容器都提供了一些基本方法，其中包括 size()--返回容器中元素数目、swap()--交换两个容器的内容、begin()--返回一个指向容器中第一个元素的迭代器、end()--返回一个表示超过容器尾的迭代器。

- 什么是迭代器?它是一个广义指针。事实上，它可以是指针，也可以是一个可对其执行类似指针的操
作--如解除引用(如 operator*())和递增(如 operator++( ))--的对象。稍后将知道，通过将指针广义化为迭代器，让STL能够为各种不同的容器类（包括那些简单指针无法处理的类）提供统一的接口。

- 什么是超过结尾(past-the-end)：它是一种迭代器，指向容器最后一个元素后面的那个元素。如end()。

- `push_back(Elem)`是只有某些 STL容器才有的方法，将元素添加到vector末尾。这样做时，它将负责内存管理，增加vector长度，使之能够容纳新的成员。
- `erase(begin, end)`删除给定区间的元素。**第二个迭代器位于区间终止处的后一个位置**。即左闭右开的区间内。
- `old_v.insert(old.begin(), new_v.begin() + 1, new_v.end());` 第一个参数指定了新元素的插入位置，第二个和第三个迭代器参数定义了被插入区间，这个区间通常是另一个容器对象的一部分。
```C++
// 这种情况下拥有超尾非常方便，使得在尾部添加元素非常简单
old_v.insert(old_v.end(), new_v.begin(), new_v.end());
```

```C++
// 16.8 vect2.cpp -- methods and iterators
#include <iostream>
#include <string>
#include <vector>
struct Review
{
	std::string title;
	int rating;
};

bool FillReview(Review& rr);
void ShowReview(const Review& rr);

int main()
{
	using std::cout;
	using std::vector;

	vector<Review> books;
	Review temp;

	while (FillReview(temp))
	{
		books.push_back(temp);
	}
	
	int num = books.size();

	if (num > 0) 
	{
		cout << "Thank you. You entered the following:\n"
			<< "Rating\tBook\n";
		
		for (int i = 0; i < num; i++)
		{
			ShowReview(books[i]);
		}

		cout << "Reprising:\n" << "Rating\tBook\n";
		vector<Review>::iterator pr;

		for (pr = books.begin(); pr != books.end(); pr++)
		{
			ShowReview(*pr);
		}

		vector <Review>oldlist(books);
		
		// copy constructor used
		if (num > 3) 
		{
			// remove 2 items
			books.erase(books.begin() + 1, books.begin() + 3);
		}

		cout << "After erasure:\n";
		for (pr = books.begin(); pr != books.end(); pr++) 
		{
			ShowReview(*pr);
		}

		//insert l item
		books.insert(books.begin(), oldlist.begin() + 1, oldlist.begin() + 2);
		cout << "After insertion:\n";

		for (pr = books.begin(); pr != books.end(); pr++) 
		{
			ShowReview(*pr);
		}

		books.swap(oldlist);
		cout << "Swapping oldlist with books:\n";
		for (pr = books.begin(); pr != books.end(); pr++) 
		{
			ShowReview(*pr);
		}
	}
	else 
	{
		cout << "Nothing entered, nothing gained.\n";
	}

	return 0;
}

bool FillReview(Review& rr)
{
	std::cout << "Enter book title(quit to quit):";
	std::getline(std::cin, rr.title);

	if (rr.title == "quit")
	{
		return false;
	}
	
	std::cout << "Enter book rating:";
	std::cin >> rr.rating;

	if (!std::cin)
	{
		return false;
	}

	// get rid of rest of input line
	while (std::cin.get() != '\n')
	{
		continue;
	}

	return true;
}

void ShowReview(const Review& rr)
{
	std::cout << rr.rating << "\t" << rr.title << std::endl;
}

```

### 16.3.3 可对vector执行的其他操作
- STL并不包含很多跟算法相关的操作，如搜索、排序、随机排序等。而是通过定义一个适用于所有容器的库函数来实现，这样做提升效率，免得每个容器都要自己定义一遍。这些库函数都包含在`#include <algorithm>`文件中。

- `for_each()`: 遍历容器。
- `random_shuffle()`: 打乱容器。
- `sort()`: 对容器进行排序，也可以指定谓词函数。
- [ ] **全排序**和**完整弱排序**区别：
```C++
// 16.8 vect2.cpp -- methods and iterators
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

struct Review
{
	std::string title;
	int rating;
};

bool operator<(const Review& rl, const Review& r2);
bool worseThan(const Review& rl, const Review& r2);
bool FillReview(Review& rr);
void ShowReview(const Review& rr);

int main()
{
	using std::cout;

	std::vector<Review> books;
	Review temp;

	while (FillReview(temp))
	{
		books.push_back(temp);
	}

	if (books.size() > 0)
	{
		cout << "Thank you. You entered the following:\n"
			<< "Rating\tBook\n";
		std::for_each(books.begin(), books.end(), ShowReview);

		std::sort(books.begin(), books.end());
		cout << "Sorted by title:\nRating\tBook\n";
		std::for_each(books.begin(), books.end(), ShowReview);

		std::sort(books.begin(), books.end(), worseThan);
		cout << "Sorted by rating:\nRating\tBook\n";
		std::for_each(books.begin(), books.end(), ShowReview);

		std::random_shuffle(books.begin(), books.end());
		cout << "After shuffling:\nRating\tBook\n";
		std::for_each (books.begin(), books.end(), ShowReview);
	}
	else 
	{
		cout << "No  enteres. ";
	}

	return 0;
}

bool operator<(const Review& rl, const Review& r2)
{
	if (rl.title < r2.title)
		return true;
	else if (rl.title == r2.title && rl.rating < r2.rating)
		return true;
	else
		return false;
}

bool worseThan(const Review& rl, const Review& r2)
{
	if (rl.rating < r2.rating)
		return true;
	else
		return false;
}

bool FillReview(Review& rr)
{
	std::cout << "Enter book title(quit to quit):";
	std::getline(std::cin, rr.title);

	if (rr.title == "quit")
	{
		return false;
	}
	
	std::cout << "Enter book rating:";
	std::cin >> rr.rating;

	if (!std::cin)
	{
		return false;
	}

	// get rid of rest of input line
	while (std::cin.get() != '\n')
	{
		continue;
	}

	return true;
}

void ShowReview(const Review& rr)
{
	std::cout << rr.rating << "\t" << rr.title << std::endl;
}
```

### 16.3.4 基于范围的for循环(C++11)
- 一般的遍历操作，auto可以自动类型推导：
```C++
for (auto x : books)
{
	ShowReview(x);
}
```
- 修改容器内元素的操作
```C++
for (auto& x : books)
{
	InflateReview(x);
}
```
- Self：这个工作中用到挺多的，也很好用。作者竟然一笔带过？而是追着`for_each`讲了半天。可能的原因：写这本书的时候C++11刚出，这种循环还没流行起来。

## 16.4 泛型编程
- STL是一种泛型编程（generic programming）。面向对象编程关注的是编程的数据方面，而泛型编程关注的是算法。它们之间的共同点是抽象和创建可重复代码，但它们的理念绝然不同。
- 旨在编写独立于数据类型的代码：

  
### 16.4.1 为何使用迭代器

### 16.4.2 迭代器类型

### 16.4.3 迭代器层次结构

### 16.4.4 概念、改进和模型

### 16.4.5 容器种类
- [ ] 这里编号错了，到时候看书本的提示吧。


## 16.5 函数对象
### 16.5.1 函数符概念

### 16.5.2 预定义的函数符

### 16.5.3 自适应函数符和函数适配器


## 16.6 算法
### 16.6.1 算法组

### 16.6.2 算法的通用特征

### 16.6.4 函数和容器方法

### 16.6.5 使用STL


## 16.7 其他库
### 16.7.1 vector、valarray和array

### 16.7.2 模板initializer_list(C++11)

## 16.8 总结

## 16.9 复习题





