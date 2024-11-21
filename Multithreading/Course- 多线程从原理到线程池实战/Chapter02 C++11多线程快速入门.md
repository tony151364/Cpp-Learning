
# 第2章 C++11多线程快速入门 


## 2.1 为什么要使用多线程
多线程实际上加大了调试的成本，但是还要使用的原因如下：

- 任务分解:  
```耗时的操作，任务分解，实时响应```

- 数据分解:  
```充分利用多核 CPU 处理数据```

- 数据流分解:  
```读写分离，解耦合设计```

```C++
// 01 FirstThread.cpp
#include <thread> // C++11引入
#include <iostream>

using namespace std;

// 主线程函数
void ThreadMain()
{
	cout << "Begin sub thread main " << this_thread::get_id() << endl;
	
	for (int i = 0; i < 10; i++)
	{
		cout << "In thread" << i << endl;

		// 使用了C++14的字面量，也可以用C++11中的 this_thread::sleep_for(chrono::seconds(1));
		this_thread::sleep_for(1s);  // 1000ms
	}

	cout << "End sub thread main " << this_thread::get_id() << endl;
}

// 主线程入口。一个进程可以包含多个子线程。
int main(int argc, char* argv[])
{
	cout << "Main Thread ID " << this_thread::get_id() << endl;

	thread th(ThreadMain);  // 子线程创建启动

	cout << "Begin wait sub thread " << endl;  // 与上面一行可能同时执行
	th.join();  // 阻塞，等待子线程退出
	cout << "End wait sub thread " << endl;

	return 0;
}
```
- 调试的的时候可以通过线程ID`this_thread::get_id()`来判断哪里出错了


## 2.2 std::thread 对象生命周期和线程等待和分离
- `thread`的对象的生命周期怎么办？
- `thread`与`th.join()`是什么关系？
- 希望在`thread`销毁之后做一些事情该怎么办？这个时候就需要分离。

### C++11 std::thread 源码
```C++
// C++11 std::thread源码
class thread
{
	//.....
	thread() noexcept : _Thr{} {}

	explicit thread(_Fn&& _Fx, _Args&&... _Ax) {
		//.....
		_Thr._Hnd =
		reinterpret_cast<void*>(_CSTD _beginthreadex(nullptr, 0, _Invoker_proc,
		_Decay_copied.get(), 0, &_Thr._Id));
	}

	~thread() noexcept {
		if (joinable()) {
			_STD terminate();
		}
	}
};
```

- 测试代码
```C++
#include <thread>
#include <iostream>

using namespace std;

bool is_exist = false;

void ThreadMain()
{
	cout << "Begin sub thread main " << this_thread::get_id() << endl;

	for (int i = 0; i < 10; i++)
	{
		if (!is_exist)
		{
			break;
		}
		cout << "In thread" << i << endl;

		this_thread::sleep_for(100ms);
	}

	cout << "End sub thread main " << this_thread::get_id() << endl;
}

int main(int argc, char* argv[])
{
	//出错 线程句柄清理，线程还在运行
	{
		// 一旦传入一个参数就会创建一个线程，返回一个Windows句柄
		// thread th(ThreadMain);  // 出错，thread对象被销毁，子线程还在运行
	}
	
	// 分离
	{
		thread th(ThreadMain);
		th.detach();  // 子线程与主线程分离，守护线程
		// 问题：主线程退出后，子线程不一定退出。主线程已经释放，导致子线程无法访问主线程的一些变量或函数，从而引发程序崩溃。
	}

	// 阻塞等待
	{
		thread th(ThreadMain);
		
		this_thread::sleep_for(chrono::seconds(5));
		is_exist = true;

		cout << "Begin wait sub thread " << endl;
		th.join(); // 问题：主线程阻塞，等到子线程退出
		cout << "End wait sub thread " << endl;
	}

	return 0;
}
```

## 2.3 C++11 线程创建的多种方式和参数传递
### 2.3.1 普通全局函数作为线程入口
#### 如何传递参数
- C++11内部如何实现参数传递
- C++11 thread源码分析
```C++
class thread 
{ // class for observing and managing threads
public:
	//....
	template <class _Fn, class... _Args, enable_if_t<!is_same_v<_Remove_cvref_t<_Fn>, thread>, int> = 0>
	explicit thread(_Fn&& _Fx, _Args&&... _Ax) 
	{
		using _Tuple = tuple<decay_t<_Fn>, decay_t<_Args>...>;
		auto _Decay_copied = _STD make_unique<_Tuple>(_STD forward<_Fn>(_Fx),
			_STD forward<_Args>(_Ax)...);
		constexpr auto _Invoker_proc = _Get_invoke<_Tuple>(make_index_sequence<1 +
			sizeof...(_Args)>{});
		_Thr._Hnd = reinterpret_cast<void*>(_CSTD _beginthreadex(nullptr, 0, _Invoker_proc,
				_Decay_copied.get(), 0, &_Thr._Id));
	}
};
```

#### 传递参数示例代码
```C++
#include <thread>
#include <iostream>
#include <string.h>

using namespace std;

class Para
{
public:
	Para() { cout << "Construct Para" << endl; }
	Para(const Para& p) 
	{ 
		this->name = p.name;
		cout << "Copy Construction Para" << endl;
	}

	virtual ~Para() { cout << "Deconstruct Para" << endl; }

	string name;
};

void ThreadMain(int p1, float p2, string str, Para p4)
{
	this_thread::sleep_for(100ms);
	cout << "ThreadMain " << p1 << " " << p2 << " " 
		 << str << p4.name << endl;
}

int main(int argc, char* argv[])
{
	thread th;

	{
		float f = 12.1f;
		Para p;
		p.name = "Test Para Class";
		// 所有的参数都复制了一份到线程函数
		th = thread(ThreadMain, 101, f, "Test String", p);
	}
	
	th.join();

	return 0;
}
```

#### 线程函数传递指针和引用变量
```C++
#include <thread>
#include <iostream>
#include <string.h>

using namespace std;

class Para
{
public:
	Para() { cout << "Construct Para" << endl; }
	Para(const Para& p) 
	{ 
		this->name = p.name;
		cout << "Copy Construction Para" << endl;
	}

	virtual ~Para() { cout << "Deconstruct Para" << endl; }

	string name;
};

void ThreadMainRef(const Para& p)
{
	cout << " ThreadMainPtr name = " << p.name << endl;
}

void ThreadMainPtr(const Para* p)
{
	if (p)
	{
		cout << " ThreadMainPtr name = " << p->name << endl;
	}
}

int main(int argc, char* argv[])
{
	{   // 传递引用
		Para p;
		p.name = "Test Ref";

		thread th;
		th = thread(ThreadMainRef, std::ref(p)); // std::ref()指定引用类型
		th.join();
	}

	{   // 传递指针（正常版本）
		Para p;
		p.name = "Test Ptr";
		
		thread th;
		th = thread(ThreadMainPtr, &p);
		th.join();
	}

	{   // 传递指针（错误版本，没有打印name的值。因为主线程已经销毁，指针所指向的东西也没了。 ）
		
		Para p;
		p.name = "Test Ptr";

		thread th;
		th = thread(ThreadMainPtr, &p);
		th.detach();

		// 避免方式：传递堆中的参数，或者静态的参数，防止代码段结束后释放。
	}
	
	return 0;
}
```

#### 参数传递的一些坑
- 传递空间已经销毁（比如主线程已经提前释放）
- 多线程共享访问一块空间
- 传递的指针变量的生命周期小于线程

```C++

#include <iostream>
#include <thread>

using namespace std;

struct TPara
{
	string name;
};

//线程入口函数
void ThreadMain(TPara* str)
{
	this_thread::sleep_for(1000ms);
	//str已经销毁
	cout << " 进入子线程" << this_thread::get_id() << str->name << endl;
}

//主线程入口
int main(int argc, char* argv[])
{
	//1 创建并启动子线程，进入线程函数
	thread th;
	{
		TPara a;
		a.name = "test string in A";
		th = thread(ThreadMain, &a);
	}
	cout << "等待子线程退出" << endl;
	th.join();
	cout << "等待子线程返回" << endl;
	return 0;
}
```

### 2.3.2 类成员函数作为线程入口
#### 接口调用和参数传递
```C++

#include <iostream>
#include <thread>

using namespace std;
class MyThread
{
public:
	void Main()
	{
		cout << "MyThread Main " << name << " : " << age << endl;
	}
	string name;
	int age = 100;
};

int main()
{
	MyThread myth;
	myth.name = "Test name 001";
	myth.age = 20;
	thread th(&MyThread::Main, &myth); // 需要加&是因为成员函数和普通函数在底层机制上不同
	th.join();

	return 0;
}
```

#### 线程基类封装
- 当 join() 被调用时，主线程将会阻塞，直到子线程完成其执行后再继续运行。
```C++

#include <thread>
#include <iostream>
#include <string>

using namespace std;

class XThread
{
public:
	virtual void Start()
	{
		is_exit_ = false;
		th_ = std::thread(&XThread::Main, this);
	}

	virtual void Stop()
	{
		is_exit_ = true;
		Wait();
	}

	virtual void Wait()
	{
		if (th_.joinable())  // 检查当前线程是否可join
			th_.join();  // 等待当前线程执行完毕
	}

	bool is_exit() { return is_exit_; }
private:
	virtual void Main() = 0;
	std::thread th_;
	bool is_exit_ = false;
};

class TestXThread :public XThread
{
public:
	void Main() override
	{
		cout << "TestXThread Main begin" << endl;
		while (!is_exit())
		{
			this_thread::sleep_for(100ms);
			cout << "." << flush;
		}
		cout << "TestXThread Main end" << endl;
	}

	string name;
};

int main(int argc, char* argv[])
{
	TestXThread testth;
	testth.name = "TestXThread name ";
	testth.Start();
	this_thread::sleep_for(3s);
	testth.Stop();

	return 0;
}
```

### 2.3.3 Lambda 临时函数作为线程入口函数
```
lambda 函数，其基本格式如下：
[捕捉列表] (参数) mutable -> 返回值类型 {函数体}
```
- 如果捕获列表中的参数是通过引用捕获的（&），加不加 mutable 都无所谓
- 如果 Lambda 的函数体中只有一个简单的 return 语句，返回类型可以由编译器自动推导出来。
- 如果 Lambda 表达式的函数体比较复杂，或者需要返回不同类型的值，可能需要显式指定返回类型、

示例代码内容：
- lambda 线程传递参数
- 线程入口为类成员 lambda 函数
```C++
#include <thread>
#include <iostream>

using namespace std;

class TestLambda
{
public:
	void Start()
	{
		// 捕获参数
		thread th([this] { cout << "name = " << this->name << endl; });
		th.join();
	}

private:
	std::string name = "Test Lambda";
};

int main()
{
	// 普通Lambda函数调用
	thread th([] (int i) { cout << "This a Lambda function!\n"; }, 123);
	th.join();

	// 类成员函数中调用Lambda
	TestLambda TL;
	TL.Start();

	return 0;
}
```


### 2.3.4 call_once 多线程调用函数，但函数只进入一次
- 某些功能可能会用到。用互斥变量会比较复杂，代码逻辑有些乱。
```C++
#include <iostream>
#include <mutex>
#include <thread>

using namespace std;

void SystemInit()
{
	cout << "Call SystemInit" << endl;
}

void SystemInitOne()
{
	static std::once_flag flag;
	std::call_once(flag, SystemInit);
}

int main()
{

	for (int i = 0; i < 3; i++)
	{
		// SystemInit();
		thread th(SystemInitOne);   // C++11, 只调用一次
		th.join();
	}

	return 0;
}
```
