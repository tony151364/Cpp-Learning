## [类与对象](https://www.bilibili.com/video/BV1jm4y1w7pa)

### 103.简单使用类
- 1.在类中使用枚举，作用域是整个类
- 2.在实际开发中，用结构体描述纯粹的数据，用类描述对象
- 3.在类的声明中定义的函数都将自动成为内联函数

### 104.构造函数和析构函数
- 1.在构造函数名后面加括号和参数不是调用构造函数，是创建匿名对象。
- 2.以下两行代码有本质区别：
```C++
CGirl girl = CGirl("西施", 20);  // 显式创建对象

// 一共创建了两个对象
CGirl girl;               // 创建对象
girl = CGirl("西施", 20);  // 创建匿名对象，然后给现有的对象赋值
```
- 3.用 new/delete 创建/销毁 对象时，也会调用 构造/析构 函数
- 4.不建议在 构造/析构 函数中写太多的代码，因为很多代码都是重复的，这时候可以把这些重复的代码放到一个成员函数（如：initData()）中，然后在构造函数中调用该成员函数。
- 5.除了初始化，不建议让构造函数做太多的工作，一般只作一些“只会成功不会失败”的工作。因为没有返回值，无法知道它的调用结果，所以复杂的工作交给它不太合适。
- 6.C++11 支持统一初始化列表
```C++
CGirl girl {"西施", 20}; 
CGirl girl= CGirl("西施", 20);
CGirl* girl =  new CGirl{"西施", 20};
```
- 7.如果类的成员也是类，创建对象时，先构造成员类；销毁对象时，先析构成员类

### 105.拷贝构造函数
- 在C++中，用一个已存在的对象创建新的对象，不会调用（普通）构造函数，而是调用拷贝构造函数。如果类中没有定义拷贝构造函数，编译器将提供一个拷贝构造函数，它的功能是把已存在对象的成员变量赋值给新对象的成员变量。
```C++
类名(const 类名& value)
CGirl(const CGirl& girl)
{
  m_name = 漂亮的+gg.m_name;
  m_age = gg.m_age - 1;
  cout << "..." << endl;
}
```
- 以值传递的方式调用函数时，如果实参为对象，调用拷贝构造函数(形参 = 实参）
- **函数以值的方式返回对象时，可能会调用拷贝构造函数（VS会调用；Linux不会，因为g++编译器做了优化，没有销毁函数中返回的那个对象）**
- 拷贝构造函数也可以重载
- 如果类中重载了拷贝构造函数却没有定义默认的拷贝构造函数，编译器也会提供默认的拷贝构造函数，**这一点和普通构造函数不一样**

### 105.5浅拷贝和深拷贝
- 深拷贝：
  - 先分配内存
  - 再拷贝数据：简单的类型可以直接赋值，复杂的可以用memcpy()函数
```C++
CGirl(const CGirl& gg)
{
  m_name = gg.m_name;
  m_age = gg.m_age;
  
  m_ptr = new int;  // 分配内存
  // *m_ptr = *gg.m_ptr;  // 拷贝数据1
  memcpy(m_ptr, gg.m_ptr, sizeof(int));  // 拷贝数据2
}  
```
### 106.初始化列表
```C++
CGirl():m_name("西施"), m_age(23)
{
  cout << "调用了CGirl()的构造函数\n";
}
```
- 注意：
  - 1.如果成员已经在初始化列表中，则不应该在构造函数中再次赋值 ,因为会将初始化列表的值覆盖掉
  - 3.初始化列表和赋值有本质的区别，如果成员是类，使用初始化列表调用的是成员类的拷贝构造函数，而赋值则是先创建成员类的对象（将调用成员类的普通构造函数），然后在赋值；如果不采用初始化列表，调用的是普通构造函数。如果采用初始化列表，调用的是拷贝构造函数。；如果没有初始化列表，对象的初始化和赋初始值是两步操作。如果采用了初始化列表，对象的初始化和赋值是一步操作。**所以采用初始化列表的效率更高**
  - 4.如果成员是类，初始化列表对性能略有提升
  - 5.如果成员是常量和引用，必须使用初始化列表，因为常量和引用只能在定义的时候初始化。
  - 6.如果成员是没有默认构造函数的类，则必须使用初始化列表。
  - 7.拷贝构造函数也可以有初始化列表，比较少用。
  - 8.类的成员变量可以不出现在初始化列表中，那么编译器就会采用默认的初始化方法。

### 107.const修饰成员
- 在类的成员函数后面加*const*关键字，表示在成员函数中保证不会修改调用对象的成员变量。
- 注意：在实际开发中，如果成员函数不会修改成员变量，就应该加const修饰，这是C++的编程规范，就像用const修饰函数的形参一样
- 注意：
  - 1.*mutable*可以突破*const*的限制，被*mutable*修饰的成员变量，将永远处于可变的状态，在*const*修饰的函数中，*mutable*成员也可以被修饰；也就是说，如果在声明时加了*mutable*关键字，那么即便成员函数加了*const*修饰，也能修改成员变量的值
  - 2.非*const***成员函数**可以调用*const*成员函数和非*const*成员函数
  - 3.*const*成员函数不能调用非*const*成员函数
- **提问：**
  - 1.为什么要保护类的成员变量不被修改？
  - 2.为什么用*const*保护了成员变量，还要再定义一个*mutable*关键字来突破*const*的封锁线？
  - 3.到底有没有必要使用*const*和*mutable*这两个关键字？
  - 4.常对象只能访问加了*const*修饰的成员函数；对构造函数和析构函数加*const*修饰是非法的
- **回答：**
  - 保护类的成员变量不在成员函数中被修改，是为了保证模型的正确性，通过用*const* 关键字来避免在函数中错误的修改了类对象的状态。并且在所用使用该成员函数的地方都可以更准确的预测到使用该成员函数带来的影响。而*mutable*则是为了突破*const*的封锁线，让累的一些次要的或者是辅助性的成员变量随时可以被修改。没有使用*const*和mutable当然也没有错，它们是只是给建模工具更多的设计约束性和灵活性，而且程序员可以把更多的逻辑检查问题交给编译器和建模工具去做，从而减轻程序员的负担。

### 108.this指针
- 如果类的成员函数中涉及多个对象，在这种情况下需要使用this指针。
- this指针存放了对象的地址，**它被作为隐藏参数传递给了成员函数**，指向调用成员函数的对象（调用者）
- this指针可以解决成员变量名与函数名相同的问题，当然用m_前缀或后缀的方法更好。
- \*this表示整个对象
```C++
int main()
{
  CGirl g1("西施",5),  g2("西瓜",3),  g3("冰冰",1),  g4("幂幂",5),  g5("金莲",2);
  const CGirl& g = g1.pk(g2).pk(g3).pk(g4).pk(g5);  // cout 也是类似的
  g.show();
}
```

### 109、静态成员
- 类的静态成员包括静态成员变量和静态成员函数。
- **如果把类的成员声明为静态的，就可以把它与类的对象独立开来（静态成员不属于对象）**
- 静态成员：
	- 使用*static*关键字进行修饰
	- 使用范围解析运算符*::*就可以访问，不需要创建对象。
	- **私有静态成员在类外无法访问**。不然类的静态成员与全局变量就没区别了
- 静态成员变量：
	- 可以实现多个对象间的数据共享，比全局变量更有安全性
	- 静态成员变量不会在创建对象的时候初始化，必须在程序的全局区用代码清晰地初始化（用范围解析运算符*::*）
	- const静态成员变量可以在定义类的时候初始化
- 静态成员函数：
	- 在静态成员函数中只能访问静态成员
	- 静态成员函数没有this指针（因为没对象）
	- 在非静态函数中可以访问静态成员（不用加范围解析运算符了）

### 110.简单对象模型
- C++中:
  - 有两种数据成员：nonstatic static;
  - 三种成员函数：nonstatic static virtual
- 静态成员变量属于类，在整个程序中只有一份，不计算在对象的大小范围之内
- 静态变量和全局变量和地址在一起，都在全局数据区
- 成员函数的地址和普通函数的地址是放在一起的
- 用空指针可以调用没有用到this指针的非静态成员函数。（如果成员函数中没有用到非静态成员变量，就可以用空指针去调用它）（其实这时候就是个C描述的普通函数了）
```C++
Girl girl = nullptr;
girl->showname();
```
- 在没有创建对象的情况下，访问非静态成员变量就是访问空指针。用空指针调用成员函数的情况很少见，为了保证程序的稳定性，在成员函数中可以增加判断this指针是否为空的代码
- 对象的地址是第一个非静态成员变量的地址，如果类中没有非静态成员变量，编译器会隐含增加一个1字节的占位成员
- [ ] 类的成员函数是分开存储的，不论对象是否存在，都占用存储空间，在内存中只有一个副本，也不计算在对象大小之内

## 使用类

### 111.友元
- 友元提供了另一访问类的私有成员的方案。
- 友元有三种：
  - 友元全局函数：在友元全局函数中，可以访问另一个类的所有成员
  - 友元类：在友元类所有成员函数中，可以访问另一个类的所有成员
  - 友元成员函数
#### （1） 友元全局函数 
- 声明友元的代码放在类中什么地方都可以，不受public和private关键字的约束，一般放在最上面
```C++
#include<iostream>
using namespace std;

class CGirl
{
    friend int main();
    friend void func();
    friend class CBoy;
public:
    string m_name;
    CGirl() { m_name = "西施"; m_xw = 87; }
    void showname() { cout << "姓名: " << m_name << endl; }
private:
    int m_xw;
    void showxw() { cout << "胸围：" << m_xw << endl; }
};

class CBoy
{
public:
    void func(const CGirl& g)
    {
        cout << "我女朋友的姓名是：" << g.m_name << endl;
        cout << "我女朋友的胸围是: " << g.m_xw << endl;
    }
};

void func()
{
    CGirl g;
    g.showxw();
}

int main()
{
    func();

    CGirl g;
    CBoy b;
    b.func(g);
}
```
- 这是非常规的操作，一般把普通的全局函数作为类的友元函数

#### （2）友元类
- 在友元类所有成员函数中，都可以访问另一个类的所有成员

- 友元类注意事项：
  - 友元关系不能被继承
  - 友元关系是单向的，不具备交换性、传递性 

#### (3) 友元成员函数
- 在友元成员函数中，可以访问另一个类的所有成员
- 如果要把男朋友类CBoy的某成员函数声明为超女类CGirl的友元，声明和定义的顺序如下：(友元成员函数和友元全局函数的原理是一样的，但是写代码的时候要注意声明和定义的顺序）
```C++
// 第一步：把超女类的声明前置
class CGirl;  // 前置声明 
// 第二步：把CBoy放在CGirl之前
// 第三步：把男朋友类的成员函数的函数体从类的定义中拿出来，放在定义超女类的后面
class CBoy {.....};
// 第四步：把男朋友类的成员函数声明为超女类的友元函数
class CGirl {.....};
```

```C++
#include<iostream>
using namespace std;

class CGirl;

class CBoy
{
public:
    void func1(const CGirl& g);

    void func2(const CGirl& g);
};

class CGirl
{
    friend void CBoy::func1(const CGirl& g);
    friend void CBoy::func2(const CGirl& g);

public:
    string m_name;
    CGirl() { m_name = "西施"; m_xw = 87; }
    void showname() { cout << "姓名: " << m_name << endl; }

private:
    int m_xw;
    void showxw() { cout << "胸围：" << m_xw << endl; }
};

void CBoy::func1(const CGirl& g)
{
    cout << "func1()我女朋友的胸围是: " << g.m_xw << endl;
}

void CBoy::func2(const CGirl& g)
{
    cout << "func2()我女朋友的胸围是: " << g.m_xw << endl;
}

int main()
{
    CGirl g;
    CBoy b;
    b.func1(g);
    b.func2(g);
}
```

### 121.自动类型装换
- **自动类型转换：** 某些类型的转换编译器可以隐式的进行，不需要程序员干预
- **强制类型转换：** 有些类型的转换需要程序员显示指定
- 若内置类型兼容，C++可以自动类型转换。大类型转小类型可能丢失精度（int -> long）

#### (1) 自动类型转换
自动类型转换的规则如下：
- 如果一个表达式中出现了不同类型操作数的混合运算，较低类型将自动想较高类型转换
- 当表达式中含有浮点数是，所有操作数都将转换为浮点数.
- 赋值运算的右值类型与左值类型不一致时，将右值类型提升/降低为左值类型。
- 赋值运算右值超出了左值类型的表示范围，把该右值截断后赋值给左值，所得结果可能毫无意义

#### (2) 强制类型转换
- 强制类型转换又称显示类型转换
- 语法：``` (目标类型)表达式。dynamic_cast<RadioButton*>(GetControl("btn_1")); ```
- 注意：
	- 如果用了强制类型转换，编译的警告信息将不再出现
	- 类型转换的优先级比较高，如果没把握就加上括号()
```C++
// C++不自动转换不兼容类型，下面语句非法
int* ptr = 8;
// 可以用强转。但是一般转换是要有意义的
int* ptr = (int*)8;
```

- 这节课学习把某种数据类型转换为类的类型

```C++
int main()
{
	// method 1
	CGirl g1(8);			// 常规的写法（有参构造）
	// method 2
	CGirl g1 = CGirl(8);	// 显式转换（有参构造）
	// method 3
	CGirl g1 = 8;			// 隐式转换（有参构造）
	// method 4
	CGirl g1;				// 创建对象（调用了默认无参构造函数）
	g1 = 8;					// 用CGirl(8)创建临时对象，再赋给g（再一次调用有参构造函数）
}
```

#### （3）强制类转换的特点：
- 1）一个类可以有多个转换函数
- 2）多个参数的构造函数，除第一个参数外，如果其他参数都有缺省值，也可以作为转换函数 
- 3）CGirl(int)的隐式转换的场景：
	- 将CGirl对象初始化为int值时。 ```C++ CGirl g1 = 8;  ```
	- 将int值赋给CGirl对象时。  ```C++ CGirl g1; g1 = 8; ```
	- 将int值传递给接受CGril参数的函数时。``` CGril function(CGril g) { return g}; function(8); ```
	- 返回值被声明为CGril的函数试图返回int值时。``` CGril function(int n) { return n}; function(8); ```
	- 在上述任意一种情况下，使用可转换为int类型的内置类型时（char）
- 4）如果自动类型转换有二义性，编译将报错
- 5）explicit关键字用于关闭这种自动特性，但仍允许显式转换
	-实际开发中如果强调的是构造，建议使用explicit，如果强调的是类型转换，则不适用explicit
```C++
explicit CGirl(int bh);
CGirl g = 8;  // wrong
CGirl g = CGirl(8);  // 显式转换，right
CGirl g = (CGirl)8;  // 显式转换，right
```

### 122.转换函数
- 构造函数只用于从某种类型转换到类类型的转换，如果要进行相反的转换，可以使用特殊的运算符函数-转换函数
- 语法：operator 数据类型()
- **注意：转换函数必须是类的成员函数；不能指定返回值类型；不能有参数**
```C++
class CGril
{
	int m_bh;  // 编号
	string m_name;
	double m_weight;
	
	// 默认构造函数
	CGirl() { m_bh = 8; m_name = "西施"; m_weight = 50.7; }
	
	explicit operator int() { return m_bh; } // 加了explicit就不能隐式转换了，这里编译器会选择double转换
	operator string() { return m_name; }
	operator double() { return m_weight; }
}

int main()
{
	CGirl g;
	int a = (int)g;  // 显式转换
	string b = g;  // 隐式转换
	double c = g;  // 隐式转换
	
	cout << "a: " << a << endl;
	cout << "b: " << b << endl;
	cout << "c: " << c << endl;
	
	short d = g;  // 没定义，产生二义性，因为int 和 double 都可以转换为short（我觉得应该用int，毕竟两个最相似）
	short d = (int)g;  // OK
}
```
- 这节课讲的意义不大，一般开发采用成员函数而不是转换函数
- 警告：应谨慎的使用隐式转换函数。通常，最好选择仅在被显式地调用时才会执行的成员函数。因为：**看视频！**

## [类继承](https://www.bilibili.com/video/BV1Qe4y1Y7jU)

### 123.继承的基本概念
#### 使用继承的场景
- 1）如果新创建的类与现有的类相似，只是多出若干成员变量或成员函数时，可以使用继承
```C++
#include <iostream>
#include <cstring>

using namespace std;

class CAllComers  // 海选者报名类
{
public:
 std::string m_name;
 std::string m_tel;

 CAllComers() { m_name = "某女"; m_tel = "不详"; }
 void sing() { cout << "我是一只小小鸟。\n"; }
 void setname(const string& name) { m_name = name; }
 void sette(const string& tel) { m_tel = tel; }
};

class CGirl :public CAllComers
{
public:
 int m_bh;  // 编号
 CGirl() { m_bh = 8; }
 void show() { cout << "编号：" << m_bh << "，姓名：" << m_name
  << "，联系电话：" << m_tel << endl; }
};

int main()
{
 CGirl g;
 g.setname("西施");
 g.show();
}
```
- 2）当需要创建多个类是，如果他们拥有很多相似的成员变量或成员函数，可以将这些类共同的成员提取出来，定义为基类，然后从基类继承

```C++
class Sort {  // 排序算法的基类
 int m_data[30];  // 待排序的数组
 void print();  // 显示排序后的结果
};

class BubbleSort :public Sort {  // 冒泡排序
 void sort();
};

class SelectSort :public Sort {  // 选择排序
 void sort();
};

class ShellSort :public Sort {  // 希尔排序
 void sort();
};

```

### 124.继承方式
- 访问权限：public、protected（派生类中可以访问，main函数中不能访问，也就是派生类外部是不能访问的）、private
- 继承方式：public、protected、private
 - public 继承：基类的public和protected在派生中一样，private是不能访问
 - protectd 继承：public的变量和方法变成protected，protected不变，private还是不能访问
 - private 继承：public和protected都变成private，private还是不能访问
```C++
// private 继承演示
#include <iostream>
#include <cstring>

using namespace std;

class CAllComers  // 海选者报名类
{
public:
 std::string m_name;
 std::string m_tel;

protected:
 int m_a;

private:
 int m_b;

public:
 CAllComers() { m_name = "某女"; m_tel = "不详"; }
 void sing() { cout << "我是一只小小鸟。\n"; }
 void setname(const string& name) { m_name = name; }
 void sette(const string& tel) { m_tel = tel; }
};

class CGirl :private CAllComers
{
public:
 int m_bh;  // 编号
 CGirl() { m_bh = 8; }
 void show() {
  setname("西施");  // 父类的非私有成员在在派生类中都可以访问
  cout << "编号：" << m_bh << "，姓名：" << m_name
   << "，联系电话：" << m_tel << m_a << endl; // m_b就不行
 }
};

int main()
{
 CGirl g;
 // g.setname("西施");  // 但是在外部不能访问
 g.show();
}
```

- 由于private和protected继承方式会改变基类成员在派生类中的访问权限，导致继承关系复杂，所以在实际开发中，一般用public
 - 在派生类中，可以通过基类的公有成员函数间接访问基类的私有成员
 - 使用using关键字可以改变基类成员在派生类中的访问权限
 - 注意：using只能改变基类中public和protected成员的访问权限，不能改变private成员的访问权限，因为基类中的private成员在派生类中是不可见的，根本不能使用
```C++
// 例子
class A {
public:
 int m_a = 10;
public:
 int m_b = 20;
 void test () {}
private:
 int m_c = 30;
};

class B :public A
{
public:
 using A::m_b;  // 把m_b的权限修改为公有的
private:
 using A::m_a;  // 把m_a的权限修改为私有的
};

int main()
{
 B b;
 // b.m_a = 11;
 b.m_b = 21;
 // b.m_c = 31;

 b.test();
}
```
 
### 125.继承的对象模型
- 1）创建派生类对象时，先调用基类的构造函数，再调用派生类的构造函数
- 2）销毁派生类对象时，先调用派生类的析构函数，再调用基类的析构函数
- 3）创建派生类对象时只会申请一次内存，派生类对象包含了基类对象的内存空间，this指针是同一个
- 4）创建派生类对象时，先初始化基类对象，再初始化派生类对象
- 5）在VS中，用cl.exe可以查看类的内存模型
- 6）对派生类对象用sizeof得到的是积累所有成员的大小（包括私有成员）+ 派生类对象所有成员的大小
- 7）在C++中，不同继承方式的访问权限只是语法上的处理。
- 8）对派生类对象用memset()会清空基类成员
- 9）用制作可以访问到基类中的私有成员（内存对齐）

```C++
#include <iostream>
using namespace std;

void* operator new(size_t size)  // 重载new运算符
{
 void* ptr = malloc(size);
 cout << "申请到的内存地址是：" << ptr << "，大小是：" << size << endl;
 return ptr;
}

void operator delete(void* ptr)
{
 if (ptr == 0) // 对空指针delete是安全的
 {
  return;
 }

 free(ptr);

 cout << "释放了内存.\n";
}

class A {
public:
 int m_a = 10;
protected:
 int m_b = 20;
private:
 int m_c = 30;

public:
 A() 
 {
  cout << "A中的this指针是：" << this << endl;
  cout << "A中m_a的地址是：" << &m_a << endl;
  cout << "A中m_b的地址是：" << &m_b << endl;
  cout << "A中m_c的地址是：" << &m_c << endl;
 }

 void func() { cout << "m_a=" << m_a << ", m_b=" << m_b << ", m_c=" << m_c << endl; }
};

class B :public A
{
public:
 int m_d = 40;

 B()
 {
  cout << "B中的this指针是：" << this << endl;
  cout << "B中m_a的地址是：" << &m_a << endl;
  cout << "B中m_b的地址是：" << &m_b << endl;
  cout << "B中m_d的地址是：" << &m_d << endl;
 }

 void func1() { cout << "m_d=" << m_d << endl; }
};

int main()
{
 cout << "基类占用内存的大小是： " << sizeof(A) << endl;
 cout << "派生类占用内存的大小是： " << sizeof(B) << endl;

 B* p = new B;
 p->func(); p->func1();

 // memset()函数一般不用于类，可能会造成意外的结果，这是其中的一种情况
 // memset(p, 0, sizeof(B));  // 把派生类全部成员的值清零
 
 // 这种方法属于奇巧淫技，在实际开发中，不建议使用
 *((int*)p + 2) = 31;  // m_c = 31

 p->func(); p->func1();
 delete p;
}
```

### 126.如何构造基类
派生类构造函数的要点如下：
- 1）创建派生类对象时，程序首先调用基类的构造函数，然后再调用派生类 构造函数
- 2）如果没有指定基类构造函数，将使用基类的默认构造函数
- 3）**可以用初始化列表指明要使用的基类构造函数**
- 4）基类构造函数赋值初始化被继承的数据成员；

- 在类的继承中，有一个原则：基类的成员变量由基类的构造函数初始化。派生类新增的成员变量由派生类的构造函数初始化。有两个原因：（见视频）

```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a;
private:
 int m_b;

public:
 A():m_a(0), m_b(0)  // 基类的默认构造函数。
 {
  cout << "调用了基类的默认构造函数A()。\n";
 }

 A(int a, int b) : m_a(a), m_b(b)  // 基类有两个参数的构造函数
 {
  cout << "调用了基类的构造函数A(int a, intb). \n";
 }

 A(const A& a) :m_a(a.m_a + 1), m_b(a.m_b + 1)  // 基类的拷贝构造函数
 {
  cout << "调用了基类的构造函数\n";
 }

 // 显示基类A全部的成员
 void showA() { cout << "m_a=" << m_a << ", m_b" << m_b << endl; }
};

class B :public A
{
public:
 int m_c;
 B() : m_c(0), A()  // 派生类的默认构造函数，指明调用基类的默认构造函数
 {
  cout << "调用了派生类的构造函数B()。\n";
 }

 B(int a, int b, int c) : A(a, b), m_c(c)  // 指明基类的有两个参数的构造函数
 {
  cout << "调用了派生类的构造函数B(int a, int b, int c)。\n";
 }

 B(const A& a, int c) : A(a) , m_c(c) // 指明用基类的拷贝构造函数
 {
  cout << "调用了派生类的构造函数B(const A& a, int c)。\n";
 }

 // 显示派生类B全部的成员
 void showB(){ cout << "m_c =" << m_c << endl << endl; }
};

int main()
{
 B b1;  // 将调用基类默认的构造函数
 b1.showA();
 b1.showB();

 B b2(1, 2, 3);
 b2.showA();
 b2.showB();

 A a(3, 4);

 B b3(a, 3);
 b3.showA();
 b3.showB();

 return 0;
}
```
### 127.名字遮蔽与类作用域
- 如果派生类中的成员（包括成员变量和成员函数）和基类中的成员重名，通过派生类对象或者在派生类的成员函数中使用该成员时，将使用派生类新增的成员，而不是基类的。
- 注意：基类的成员函数和派生类的成员函数不会构成重载，如果派生类有同名函数，那么就会**遮蔽**基类中的所有同名函数。
```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a = 30;
 void func() { cout << "调用了A的func()函数。\n"; }
 void func(int a) { cout << "调用了A的func()函数。\n"; }
};

class B :public A  // 派生类
{
public:
 int m_a = 80;

 void func() { cout << "调用了B的func()函数。\n"; }
};

int main()
{
 B b;
 cout << "m_a = " << b.m_a << endl;
 b.func();
 // b.func(1); 基类的成员函数和派生类的成员函数不会构成重载

 return 0;
}
```
- 类是一种作用域，每个类都有自己的作用域，在这个作用域之内定义成员。
- 在类的作用域之外，普通的成员只能通过对象（可以是对象本身，也可以是对象指针或对象引用）来访问，静态成员可以通过对象访问，也可以通过类访问。
- 在成员名面前加类名和域解析符可以访问对象的成员。
- 如果不存在继承关系，类名和域解析符可以省略不写。
```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a = 30;
 int m_b = 50;

 void func() { cout << "调用了A的func()函数。\n"; }
 void func(int a) { 
  cout << "调用了A的func(int a)函数。\n"; 
  A::m_a = m_a; 
  this->m_a = a;
 }
};

int main()
{
 A a;
 cout << "m_a的值是：" << a.A::m_a << endl;
 a.A::func();
 a.A::func(1);

 return 0;
}
```
- 当存在继承关系时，基类的作用域嵌套派生类的作用域中。如果成员在派生类的作用域已经找到，就不会在基类作用域中继续查找；如果没有找到，则继续在基类作用域中查找。
- 如果在成员的前面加上类名和域解析符，就可以直接使用该作用域的成员
```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a = 10;
 void func() { cout << "调用了A的func()函数。\n"; }
};

class B:public A{  // 子类
public:
 int m_a = 20;
 void func() { cout << "调用了B的func()函数。\n"; }
};

class C:public B {  // 孙类
public:
 int m_a = 30;
 void func() { cout << "调用了C的func()函数。\n"; }
};

int main()
{
 C c;
 cout << "C::m_a的值是：" << c.C::m_a << endl;
 cout << "B::m_a的值是：" << c.B::m_a << endl;
 cout << "A::m_a的值是：" << c.A::m_a << endl;
 cout << "A::m_a的值是：" << c.B::A::m_a << endl;

 c.C::func();
 c.B::func();
 c.A::func();
 c.B::A::func();
 return 0;
}
```

### 128.继承的特殊关系
派生类和基类之间一些特殊关系：
- 1）如果继承方式是公有的，派生类对象可以使用基类成员
- 2）可以把派生类对象赋值给基类对象（包括私有成员），但是，会舍弃非基类的成员（多态）
```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a = 0;
private:
 int m_b = 0;
public:
 void show() { cout << "A::show(), m_a=" << m_a << ", m_b =" << m_b << endl; }
 void setb(int b) { m_b = b; }
};

class B:public A{  // 子类
public:
 int m_c = 0;
 // 显示派生类B全部的成员

 void show() { cout << "B::show(), m_a = " << m_a << ", m_c = " << m_c << endl; }
};

int main()
{
 A a;
 B b;

 b.m_a = 10;
 b.setb(20);
 b.m_c = 30;

 a.show();
 a = b; // B私有成员也会复制到A中；(回忆：类的赋值调用的是赋值函数，如果类中没有重载赋值函数，编译器就会提供一个默认赋值函数）
 b.show();

 return 0;
}
``` 
- 3）基类指针可以在不进行显式转换情况下指向派生类对象
- 4）基类引用可以在不进行显示转换的情况下引用派生类对象（回忆：引用的本质是指针常量的伪装，也是指针）
```C++
#include <iostream>
using namespace std;

class A {  // 基类
public:
 int m_a = 0;
private:
 int m_b = 0;
public:
 void show() { cout << "A::show(), m_a=" << m_a << ", m_b =" << m_b << endl; }
 void setb(int b) { m_b = b; }
};

class B:public A{  // 子类
public:
 int m_c = 0;
 // 显示派生类B全部的成员

 void show() { cout << "B::show(), m_a = " << m_a << ", m_c = " << m_c << endl; }
};

int main()
{
 // C++ 中数据类型觉得了操纵方法
 B b;
 A* a = &b;

 b.m_a = 10;
 b.setb(20); // 设置成员m_b的值
 b.m_c = 30;
 b.show();

 a->m_a = 101;
 a->setb(201);
 a->show();

 return 0;
}
```

注意：
- 1）基类指针或引用只能调用基类的方法，不能调用派生类的方法。
- 2）可以用派生类构造基类
- 3）如果函数的形参是基类，实参可以用派生类。（回忆：在程序中，用派生类对象赋值给基类对象，本质上是调用基类的赋值函数）
- 4）C++要求指针和引用类型与赋给的类型匹配，这一规则对继承来说是例外。但是，这种例外只是单向的，不可以将基类对象和指针赋给派生类引用和指针（没有价值，没有讨论的必要）

### 129.多继承与虚继承
- 在实际开发中很少用多继承，而虚继承又是为了解决多继承的问题才出现的，应用场景更少
```C++
#include <iostream>
using namespace std;
class A1 // 基类1
{
public:
 int m_a = 10;
};

class A2
{
public:
 int m_a = 20;
};

class B :public A1, public A2
{
public:
 int m_a = 30;
};

int main()
{
 B b;
 // 如果基类和派生类重名，在成员面前加类名和域名解析符就行了
 cout << "m_a的值是： " << b.m_a << "，m_b的值是：" << b.A1::m_a << "，m_c的值是：" << b.A2::m_a;
}
```
#### 菱形继承
```C++
#include <iostream>
using namespace std;
class A
{
public:
 int m_a = 10;
};

class B:public A {};

class C :public A {};

class D :public B, public C{};

int main()
{
 D d;
 
 d.B::m_a = 30;
 d.C::m_a = 80;

 cout << "B::m_a的地址是： " << &d.B::m_a << "，值是：" << d.B::m_a << endl;
 cout << "B::m_a的地址是： " << &d.C::m_a << "，值是：" << d.C::m_a << endl;
}
```
- 菱形继承存在两个问题：数据冗余和名称的二义性

```C++
// 加上virtual 关键字，数据冗余和名称的二义性的问题都解决了
#include <iostream>
using namespace std;
class A
{
public:
	int m_a = 10;
};

class B :virtual public A {};

class C :virtual public A {};

class D :public B, public C {};

int main()
{
	D d;

	/*d.B::m_a = 30;
	d.C::m_a = 80;*/

	d.m_a = 80;
	cout << "B::m_a的地址是： " << &d.B::m_a << "，值是：" << d.B::m_a << endl;
	cout << "B::m_a的地址是： " << &d.C::m_a << "，值是：" << d.C::m_a << endl;
}
```
- 有了多继承，就有了菱形继承，有了菱形继承就有虚继承，增加了复杂性
- 不提倡使用多继承，只有在比较简单和不出现二义性的情况时才使用多继承，能用单一继承解决的问题就不要使用多继承。
- 正是因为多继承复杂，程序编写、调试和维护都变得困难，后来一些面向对象的语言都不支持多继承

### 131.多态的基本概念
- 基类指针表现出了多种形式，这种现象称为多态
```C++
#include <iostream>
using namespace std;

class CAllComers
{
public:
	int m_bh = 0;
	virtual void show() { cout << "CAllComers::show(): 我是" << m_bh << "号。 " << endl; }
};

class CGirl :public CAllComers
{
public:
	int m_age = 0;  
	void show() { cout << "CGirl::show(): 我是" << m_bh << "号，" << m_age << "岁。" << endl; }
};
int main()
{
	CAllComers a; a.m_bh = 3;
	CGirl g; g.m_bh = 8; g.m_age = 23;

	CAllComers* p;
	p = &a; p->show();
	p = &g; p->show();

	// 基类引用也可以使用多态
	CAllComers& ra = a; ra.show();
	CAllComers& rg = g; rg.show();
	return 0;
}
```
**注意：**
- 1）只需要在基类的函数声明中加上virtual关键字，函数定义时不能加
- 2）在派生类中重定义虚函数时，函数特征要相同
- 3）当在基类中定义了虚函数时，如果在派生类没有重定义该函数，那么将使用基类的虚函数。
- 4）在派生类中重定义了虚函数的情况下，如果想使用基类的虚函数，可以加类名和域解析符
- 5）如果要在派生类中重新定义基类的函数，则将它设置为虚函数；否则，否则不要设置为虚函数，有两种好处：
	- 首先：普通函数效率更高，后面再介绍
	- 其次：如果没有声明为虚函数，是想告诉别人不要重新定义该函数

### 132.多态的应用场景
- 基类实现基本功能。派生类重定义虚函数，扩展功能、提升性能或者实现个性化功能。
- 多态用C语言实现该怎么实现

### 133.多态的对象模型
- 如果类中没有虚函数，编译的时候。编译器直接把成员函数的地址链接到二进制文件中。如果类中有虚函数，编译器不会把虚函数的地址链接到二进制文件中。在有虚函数的类中，多了一个隐身的成员：虚函数指针。
	- 程序在运行中，如果创建了对象。除了给对象的成员分配内存，还会创建一个虚函数表，用虚函数指针指向虚函数表。
	- 在程序中，如果调用的是普通成员函数，程序的二进制代码中有普通函数的地址，直接执行函数就行了。如果调用的是虚函数，要先查找虚函数表，得到函数的地址，再执行函数。
	- 调用普通成员函数的效率比调用虚函数的效率更高，所以说，如果不考虑多态，不要把普通成员函数设置为虚函数
	- 如果基类中有虚函数，派生类会从基类中多继承一个虚函数指针和虚函数表。C++这样设计的目的，是为了让基类和派生类保持相同的内存模型。
	- 在程序运行过程中，如果创建了派生类对象。在虚函数表中，会用派生类成员函数的地址取代基类成员函数的地址
	- 在程序中，如果用基类指针指向派生类对象，用基类指针调用虚函数的时候，还是按照基类的方法调用函数

### 134.如何析构派生类
- C++规定：派生类的析构函数在执行完后，会自动执行基类的析构函数

```C++
#include <iostream>
using namespace std;

class AA
{
public:
	AA() { cout << "调用了基类的构造函数AA()。\n"; }
	virtual void func() { cout << "调用了基类的func()。\n"; }
	virtual ~AA() { cout << "调用了基类的析构函数~AA()。\n"; }
};

class BB:public AA
{
public:
	BB() { cout << "调用了派生类的构造函数BB()。\n"; }
	virtual void func() { cout << "调用了派生类的func()。\n"; }
	~BB() { cout << "调用了派生类的析构函数~BB()。\n"; }
};

int main()
{
	//BB* b = new BB;
	//// 每次主动调用派生类的析构函数，都会调用一次基类的析构函数
	//b->~BB();  // 因为C++规定：派生类的析构函数在执行完后，会自动执行基类的析构函数
	//delete b;

	AA* a = new BB;  // 把基类析构函数设置为虚函数，子类就会自动调用了，这很多态
	delete a;
}
```
- 之前提到过，如果要在在派生类中重定义基类的虚函数，它们的函数特征必须相同，但是基类和派生类的析构函数不可能相同，其实C++编译器会对它们做特殊的处理
- 不能继承的函数：
	- 构造函数
	- 析构函数
	- 赋值运算符函数
	- 友元函数 
- 2）析构函数可以手工调用，如果对象中有堆内存，析构函数以下代码是必要的：
```C++
delete ptr;
ptr = nullptr;

// 只进行delete而不把指针置为空，这想法不对，因为析构函数可能被主动调用。因为可能会导致野指针崩溃。
// 所以在析构函数中，释放堆区内存之后，也应该把指针指向空
```
- 5）对于基类，即使它不需要析构函数，也应该提供一个空虚析构函数。目的是为了让派生类有机会重写析构函数

### 135.纯虚函数和抽象类
- 纯虚函数是一种特殊的虚函数，在某些情况下，基类中不能对虚函数给出有意义的实现，把它声明为纯虚函数。
- 基类中的虚函数一般用于实现缺省的、通用的功能。如果不需要缺省和通用的功能，就把基类的虚函数设置为纯虚函数。 
- 纯虚函数只有函数名、参数和返回值类型，没有函数体，具体的实现留给派生类去做。
- 语法：``` virtual 返回值类型 函数名 (参数列表)=0; ```
- 纯虚函数在基类中为派生类保留一个函数的名字，以便派生类对它进行重定义。如果在基类中没有保留函数名字，则无法支持多态性。
- 含有纯虚函数的类被称为抽象类，不能实例化对象，可以创建指针和引用。

```C++
#include <iostream>
using namespace std;

class AA
{
public:
	AA() { cout << "调用了基类的构造函数AA()。\n"; }
	virtual void func() = 0;  // { cout << "调用了基类的func()。\n"; }
	virtual ~AA() = 0 { cout << "调用了基类的析构函数~AA()。\n"; }
};

class BB :public AA
{
public:
	BB() { cout << "调用了派生类的构造函数BB()。\n"; }
	virtual void func() { cout << "调用了派生类的func()。\n"; }
	~BB() { cout << "调用了派生类的析构函数~BB()。\n"; }
};

int main()
{
	BB b;  // 派生类只有在实现了基类纯虚函数的情况下才能实例化

	AA* p = &b;
	p->func();

	AA& r = b;
	r.func();
}
```
- 派生类必须重定义抽象类中的纯虚函数，否则也属于抽象类
- 基类中的纯虚析构函数也需要实现
```
小技巧：
有时候，想使一个类成为抽象类，但刚好没有任何纯虚函数，怎么办？方法很简单，在想要成为抽象的类中声明一个纯虚析构函数。
```

### 136.运行阶段类型识别 dynamic_cast
- **运行阶段类型识别（RTTI, RunTime Type Identification）** 为程序在运行阶段确定对象的类型，只适用于包函数虚函数的类。
- 基类指针可以指向派生类对象，如何知道基类指针指向的是那种派生类对象呢？（想调用派生类中的非虚函数）
- dynamic_cast运算符是用基类的指针来生成派生类的指针，它不能回答“指针指向的是什么类的对象”，但能回答“是否可以安全的将对象的地址赋给特定类型的指针”的问题。
- 如果转换成功，*dyanmic_cast* 返回对象的地址，如果失败，返回 *nullptr*

```C++
// 基类：Hero
// 派生类：XS、HX、LB

XS* pxs = (XS*)ptr;  // C风格强制转换的方法，程序员必须保证目标类型正确。如果基类指针指向的是HX，那么这行代码就会出问题。

XS* pxs = dynamic_cast<XS*>(ptr);  // C++的方式
```
- 注意：
	- 1）*dyanmic_cast* 只适用于包含虚函数的类。（**为多态而生**）
	- 2）*dyanmic_cast* 可以将派生类指针转换为基类指针，这种画蛇添足的做法没有意义。
	- 3）*dyanmic_cast* 可以用于引用，但是，没有与空指针对应的引用值，如果转换请求不正确，会出现*bad_cast*异常

### 137.typeid运算符和type_info类
- *typeid*运算符用于获取数据类型的信息：
	- 语法一：*typeid*(数据类型)
	- 语法二：*typeid*(变量名和表达式);
- *typeid*运算符返回*type_info*类（在头文件中<typeinfo>中定义）的对象的引用。
- *type_info*类的实现随编译器而异，但至少有*name()*成员函数，该函数返回一个字符串，通常是类名。
- *type_info*重载了 == 和 != 运算符，用于对类型进行比较
	
```C++
#include <iostream>
// #include <string>
using namespace std;

class AA
{
public:
	AA() {}
};

int main()
{
	// typeid用于C++内置数据类型
	int ii = 3;
	int* pii = &ii;
	int& rii = ii;

	cout << "type(int) = " << typeid(int).name() << endl; // int
	cout << "type(ii) = " << typeid(ii).name() << endl;  // int

	cout << "type(int*) = " << typeid(int*).name() << endl; // int * __ptr64
	cout << "type(pii) = " << typeid(pii).name() << endl;  // int * __ptr64

	cout << "type(int&) = " << typeid(int&).name() << endl; // int
	cout << "type(rii) = " << typeid(rii).name() << endl << endl;  // int

	// typeid用于自定义的数据类型
	AA aa;
	AA* paa = &aa;
	AA& raa = aa;

	cout << "typeid(AA) = " << typeid(AA).name() << endl;  // class AA
	cout << "typeid(aa) = " << typeid(aa).name() << endl;  // class AA

	cout << "typeid(AA* ) = " << typeid(AA*).name() << endl;  // class AA * __ptr64
	cout << "typeid(paa) = " << typeid(paa).name() << endl;  // class AA * __ptr64

	cout << "typeid(AA&) = " << typeid(AA&).name() << endl;  // class AA
	cout << "typeid(raa) = " << typeid(raa).name() << endl << endl;  // class AA

	// type_info 重载了 == 和 != 运算符，用于对类型进行比较
	if (typeid(AA) == typeid(aa))
	{
		cout << "It's OK_aa !" << endl;
	}

	if (typeid(AA) == typeid(*paa))
	{
		cout << "It's OK_*paa !" << endl;
	}

	if (typeid(AA) == typeid(raa))
	{
		cout << "It's OK_raa !" << endl;
	}

	if (typeid(AA*) == typeid(paa))
	{
		cout << "It's OK_paa !" << endl;
	}
}
```
- 注意：
	- 1）*type_info*的构造函数是*private*属性，也没有拷贝构造函数，所以不能直接实例化，只能由编译器在内部实例化。
	- 2）不建议用*name()*成员函数返回的字符串作为判断数据类型的依据。（编译器可能会转换类型名）
	- 3）*typeid()*运算符可以用于多态的场景，在运行阶段识别对象的数据类型。
	- 4）假设有表达式*typeid(\*ptr)*，当*ptr*是空指针时，且ptr是多态的类型，将引发*bad_typede*异常
```C++
if (typeid(XS) == typeid(*ptr))
{
	XS* pxs == (XS*) ptr;
	pxs->show();
}
```
	
	
