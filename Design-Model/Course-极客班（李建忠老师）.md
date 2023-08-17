- [ ] 后面把各个模式的类图总结一下，画一画。
- [ ] 函数对象是什么

## 一、 设计模式简介
- 变化！！！
- 复用！！！

### 从封装变化角度对模式分类
- 组件协作：现代软件专业分工之后的第一个结果是“框架与应用程序的划分”，“组件协作”模式通过晚期绑定，来实现框架与应用程序间的松耦合，是二者之间协作时常用的模式。
	- Template Method
	- Strategy
	- Observer / Event
- 单一职责：在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随需求的变化，子类急剧膨胀，同时充斥着重复的代码，这时候的关键是划清责任。
	- Decorator
	- Bridge
- 对象创建
	- Factory Method
	- Abstract Factory
	- Prototype
	- Builder（不常用）
- 对象性能
	- Singleton
	- Flyweight
- 接口隔离：
	- Facade
	- Proxy
	- Mediator（不常用）
	- Adapter
- 状态变化：
	- Memento（不常用）
	- State
- 数据结构
	- Composite
	- Iterator（不常用，被STL淘汰）
	- Chain of（不常用，被函数对象淘汰）
	- Responsibility（不常用）
- 行为变化
	- Command（不常用）
	- Visitor（不常用）
领域问题：
	- Interpreter（不常用）

### 重构获得模式 Refactoring to Patterns
- 面向对象设计模式是“好的面向对象设计”，所谓“好的面向对象设计”指的是那些可以满足“应对变化，提高复用”的设计。
- 现代软件设计的特征是“需求的频繁变化”。设计模式的要点是“寻找变化点，然后在变化点处应用设计模式，从而来更好地应对需求的变化”。“什么时候，什么地点应用设计模式”比“理解设计模式结构本身”更为重要。
- 设计模式的应用不宜先入为主，一上来就使用设计模式是对设计模式的最大误用（自己：就是说，从重构中去使用模式。刚开始代码很乱，之后想要重构，重构时用了设计模式，之后代码就变得很合理）没有一步到位的设计模式。敏捷软件开发实践提倡的“Refactoring to Patterns”是目前普遍公认的最好的使用设计模式的方法。

### 重构关键技法
- 静态 -> 动态
- 早绑定 -> 晚绑定
- 继承 -> 组合
- 编译时依赖 -> 运行时依赖
- 紧耦合 -> 松耦合

老师：学完之后再回过头来看，这5个技法讲的是一个事情，换句话说其实是从不同角度来看待同一个事情


## 二、面向对象设计原则
- 变化是复用的天敌
- 面向对象设计最大的优势在于**抵御变化**
- 面向接口设计，是产业强盛的标志。接口标准化！
- **设计原则比模式更重要**

### 重新认识面向对象
- 理解隔离变化
	- 从宏观层面来看，面向对象的构建方式能适应软件的变化，能将变化所带来的影响减为最小
- 各司其职
	- 从微观层面来看，面向对象的方式更强调个各类的“责任”
	- 由于需求变化导致的新增类型不应该影响原来类型的实现——是所谓各司其职
- 对象时什么
	- 从语言层面来看，对象封装了代码和数据。
	- 从规格层面讲，对象是一系列可被使用的公共接口
	- 从概念层面讲，对于是某种拥有责任的对象

### 面向对象原则1：依赖倒置原则（DIP）
- 高层模块（稳定）不应该依赖于底层（变化），二者都应该依赖抽象
- 抽象（稳定）不应该依赖于实现细节（变化），实现细节应该依赖于抽象（稳定）
	
### 面向对象原则2：开放封闭原则（OCP）
- 对扩展开放，对更改封闭
- 类模块应该是可扩展的，但是不可修改
	
### 面向对象原则3：单依职责原则（SRP）
- 一个类应该仅有一个引起它变化的原因
- 变化的方向隐含着类的责任

- 有时候，一个类搞太多方法，那么这个类的责任就会很多，容易出现混乱

### 面向对象原则4：Liskov替换原则（LSP）
- 子类必须能够替换它们的基类（IS-A）
- 继承表达类型抽象

### 面向对象原则5： 接口隔离原则（ISP）
- 不应该强迫客户程序依赖它们不用的方法
- 接口应该小而完备
	
- public太多，外部就会产生依赖，维护起来就很麻烦。合适地使用public、protected、private

### 面向对象原则6：优先使用对象组合，而不是继承
- 类继承通常为“白箱复用”，对象组和通常为“黑箱复用”
- 继承在某种程度上破坏了封装性，子类父类耦合度高
- 而对象组合则只要求被组合的对象具有良好定义的接口，耦合度低。:

### 面向对象原则7：封装变化点
- 使用封装来创建对象之间的分界层，让设计者可以在分界层的一侧进行修改，而不会对另一侧产生不良的影响，从而实现层次间的松耦合。

### 面向对象原则8：针对接口编程，而不是针对实现编程
- 不将变量类型声明为某个特定的具体类，而是声明为某个接口。
- 客户程序无需获知对象的具体类型，只需要知道对象所具有的接口
- 减少系统中各个部分的依赖关系，从而实现“高内聚，松耦合”的类型设计方案
- 补充：该原则与依赖倒置原则相辅相成，违背一个基本俩都违背了


## 三、模板方法（template method）（“组件协作”模式）
### “组件协作”模式：
- 现代软件专业分工之后的第一个结果是“框架与应用程序的划分”，“组件协作”模式通过晚期绑定，来实现框架与应用程序间的松耦合，是二者之间协作时常用的模式。
- 典型模式
	- Template Method
	- Strategy
	- Observer / Event
 ### 动机
 - 在软件构建过程中，对于某一项任务，它常常有稳定的整体操作结构，但各个子步骤却又很多变化的需求，或者由于固有的原因（比如框架与应用之间的关系）而无法和任务的整体结构同时实现。
 - 如果在确定稳定操作结构的前提下，来灵活应对各个子步骤的变化或者晚期实现需求？

```C++
//程序库开发人员
class Library
{
public:
	void Step1()
	{
		//...
	}

	void Step3()
	{
		//...
	}

	void Step5()
	{
		//...
	}
};

//应用程序开发人员
class Application
{
public:
	bool Step2()
	{
		//...
	}

	void Step4()
	{
		//...
	}
};

int main()
{
	Library lib();
	Application app();

	lib.Step1();

	if (app.Step2())
	{
		lib.Step3();
	}

	for (int i = 0; i < 4; i++)
	{
		app.Step4();
	}

	lib.Step5();
}
```
```C++
//程序库开发人员
class Library {

public:
	//稳定 template method
	void Run()
	{

		Step1();

		if (Step2())  //支持变化 ==> 虚函数的多态调用
		{
			Step3();
		}

		for (int i = 0; i < 4; i++)
		{
			Step4(); //支持变化 ==> 虚函数的多态调用
		}

		Step5();

	}
	virtual ~Library() { }

protected:
	void Step1()  //稳定
	{
		//.....
	}
	void Step3()  //稳定 
	{
		//.....
	}
	void Step5()  //稳定
	{
		//.....
	}

	virtual bool Step2() = 0;  //变化
	virtual void Step4() = 0;  //变化
};

//应用程序开发人员
class Application : public Library
{
protected:
	virtual bool Step2()
	{
		//... 子类重写实现
	}

	virtual void Step4()
	{
		//... 子类重写实现
	}
};

// 应用开发人员不需要再写程序的主流程
int main()
{
	Library* pLib = new Application();
	pLib->Run();  // 弹幕：QT里面的Run方法也是如此

	delete pLib;
}
```
-  第一种写法是早绑定：Application -> Library （C语言，面向过程设计）
	- 一个晚的东西去调用早的东西是早绑定
-  第二种写法是晚绑定：Library -> Application （C++，面向对象设计）
	- 容易出现只见树木不见森林。要试图去了解程序的主流程  

### 模式定义
- 定义一个操作中的算法的骨架（稳定），而将一些步骤延迟（变化）到子类中。Template Method使得子类可以不改变（复用）一个算法的结构即可重定义（override 重写）该算法的某些特定步骤。  ———— 《设计模式》GoF
- 老师：template method的前提是Run是稳定的，也就是你有一个算法的骨架可以被重用。
- 老师：设计模式的一个假设是存在一个稳定点，如果没有一个稳定点，那么设计模式没有任何作用。同样的，如果所有东西都是稳定的，设计也没有任何意义
- 老师：设计模式的最大作用是在变化与稳定之间寻找隔离点，然后来分离它们，从而来管理变化。把兔子关进笼子里。
### 要点总结
- Template Method模式是一种非常基础性的设计模式，在面向对象系统中有着大量的应用。它用最简洁的机制（虚函数的多态性）为很多应用程序框架提供了灵活的扩展点，是代码复用方面的基本实现结构。
- 除了可以灵活应对子步骤的变化外，“不要调用我，让我来调用你”的反向控制结构是Template Method的典型应用。
- 在具体实现方面，被Template Method调用的虚方法可以具体实现，也可以没有任何实现（抽象方法，纯虚方法），但一般推荐将它们设置为protected方法。
- 老师：扩展 = 继承 + 多态。子类去继承父类然后去override，这个就叫做扩展。
- 老师：虚函数表底层就是函数指针

 
## 四、策略模式（“组件协作”模式）
### 动机
- 在软件构建过程中，某些对象使用的算法可能多种多样，经常改变，如果将这些算法都编码到对象中，将会使对象变得异常复杂；而且有时候支持不是用的算法也是一个性能负担。
- 如何在运行时分局需要透明地更改对象的算法？将算法与对象本身解耦，从而避免上述问题？
```C++
enum TaxBase {
	CN_Tax,
	US_Tax,
	DE_Tax,
	FR_Tax       // 更改
};

class SalesOrder {
	TaxBase tax;
public:
	double CalculateTax()
	{
		// ...

		if (tax == CN_Tax)
		{
			// CN***********
		}
		else if (tax == US_Tax)
		{
			// US***********
		}
		else if (tax == DE_Tax)
		{
			// DE***********
		}
		else if (tax == FR_Tax)  // 更改
		{
			// ...
		}

		// ....
	}
};
```
```C++
// 设计模式说的复用性是编译之后的二进制级别的复用，不是代码层面的复用
// 当你的代码出现多个if else时，这就是策略模式需要的特征
// if else 绝对不变的情况下，即情况不会增加，比如一周七天。这时候不用strategy

class TaxStrategy
{
public:
	virtual double Calculate(const Context& context) = 0;
	virtual ~TaxStrategy() {}
};


class CNTax : public TaxStrategy
{
public:
	virtual double Calculate(const Context& context)
	{
		//***********
	}
};

class USTax : public TaxStrategy
{
public:
	virtual double Calculate(const Context& context)
	{
		//***********
	}
};

class DETax : public TaxStrategy
{
public:
	virtual double Calculate(const Context& context)
	{
		//***********
	}
};

//扩展
//*********************************
class FRTax : public TaxStrategy {

public:
	virtual double Calculate(const Context& context)
	{
		//.........
	}
};


class SalesOrder
{
private:
	TaxStrategy* strategy;

public:
	SalesOrder(StrategyFactory* strategyFactory)  // 用工厂模式去创建
	{
		this->strategy = strategyFactory->NewStrategy();
	}

	~SalesOrder()
	{
		delete this->strategy;
	}

	public double CalculateTax()
	{
		//...
		Context context();
		double val = strategy->Calculate(context); //多态调用
		//...
	}

};
```
### 模式定义
- 定义一些列算法，把它们一个个封装起来，并且使它们可以互相替换（变化）。该模式使得算法可独立于使用它的客户程序（稳定）而变化（扩展，子类化）。   ———— 《设计模式》GoF
### 要点总结
- Strategy及其子类为组件提供了一些列可重用的算法，从而可以使得类型在运行时方便的根据需要在各个算法之间切换。
- Strategy模式提供了用条件判断语句以外的另一种选择，消除条件判断语句，就是在解耦合。含有许多条件判断语句的代码通常都需要strategy模式
- 如果Strategy对象没有实例变量，那么各个上下文可以共享同一个Strategy对象，从而节省对象开销


## 五、观察者模式（Observer）（“组件协作”模式）
- 广播就是观察者模式
### 动机
- 在软件构建过程中，我们需要为某些对象建立一种“通知依赖关系” ———— 一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知。如果这样的依赖关系过于紧密，将使软件不能很好地抵御变化。
- 使用面向对象技术，可以将这种依赖关系弱化，并形成一种稳定的依赖关系。从而实现软件体系结构的松耦合。
```C++
// C++推荐的形式是：一个主继承类，其他的都是接口或者抽象基类
class MainForm: public Form, public IProgress
{
	TextBox* txtFilePath;
	TextBox* txtFileNumber;
	
	ProgressBar* progressBar;
	
public:
	void Button1_Click()
	{
		stirng filePath = txtFilePath->getText();
		int number = atoi(txtFileNumber->getText().c_str());
		
		FileSplitter splitter(filePath, number);
		
		splitter.add_IProgress(this);
		splitter.add_IProgress(cn);
		
		splitter.split();
		
		splitter.remove_IProgress(this);
	}
	
	virtual void DoProgress(float value)
	{
		progressBar->setValue(value);
	}
}
```

```C++
class IProgress
{
public：
	virtual void DoProgress(float value) = 0;
	virtual ~IProgress(){ }
}

class FileSplitter
{
	string m_filePath;
	int m_fileNumber;
	
	// ProgressBar* m_progressBar;  // 具体通知控件
	List<IProgress*> m_iprogressList;  // 抽象通知机制, 支持多个观察者
	
public:
	FileSplitter(const string& filePath, int fileNumber):
		m_filePath(filePath),
		m_fileNumber(fileNumber),
		{
		
		}
		
	void add_IProgress(IProgress* iprogress)
	{
		m_iprogressList.push_back(iprogress);
	}
	
	void remove_IProgress(IProgress* iprogress)
	{
		m_iprogressList.remove(iprogress);
	}
	
	void split()
	{
		// 1.读取大文件
		
		// 2.分批次向小文件中写入
		for (int i = 0; i < m_fileNumber; i++)
		{
			// ...
			
			
			float progressValue = m_fileNumber;
			progressValue = (i + 1) / progressValue;
			onProgress(progressValue) 
		}
	}
protected:
	void onProgress(float value) 
	{
	List<IProgress*>::Iterator itor = m_iprogressList.begin();
	List<IProgress*>::Iterator end = m_iprogressList.end();
	
	while (itor != end)
	{
		(*itor)->DoProgress(progressValue);  // 更新进度条

		
		itor++;
	}
}
```

#### 模式定义
- 定义对象间的一种一对多的（变化）的依赖关系，以便当一个对象（Subject）的状态发生改变时，所有依赖于它的对象得到通知并自动更新。   ———— 《设计模式》GoF

#### 要点总结
- 使用面向对象的抽象，Observer模式使得我们可以独立地改变目标与观察者，从而使二者之间的依赖关系达至松耦合
- 目标发送通知是，无需指定观察者，通知（可以携带通知信息作为参数）会自动传播
- 观察者自己决定是否需要订阅通知，目标对象对此一无所知。
- Observer 模式是基于事件的UI框架中非常常用的设计模式，也是MVC模式的重要组成部分


## 6.装饰模式（"单一职责" 模式）
### "单一职责" 模式
- 在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随需求的变化，子类急剧膨胀，同时充斥着重复的代码，这时候的关键是划清责任。
	- Decorator
	- Bridge
### 动机
- 在某些情况下我们可能会“过度地使用继承来扩展对象的功能”，由于继承为类型引入的静态特质，使得这种扩展方式缺乏灵活性；并且随着子类的增多（扩展功能的增多），各种子类的组合（扩展功能的组合）会导致更多子类的膨胀。
- 如何使“对象功能的扩展”能够根据需要来动态地实现？同时避免“扩展功能的增多”带来的子类膨胀问题？从而使得任何“功能扩展变化”所导致的影响降到最低？
```C++
// 业务操作
class stream
{
public:
	virtual char Read(int number) = 0;
	virtual void Seek(int position) = 0;
	virtual void Write(char data) = 0;
	
	virtual ~Stream() {}
};

// 主体类
class FileStream: public Stream
{
public:
	virtual char Read(int number) {
		// 读文件流
	}
	
	virtual void Seek(int position) {
		// 定位文件流
	}
	
	virtual void Write(char data)
	{
	
	}
}
```
### 模式定义
- 动态（组合）的给一个对象增加一些额外的职责。就增加功能而言，Decorator模式比生成子类（继承）更为灵活（消除重复代码&减少子类个数）。    ———— 《设计模式》GoF

### 要点总结
- 通过采用组合而非继承的手法，Decorator模式实现了在运动时动态扩展对象功能的能力，而且可以根据需要扩展多个功能。避免了使用继承带来的“灵活性差”和“多子类衍生问题”
- Decorator类在接口上表现为is-a Component的继承关系，即Decorator类继承了Component类所具有的接口。但在实现上又表现为has-a Component的组合关系，即Decorator类又使用了另外一个Component类
- Decorator模式的目的并非解决“多子类衍生的多继承”问题，Decorator模式应用的要点在于解决“主体类在多个方向上的扩展功能”——是为“装饰”的含义
- 老师：同时继承又同时组合，基本就是Decorator设计模式了


## 7.桥模式（"单一职责" 模式）

- 由于某些类型的固有的实现逻辑，使得它们具有两个变化的维度，乃至多个维度的变化
- 如何应对这种“多维度的变化

```C++
// 更改前
// 更改前
class Messager {
public:
	virtual void Login(string username, string password) = 0;
	virtual void SendMessage(string message) = 0;
	virtual void SendPicture(Image image) = 0;

	virtual void PlaySound() = 0;
	virtual void DrawShape() = 0;
	virtual void WriteText() = 0;
	virtual void Connect() = 0;


	virtual ~Messager() {}
};


// 平台实现 n
class PCMessagerBase : public Messager
{
public:
	virtual void PlaySound() {
		// ***************
	}

	virtual void DrawShape() {
		// **************
	}

	virtual void WriteText() {
		// **************
	}

	virtual void Connect() {
		// **************
	}
};

class MobileMessagerBase : public Messager
{
public:
	virtual void PlaySound() {
		// ==============
	}

	virtual void DrawShape() {
		// ==============
	}

	virtual void WriteText() {
		// ==============
	}

	virtual void Connect() {
		// ==============
	}
};

// 业务抽象 m
// 类的数量： 1 + n + m*n
class PCMessagerLite : public PCMessagerBase
{
public:
	virtual void Login(string username, string password)
	{
		PCMessagerBase::Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		PCMessagerBase::WriteText();
	}

	virtual void SendPicture(Image image) {
		PCMessagerBase::DrawShape();
		// ..............
	}
};

class PCMessagerPerfect : public PCMessagerBase
{
public:
	virtual void Login(string username, string password) {
		PCMessagerBase::PlaySound();
		// ************
		PCMessagerBase::Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		PCMessagerBase::PlaySound();
		// ************
		PCMessagerBase::WriteText();
		// ..............
	}

	virtual void SendPicture(Image image) {
		PCMessagerBase::PlaySound();
		// ************
		PCMessagerBase::DrawShape();
		// ..............
	}
};

class MobileMessagerLite : public MobileMessagerBase
{
public:
	virtual void Login(string username, string password)
	{
		MobileMessagerBase::Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		MobileMessagerBase::WriteText();
	}

	virtual void SendPicture(Image image) {
		MobileMessagerBase::DrawShape();
		// ..............
	}
};

class MobileMessagerPerfect : public MobileMessagerBase
{
public:
	virtual void Login(string username, string password) {
		MobileMessagerBase::PlaySound();
		// ************
		MobileMessagerBase::Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		MobileMessagerBase::PlaySound();
		// ************
		MobileMessagerBase::WriteText();
		// ..............
	}

	virtual void SendPicture(Image image) {
		MobileMessagerBase::PlaySound();
		// ************
		MobileMessagerBase::DrawShape();
		// ..............
	}
};

void Process()
{
	// 编译时装配
	Messager* m = 
		new MobileMessagerPerfect();
}
```

```C++
// 更改后
class Messager {
protected:
	MessagerImp* messagerImp;
public:
	virtual void Login(string username, string password) = 0;
	virtual void SendMessage(string message) = 0;
	virtual void SendPicture(Image image) = 0;

	virtual ~Messager() {}
};

class MessagerImp
{
public:
	virtual void PlaySound() = 0;
	virtual void DrawShape() = 0;
	virtual void WriteText() = 0;
	virtual void Connect() = 0;

	virtual ~MessagerImp() {}
};

// 平台实现 n
class PCMessagerImp : public MessagerImp
{
public:
	virtual void PlaySound() {
		// ***************
	}

	virtual void DrawShape() {
		// **************
	}

	virtual void WriteText() {
		// **************
	}

	virtual void Connect() {
		// **************
	}
}

class MobileMessagerImp : public MessagerImp
{
public:
	virtual void PlaySound() {
		// ==============
	}

	virtual void DrawShape() {
		// ==============
	}

	virtual void WriteText() {
		// ==============
	}

	virtual void Connect() {
		// ==============
	}
};

// 业务抽象 m
// 类的数量： 1 + n + m
class MessagerLite : public Messager
{
public:
	virtual void Login(string username, string password)
	{
		messagerImp->Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		messagerImp->WriteText();
	}

	virtual void SendPicture(Image image) {
		messagerImp->DrawShape();
		// ..............
	}
};

class MessagerPerfect : public Messager
{
public:
	virtual void Login(string username, string password) {
		messagerImp->PlaySound();
		// ************
		messagerImp->Connect();
		// ..............
	}

	virtual void SendMessage(string messager) {
		messagerImp->PlaySound();
		// ************
		messagerImp->WriteText();
		// ..............
	}

	virtual void SendPicture(Image image) {
		messagerImp->PlaySound();
		// ************
		messagerImp->DrawShape();
		// ..............
	}
};

void Process()
{
	// 运行时装配
	MessagerImp* mImp = new PCMessagerImp();
	Messager* m = new Messager(mImp);
}
```

- 将抽象部分（业务功能）与实现部分（平台实现）分离，使它们都可以独立地变化

## 8.工厂方法（“对象创建”模式）
### “对象创建”模式
- 通过“对象创建”模式绕开new，来避免对象创建（new）过程中所导致的紧耦合（依赖具体类），从而支持对象创建的稳定。它是接口抽象之后的第一步
```C++
class Form;
class ManForm : public Form
{
	SplitterFactory* factory;  // 工厂

public:
	ManForm(SplitterFactory* factory)
	{
		this->factory = factory;
	}

	void Button1_Click() {
		string filePath = txtFilePath;
		int number = atoi(txtFileNumber);

		// 面向接口编程最简单的表现形式是，变量声明为抽象基类
		ISplitter* splitter =
			factory->CreateSplitter();  // 多态 new
		// 把兔子关进笼子里
		splitter->split();
	}
};

// 文件基类
class ISplitter
{
public:
	virtual void split() = 0;
	virtual ~ISplitter() {};
};

// 工厂基类
class SplitterFactory {
public:
	virtual ISplitter* CreateSplitter() = 0;
	virtual ~SplitterFactory();
};

// 文件具体类
class BinarySplitter : public ISplitter {

};

class TxtSplitter : public ISplitter {

};

class PictureSplitter : public ISplitter {

};

class VideoSplitter : public ISplitter {

};

// 工厂具体类
class BinarySplitterFactory : public SplitterFactory
{
public:
	virtual ISplitter* CreateSplitter()
	{
		return new BinarySplitter();
	}
};

class TxtSplitterFactory : public SplitterFactory
{
public:
	virtual ISplitter* CreateSplitter()
	{
		return new TxtSplitter();
	}
};

class PictureSplitterFactory : public SplitterFactory
{
public:
	virtual ISplitter* CreateSplitter()
	{
		return new PictureSplitter();
	}
};

class VideoSplitterFactory : public SplitterFactory
{
public:
	virtual ISplitter* CreateSplitter()
	{
		return new VideoSplitter();
	}
};
```

### 模式定义
- 定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使得一个类的实例化延迟（目的：解耦，手段：虚函数）到子类。              —— 《设计模式》GOF

### 要点总结（对着代码去读这三段话）
- Factory Method模式用于隔离类对象的使用者和具体类型之间的耦合关系。面对一个经常变化的具体类型，紧耦合关系（new）会导致软件的脆弱。

- Factory Method模式通过面向对象的手法，将所要创建
的具体对象工作延迟到子类，从而实现一种扩展（而非更改）的策略，较好地解解决了这种紧耦合关系。

- Factory Method模式解决“单个对象”的需求变化。缺点在于要求创建方法/参数相同。

## 9.抽象工厂“对象创建”模式
### 动机
- 在软件系统中，经常面临着“一系列相互依赖的对象”的创建工作；同时，由于需求的变化，往往存在更多系列对象的创建工作。
- 如何应对这种变化？如何绕过常规的对象创建方法（new），提供一种“封装机制”来避免客户程序和这种“多系列具体对象创建工作”的紧耦合

- 说明：代码从*工厂模式*改为*抽象工厂模式*
```C++
// 工厂模式
// 数据库访问基类
class IDBConnection {

};

class IDBCommand {

};

class IDBDataReader {

};

class IDBConnectionFactory {
public:
	virtual IDBConnection* CreateDBConnection() = 0;
};

class IDBCommandFactory {
public:
	virtual IDBConnection* CreateDBCommand() = 0;
};

class IDBDataReaderFactory {
public:
	virtual IDBConnection* CreateDBDataReader() = 0;
};

// 支持SQLService相关的类型
class SqlConnection : public IDBConnection
{

};

class SqlCommand : public IDBCommand
{

};

class SqlDataReader : public IDBDataReader
{

};

class SqlConnectionFactory : public IDBConnectionFactory {

};

class SqlCommandFactory : public IDBCommandFactory {

};

class SqlDataReaderFactory : public IDBDataReaderFactory {

};

class EmployeeDA0 {
	IDBConnectionFactory* dbConnectionFactory;
	IDBCommandFactory* dbCommandFactory;
	IDBDataReaderFactory* dbDataReaderFactory;

public:
	vector<EmployeeDA0> GetEmployees() {
		IDBConnection* connection =
			dbConnectionFactory->CreateDBConnection();
		connection->ConnectionString("...");

		IDBCommand* command =
			dbCommandFactory->CreateDBCommand();
		command->CommandText("...");
		command->SetConnection(connection);  // 关联性

		IDBDataReader* reader = command->ExecuteReader();  // 关联性
		while (reader->Read())
		{

		}
	}
};
```

```C++
// 数据库访问基类
class IDBConnection {

};

class IDBCommand {

};

class IDBDataReader {

};

class IDBFactory {
public:
	virtual IDBConnection* CreateDBConnection() = 0;
	virtual IDBCommand* CreateDBCommand() = 0;
	virtual IDBDataReader* CreateDBDataReader() = 0;
};


// 支持SQLService相关的类型
class SqlConnection : public IDBConnection
{

};

class SqlCommand : public IDBCommand
{

};

class SqlDataReader : public IDBDataReader
{

};

class SqlDBFactory :public IDBFactory {
public:
	virtual IDBConnection* CreateDBConnection() = 0;
	virtual IDBCommand* CrateDBCommand() = 0;
	virtual IDBDataReader* CreateDataReader() = 0;
};

class EmployeeDA0 {
	IDBFactory* dbFactory;

public:
	vector<EmployeeDA0> GetEmployees() {
		IDBConnection* connection =
			dbFactory->CreateDBConnection();
		connection->ConnectionString("...");

		IDBCommand* command =
			dbFactory->CreateDBCommand();
		command->CommandText("...");
		command->SetConnection(connection);  // 关联性

		IDBDataReader* reader = command->ExecuteReader();  // 关联性
		while (reader->Read())
		{

		}
	}
};
```
### 模式定义
- 提供一个接口，让该接口负责创建一系列“相关或者相互依赖的对象”，无需指定它们具体的类  —— 《设计模式》 GoF

### 要点总结
- 如果没有对应“多系列对象构建”的需求变化，则没有必要使用Abstract Factory模式，这时候使用简单的工厂完全可以。
- “系列对象”指的是在某一特定系列下的对象之间有相互依赖、或作用的关系。不同系列的对象之间不能相互依赖。
- Abstract Factory模式主要在于应对“新系列”的需求变动。其缺点在于难以应对“新对象”的需求变动

### 疑问：
- [ ] 为什么不把Connect、Command、DataReader三个也合到一个类当中

## 10. 原型模式（Prototype）（“对象创建”模式）

### 动机
- 在软件系统中，经常面临着“某些结构复杂的对象”的创建工作；由于需求的变化，这些对象经常面临着剧烈的变化，但是它们却拥有比较稳定一致的接口
- 如何应对这种变化？如何向“客户程序（使用这些对象的程序）”隔离出“这些易变对象”，从而使得“依赖这些易变对象的客户程序”不随着需求改变而改变？

### 模式定义
- 使用原型实例指定创建对象的种类，然后通过拷贝这些原型来创建新的对象 —— Gof
- 注意：这里是深克隆

### 什么时候用
- 使用工厂时需要考虑一个很复杂的中间状态，又希望保留这种中间状态，那就用原型模式
```C++
// 文件基类
class ISplitter
{
public:
	virtual void split() = 0;
	virtual ISplitter* Clone() = 0;  // 通过克隆自己来创建对象
	virtual ~ISplitter() {};
};

class BinarySplitter : public ISplitter {

	virtual ISplitter* Clone()
	{
		// 依赖于拷贝构造函数，所以拷贝构造函数一定要创建正确
		return new BinarySplitter(*this);
	}
};

class Form;
class ManForm : public Form
{
	ISplitter* prototype;  // 工厂

public:
	ManForm(ISplitter* factory)
	{
		this->prototype = prototype;
	}

	void Button1_Click() {

		ISplitter* splitter =
			prototype->Clone();  // 克隆原型

		splitter->split();
	}
};
```
### 要点总结
- Prototype模式同样用于隔离类对象的使用者和具体类型（易变类）之间的耦合关系，它同样要求这些“易变类”拥有“稳定的接口”
- Prototype模式对于“如何创建易变类的实体对象”采用“原型克隆”的方法来做，它使得我们可以非常灵活地动态创建“拥有某些稳定接口”的新对象——所需工作仅仅是注册一个新类的对象（即原型），然后在任何需要的地方Clone
- Prototype模式中的Clone方法可以利用某些框架中的序列化来实现深拷贝

## 11. 构建器（builder）（“对象创建”模式）

### 动机
- 在软件系统中，有时候面临着“一个复杂对象”的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着巨大的**变化**，但是将它们组合在一起的算法却相对**稳定**。
- 如何应对这种变化？如何提供一种“封装机制”来隔离出“复杂对象的各个部分”的变化，从而保持系统中的“稳定构建算法”不随着需求改变而改变？

### 模式定义
- 将一个复杂对象的构建与其表示相分离，使得同样的构建过程（稳定）可以创造不同的表示（变化）		—— 《设计模式》GoF
```C++
// 简单版本
class House
{
public:
	// 不能写成构造函数，因为在C++里面，构造函数调用虚函数完成的是静态绑定
	// 其实就是这几个Build函数没有实现，编译会出错的（代码以及移动）
	void Init()
	{
		// 构建过程是一样的，所以这5个步骤的顺序是不变的
		this->BuildPart1();

		for (int i = 0; i < 4; i++)
		{
			this->BuildPart2();  // 构建4面窗户
		}

		bool flag = this->BuildPart3();

		if (flag)
		{
			this->BuildPart4();
		}

		this->BuildPart5();
	}

	virtual ~House() {}

protected:
	// 这5个步骤需要组合一起使用
	virtual void BuildPart1() = 0;
	virtual void BuildPart2() = 0;
	virtual bool BuildPart3() = 0;
	virtual void BuildPart4() = 0;
	virtual void BuildPart5() = 0;
};

class StoneHouse : public House
{
protected:
	virtual void BuildPart1()
	{

	}

	virtual void BuildPart2()
	{

	}

	virtual bool BuildPart3()
	{

	}

	virtual void BuildPart4()
	{

	}

	virtual void BuildPart5()
	{

	}
};

int main()
{
	House* pHouse = new StoneHouse();
	pHouse->Init();
}
```

```C++
// 复杂版本：最大的作用在于将对象的表示和构建相分离

class House
{

};

class HouseBuilder  // 专门做构建
{
public:
	House* GetResult()
	{
		return pHouse;
	}
	virtual ~HouseBuilder(){}

	House* pHouse;

	virtual void BuildPart1() = 0;
	virtual void BuildPart2() = 0;
	virtual bool BuildPart3() = 0;
	virtual void BuildPart4() = 0;
	virtual void BuildPart5() = 0;
};

class StoneHouse : public House
{

};

class StoneHouseBuilder : public HouseBuilder
{
protected:
	virtual void BuildPart1()
	{

	}

	virtual void BuildPart2()
	{

	}

	virtual bool BuildPart3()
	{

	}

	virtual void BuildPart4()
	{

	}

	virtual void BuildPart5()
	{

	}
};

class HouseDirector  // 稳定的部分
{
public:
	HouseBuilder* pHouseBuilder = nullptr;

	HouseDirector(HouseBuilder* pHouseBuilder)
	{
		this->pHouseBuilder = pHouseBuilder;
	}

	House* Construct()
	{
		pHouseBuilder->BuildPart1();

		for (int i = 0; i < 4; i++)
		{
			pHouseBuilder->BuildPart2();
		}

		bool flag = pHouseBuilder->BuildPart3();

		if (flag)
		{
			pHouseBuilder->BuildPart4();
		}

		pHouseBuilder->BuildPart5();

		return pHouseBuilder->GetResult();
	}
};

int main()
{
	HouseBuilder* pHouseBuilder = new StoneHouseBuilder();
	HouseDirector* pHouseDirector = new HouseDirector(pHouseBuilder);
	pHouseDirector->Construct();
}
```
### 要点总结
- Builder模式主要用于“分步骤构建一个复杂对象”。在这其中“分步骤”是一个稳定的算法，而复杂对象的各个部分则经常变化
- 变化点在哪里，封装哪里————Builder模式主要在于应对“复杂对象各个部分”的频繁需求变动。其缺点在于难以应对“分步骤构建算法”的需求变动
- Builder模式中，要注意不同语言中构造器内调用函数的差别（C++ VS. C#）
- 老师：从变化里面能找出稳定的部分才可以使用设计模式，否则没有设计模式适合你

## 12. Singleton 单件模式（对象性能模式）

### 对象性能模式
- 面向对象很好地解决了“抽象”的问题，但是必不可免地要付出一定的代价。对于通常情况来讲，面向对象的成本大都可以忽略不计。但是某些情况，面向对象所带来的成本必须谨慎处理。
- 典型模式
	- Singleton
 	- Flyweight  

### 动机
- 在软件系统中，经常有这样一些特殊的类，必须保证它们在系统中只存在一个实例，才能确保它们的逻辑正确性、以及良好的效率。
- 如何绕过常规的构造器，提供一种机制来保证一个类只有一个实例？
- 这应该是类设计者的责任，而不是使用者的责任

### 模式定义
- 保证一个类仅有一个实例，并提供一个该实例的全局访问点
```C++
class Singleton
{
private:
	// 写成私有的，外界不能用，编译器也不会默认生成
	Singleton();
	Singleton(const Singleton& other);

public:
	static Singleton* getInstance();
	static Singleton* m_instance;
};

Singleton* Singleton::m_instance = nullptr;

// 线程非安全版本：单线程OK，多线程不行
Singleton* Singleton::getInstance()
{
	if (m_instance == nullptr)
	{
		m_instance = new Singleton();
	}

	return m_instance;
}

// 线程安全版本：但锁的代价过高（锁前检查）
Singleton* Singleton::getInstance()
{
	Lock lock; // 虽然保证了全局唯一和线程安全，但锁的代价过高。怎么高了？

	if (m_instance == nullptr)
	{
		m_instance = new Singleton();
	}

	return m_instance;
}

// 双检查锁(double check lock)：但由于内存读写reorder不安全（锁后检查）
Singleton* Singleton::getInstance()
{
	if (m_instance == nullptr)
	{
		Lock lock;
		if (m_instance = nullptr)
		{
			m_instance = new Singleton();
		}
	}

	return m_instance;
}

// C++11版本之后的跨平台实现（volatile）
std::atomic<Singleton*>Singleton::m_instance;
std::mutex Singleton::m_mutex;

Singleton* Singleton::getInstance()
{
	// 声明一个原子指针，可以帮助屏蔽编译器的reorder
	Singleton* tmp = m_instance.load(std::memory_order_relaxed);
	// 获取内存fence。fence可以理解为内存的屏障，内存reorder的一个保护。
	// 之后这个tmp变量就不会被reorder了
	std::atomic_thread_fence(std::memory_order_acquire);  // 
	
	if (tmp == nullptr)
	{
		// 双检查锁还是要锁上的
		std::lock_guard<std::mutex> lock(m_mutex);
		// 取变量都以这种方式来取了
		tmp = m_instance.load(std::memory_order_relaxed);
		
		if (tmp = nullptr)
		{
			// 之前的操作保证了这一行不会reorder
			tmp = new Singleton;
			std::atomic_thread_fence(std::memory_order_release);  // 释放内存fence
			// tmp再交给m_instance
			m_instance.store(tmp, std::memory_order_relaxed);
		}
	}

	return tmp;
}
```
### 要点总结
- Singleton模式中的实例构造器可以设置为protected以允许子类派生。
- Singleton模式一般不要支持拷贝构造函数和Clone接口，因为这有可能导致多个对象实例，与Singleton模式的初衷违背
- 如何实现多线程环境下安全的Singleton？注意对双检查锁的正确实现

## 13 Flyweight享元模式（对象性能模式）

### 动机（Motivation）
- 在软件系统采用纯粹对象方案的问题在于大量细粒度的对象会很快充斥在系统中，从而带来很高的运行时代家————主要指内存需求方面的代价
- 如何在避免大量细粒度对象问题的同时，让外部客户程序仍然能够透明地使用面向对象的方式来进行操纵？

### 模式定义
- 运用共享技术有效地支持大量细粒度的对象		———— 《设计模式》GoF
- 量多大才算大？需要评估

```C++
class Font
{
private:
	// unique object key
	string key;

	// object state
	// ...

public:
	Font(const string& key)
	{
		// ...
	}
};

class FontFactory
{
private:
	map<string, Font*> fontPool;

public:
	Font* GetFont(const string& key)
	{
		map<string, Font*>::iterator item = fontPool.find(key);

		if (item != fontPool.end())
		{
			return fontPool[key];
		}
		else
		{
			Font* font = new Font(key);
			fontPool[key] = font;
			return font;
		}
	}

	void Clear()
	{
		// ...
	}
};  // 平台上实现起来五花八门，但总体的思想是一样的
```
### 要点总结
- 面向对象很好地解决了抽象性的问题，但是作为一个运行在机器中的程序实体，我们需要考虑对象的代价问题。Flyweight主要解决面向对象的代价问题，一般不触及面向对象的抽象性问题。
- Flyweight采用对象共享的做法来降低系统中对象的个数，从而降低细粒度对象给系统带来的内存压力。再具体实现方面，要注意对象状态的处理。
- 对象的数量太大从而导致对象内存开销加大——什么样的数量才算大？这需要我们仔细的根据具体应用情况进行评估，而不能凭空臆断。

## 14 Facade门面模式（“接口隔离”模式）
### “接口隔离”模式
- 在组件构建过程中，某些接口之间直接的依赖常常会带来很多问题、甚至根本无法实现。采用添加一层间接（稳定）接口，来隔离本来互相紧密关联的接口是一种常见的解决方案
- 典型模式
	- Facade
	- Proxy
	- Adapter
	- Mediator
### 动机（Motivation）
- 上述A方案的问题在于组件的客户和组件中各种复杂的子系统有了过多的耦合，随着外部客户程序和各个子系统的演化，这种过多的耦合面临很多变化的挑战。
- 如何简化外部客户程序和系统间的交互接口？如何将外部客户程序的演化和内部子系统的变化之间的依赖相互解耦。
### 模式定义
- 为子系统中的一组接口提供一个一致（稳定））的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用（复用）

- 相差很大的代码用的可能都是facade模式，这节课不给代码了

### 要点总结
- 从客户程序的角度来看，Facade模式建华路整个组件系统的接口，对于组件内部与外部客户程序来说，达到了一种“解耦”的效果————内部子系统的任何变化不会影响到Facade接口的变化
- Facade设计模式更注重从架构的层次去看整个系统，而不是单个类的层次。Facade很多时候更是一种架构设计模式。
- Facade设计模式并非一个集装箱，可以任意地放任何多个对象。Facade模式中组件的内部应该是“相互耦合关系比较大的一系列组件”，而不是一个简单的功能集合。

## 15 Proxy代理模式（“接口隔离”模式）
### 动机（Motivation）
- 在面向对象系统中，有些对象由于某种原因（比如对象创建的开销很大，或者某些系统需要安全控制，或者需要进程外的访问等）直接访问会给使用者、或者系统结构带来很多麻烦。
- 如何在不失去透明操作对象的同时来管理/控制这些对象特有的复杂性？增加一层间接层是软件开发中常见的解决方式。
### 模式定义
```C++
class ISubject
{
public:
	virtual void process();

};

class RealSubject : public ISubject
{
public:
	virtual void process()
	{
		// ...
	}
};

class ClientApp
{
	ISubject* subject;

public:
	ClientApp()
	{
		subject = new RealSubject;
	}

	void DoTask()
	{
		// ...
		subject->process();
	}
};
```
```C++
class ISubject
{
public:
	virtual void process();

};

// proxy的设计。针对RealSubject的代理
class SubjectProxy : public ISubject
{
public:
	virtual void process()
	{
		// 对RealSubject的一种间接访问
		// 采用一致的接口，从而让客户端感觉是一致的
	}
};

class ClientApp
{
	ISubject* subject;

public:
	ClientApp()
	{
		subject = new SubjectProxy;
	}

	void DoTask()
	{
		// ...
		subject->process();
	}
};
```
### 要点总结
- “增加一层间接层”是软件系统中对许多复杂问题的一种常见解决方法。在面向对象系统中，直接使用某些对象会带来很多问题，作为间接层的Proxy对象便是解决这一问题的常用手段。
- 具体Proxy设计模式的实现方法、实现粒度都相差很大，有些可能对丹哥对象做细粒度的控制，如copy-on-write技术，有些可能对组件模块提供抽象代理层，在架构层次对对象做Proxy。
- Proxy并不一定要求保持接口完整的一致性，只要能够实现间接控制，有时候损失一些透明性是可以接收到

## 16 Adapter 适配器（“接口隔离”模式）
### 动机（Motivation）
- 在软件系统中，由于应用环境的变化，常常需要将“一些现存的对象”放在新的环境中应用，但是新环境要求的接口是这些现存对象所不满足的
- 如何应对这种“迁移的变化”？如何技能利用现有对象的良好实现，同时又能满足新的应用环境所要求的接口？

### 模式定义
- 将一个类的接口转换成客户希望的另一个接口。Adapter模式是的原本由于接口不兼容而不能在一起工作的那些类可以一起工作
```C++
// 目标接口（新接口）
class ITarget
{
public:
	virtual void process()
	{
		// ...
	}
};

// 遗留接口（老接口）
class IAdaptee
{
public:
	virtual void foo(int data) = 0;
	virtual int bar() = 0;
};

class OldClass: public IAdaptee
{
public:
	virtual void foo(int data)
	{

	}

	virtual int bar()
	{

	}

	// 两者内部有关联性，才可以进行接口的转换
};

// 对象适配器
class Adapter :public ITarget  // 继承
{
protected:
	IAdaptee* pAdaptee;  // 组合。这就是对象适配器，通过组合对象来实现

public:
	Adapter(IAdaptee* pAdaptee)
	{
		this->pAdaptee = pAdaptee;
	}

	virtual void process()
	{
		int data = pAdaptee->bar();

		pAdaptee->foo(data);

	}
};

// 类适配器
class Adapter :public ITarget, protected OldClass  // 多继承
{
	// 固定了OldClass就缺乏灵活性，固定死了。
};

int main()
{
	IAdaptee* pAdaptee = new OldClass();

	ITarget* pTarget = new Adapter(pAdaptee);
	pTarget->process();
}

// STL里面的队列和栈也相互转换
// 它们既是接口又是实现，所以没有ITarget
class stack
{
	dequeue container;
};

class queue
{
	dequeue container;
};
```
### 要点总结
- Adapter模式主要应用于“希望复用一些现存的类，但是接口又与复用环境要求不一致的情况”，在遗留代码复用、类库迁移等方面非常有用
- GoF23 定义了两种Adapter模式的实现结构：对象适配器和类适配器。但类适配器采用“多继承”的实现方式，一般不推荐使用。对象适配器采用“对象组合”的方式，更符合松耦合精神
- Adapter模式可以实现的非常灵活，不必拘泥于GoF23中定义的两种结构。例如，完全可以将Adapter模式中的“现存对象”作为新的接口方法参数，来达到适配的目的。
- 老师：类适配器今天已经用的很少很少了。可以这么说，类适配器只有坏处没有好处。直接换成对象适配器多好。

## 17 Mediator 中介者（“接口隔离”模式）
### 动机（Motivation）
- 在软件构建过程中，经常会出现多个对象互相关联交互的情况，对象之间常常会维持一种复杂的引用关系，如果遇到一些需求的更改，这种直接的引用关系将不断得面临变化
- 在这种情况下，我们可使用一个“中介对象”来管理对象的关联关系，避免相互交互的对象之间的紧耦合引用关系，从而更好地抵御变换

### 模式定义
- 用一个中介对象来封装（封装变化）一些列的对象交互。中介者使各对象不需要显式的相互引用（编译时依赖 -> 运行时依赖），从而使其耦合松散（管理变化），而且可以独立地改变它们之间的交互。

### 要点总结
- 将多个对象间复杂的关联关系解耦，Mediator模式将多个对象间的控制逻辑进行集中管理，变“多个对象互相关联”为“多个对象和一个中介者关联”，简化了系统的维护，抵御了可能的变化
- 随着控制逻辑的复杂化，Mediator具体对象的实现可能相当复杂。这时候可以对Mediator对象进行分解处理
- Facade模式是解耦系统间（单向）的对象关联关系；Mediator模式是解耦系统内各个对象之间（双向）的关联关系

## 18 State 状态模式（“状态变化”模式）（对比“策略模式”）
### “状态变化”模式
- 在组件构建过程中，某些对象的状态经常面临变化，如何对这些变化进行有效的管理？同时又维持高层模块的稳定？“状态变化”模式违者一问题提供了一种解决方案
- 典型模式
	- State
 	- Memento
### 动机（Motivation）
- 在软件构建过程中，某些对象的状态如果改变，其行为也会随之发生变化，比如文档处于只读状态，其支持的行为和读写状态支持的行为就可能完全不同。
- 如何在运行时根据对象的状态来透明地更改对象的行为？而不会为对象操作和状态转化之间引入紧耦合

### 模式定义
- 运行一个对象在其内部状态改变时改变它的行为。从而使对象看起来似乎修改了其行为。		———— 《设计模式》 GoF
```C++
enum NetworkState
{
	Network_Open,
	Network_Close,
	Network_Connect,
};

class NetworkProcessor
{
	NetworkState state;

public:
	void Operation1()
	{
		if (state == Network_Open)
		{
			// ***********
			state = Network_Close;
		}
		else if (state == Network_Close)
		{
			// ..........
			state = Network_Connect;
		}
		else if (state == Network_Connect)
		{
			// $$$$$$$$$$
			state = Network_Open;
		}
	}

	void Operation2()
	{
		if (state == Network_Open)
		{
			// ***********
			state = Network_Connect;
		}
		else if (state == Network_Close)
		{
			// ***********
			state = Network_Open;
		}
		else if (state == Network_Connect)
		{
			// ***********
			state = Network_Close;
		}
	}

	void Operation3()
	{

	}
};
```
```C++
class NetworkState
{
public:
	NetworkState* pNext;  // 实际开发可能设置为保护或私有
	
	virtual void Operation1() = 0;
	virtual void Operation2() = 0;
	virtual void Operation3() = 0;
	
	virtual ~NetworkState();
};

class OpenState:public NetworkState
{
	// 这个状态倾向于用Singleton，没有要有多个
	static NetworkState* m_instance;
public:
	static NetworkState* getInstance()
	{
		if (m_instance == nullptr)
		{
			m_instance == new OpenState();
		}

		return m_instance;
	}

	virtual void Operation1()
	{
		// ***********
		pNext = CloseState::getInstance();
	}

	virtual void Operation2()
	{
		// ..........
		pNext = ConnectState::getInstance();
	}

	virtual void Operation3()
	{
		// $$$$$$$$$$
		pNext = OpenState::getInstance();
	}
};

class CloseState :public NetworkState
{
	// ...
};

// 扩展
class ConnectState :public NetworkState
{
	// ...
};

class NetworkProcessor
{
	NetworkState* pState;

public:
	NetworkProcessor(NetworkState* pState)
	{
		this->pState = pState;
	}

	void Operation1()
	{
		// ...
		pState->Operation1();
		pState = pState->pNext;
		// ...
	}

	void Operation2()
	{
		// ...
		pState->Operation2();
		pState = pState->pNext;
		// ...
	}

	void Operation3()
	{
		// ...
		pState->Operation3();
		pState = pState->pNext;
		// ...
	}
};
// 老师：虚函数的本质是一个运行时的if-else
// 弹幕：与策略模式的区别是变化封装在各个子类里面了
// 弹幕：状态增加，则只需要增加一个新的状态类。操作增加，只需要在接口中添加操作，子类实现对应操作即可。以前的代码不发生变化
```
### 要点总结
- State模式将所有与一个特定状态相关的行为都放入一个State的子对象中，在对象状态切换时，切换相应的对象；但同时维持State的接口，这样实现了具体操作与状态转换之间的解耦。
- 为不同的状态引入不同的对象使得状态转换变的更加明确，而且可以保证不会出现状态不一致的情况，因为转换是原子性的————即要么彻底转换过来，要么不转换。
- 如果State对象没有实例变量，那个各个上下文可以共享同一个State对象，从而节省开销。（Singleton）

## 19 Memento（“状态变化”模式）
### 动机（Motivation）
- 在软件构建过程中，某些对象的状态在转换过程中，可能由于某种需要，要求程序能够回溯到对象之前处于某个点时的状态。如果使用一些公有接口来让其他对象得到对象的状态，便会暴露对象的细节实现。
- 如何实现对象状态的良好保存与恢复？但同时又不会因此而破坏对象本身的封装性。

### 模式定义
- 在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可以将该对象恢复到原先保存的状态。		———— 《设计模式》 GoF

```C++
#include <string>
using std::string;

class Memento
{
	string state;
	// ...
public:
	Memento(const string& s) :state(s) {}
	string getState() const { return state; }
	void setState(const string& s) { state = s; }
};

class Originator
{
	string state;
	// ......
public:
	Originator() {}
	Memento createMemento()
	{
		// 在实际的实现中可能相当复杂
		Memento m(state);  // 这句话就是“拍照”，当前内存状态的快照
		return m;
	}
	void setMomento(const Memento& m)
	{
		state = m.getState();
	}
};

// caretacker
int main()
{
	Originator originator;

	// 捕获对象状态，存储到备忘录。类似深拷贝的 内存快照方式
	Memento mem = originator.createMemento();

	//  ... 改变originator状态

	// 从备忘录中恢复
	originator.setMomento(mem);
}
```
- 老师：这个模式在今天其实有点过时的，效率有点低，这本书是94年出的，思想的是不变的。
- 老师：有了序列化，和内存编码之后，已经不采用面向对象的方式实现。

### 要点总结
- 备忘录（Memento）存储原发器（Originator）对象的内部状态，在需要时恢复原发器的状态。
- Memento模式的核心是数据隐藏，即Originator需要向外界隐藏信息，保持其封装性。但同时有需要将状态保存到外界（Memento）
- 由于现代语言运行时（如C#、Java等）都需要相当的对象序列化支持，因此往往采用效率较好、又较容易正确是现代序列化方案来实现Memento模式

## 20 组合模式（Composite）
### “数据结构”模式
- 常常有一些组件在内部具有特定的数据结构，如果让客户程序依赖这些特定的数据结构，将极大地破坏组件的复用。这时候，将这些特定数据结构封装在内部，在外部提供统一的接口，来实现与特定数据结构无关的访问，是一种行之有效的解决方案。
- 典型模式:
	- Composite
	- Iterator
	- Chain of Resposibility

### 动机（Motivation）
- 在软件的某些情况下，客户代码过多地依赖于对象容器复杂的内部实现结构，对象容器内部实现结构（而非抽象接口）的变化将引起客户代码的频繁变化，带来了代码的维护性、扩展性等弊端。
- 如何将“客户代码与复杂的对象容器结构”解耦？让对象容器自己来实现自身的复杂结构，从而使得客户代码就像处理简单对象一样来处理复杂的容器对象？

### 模式定义
- 将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得用户对单个对象和组合对象的使用具有一致性（稳定）。		———— 《设计模式》 GoF
```C++
#include <algorithm>
#include <string>

using namespace std;

class Component
{
public:
	virtual void process() = 0;
	virtual ~Component() {}
};

class Composite :public Component
{
	string name;
	list<Component*> element;
public:
	Composite(const string& s) :name(s) {}
	void add(Component* element)
	{
		if (this == element)
		{
			return;
		}

		element->push_back(element);
	}
	void remove(Component* element)
	{
		if (this == element)
		{
			return;
		}

		element->remove(element);
	}
	void process()  // 对父类override
	{
		// 1.process current node

		// 2. process leaf nodes
		for (auto& e : elements)
		{
			e->process();  // 多态调用
		}
	}
};

class Leaf :public Component
{
	string name;
public:
	Leaf(string s) : name(s) {}
	void process()
	{
		// process current node
	}
};

void Invoke(Component& c)
{
	// ...
	c.process();
	//
}

int main()
{
	Composite root("root");
	Composite treeNode1("treeNode1");
	Composite treeNode2("treeNode2");
	Composite treeNode3("treeNode3");
	Composite treeNode4("treeNode4");
	Leaf leaf1("leaf1");
	Leaf leaf2("leaf2");

	root.add(&treeNode1);
	treeNode1.add(&treeNode2);
	treeNode2.add(&leaf1);

	root.add(&treeNode3);
	treeNode3.add(&treeNode4);
	treeNode4.add(&leaf2);

	Invoke(root);
	Invoke(leaf2);
	Invoke(treeNode3);
}
```

### 要点总结
- Composite模式采用树形结构来实现普遍存在的对象容器，从而将“一对多”的关系转化为“一对一”的关系，使得客户代码可以一致地（复用）处理对象和对象容器
- 将“客户代码与复杂的对象容器结构”解耦是Composite的核心思想，解耦之后，客户代码将于纯粹的抽象接口——而非对象容器的内部实现结构——发生依赖，从而更难“应对变化”。
- Composite模式在具体实现中，可以让父对象中的子对象反向追溯。如果付对象有频繁的遍历需求，可使用缓存技巧来改善效率。

## 21 迭代器（“数据结构模式”）
### 动机（Motivation）
- 在软件构建过程中，集合对象内部结构常常变化各异。但对于这些集合对象，我们希望在不暴露其内部结构的同时，可以让外部客户代码透明地访问其中包含的元素；同时这种“透明遍历”也为“同一种算法在多种几何对象上进行操作”提供了可能。
- 使用面向对象技术将这种编辑机制抽象为“迭代器对象”为“应对变化中的几何对象”提供一种优雅的方式。

### 模式定义
- 提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露（稳定）该对象的内部表示。		———— 《设计模式》 GoF

```C++
template<typename T>
class Iterator
{
public:
	virtual void first() = 0;
	virtual void next() = 0;
	virtual bool isDone() = 0;
	virtual T& current() = 0;
};

template<typename T>
class MyCollection
{
public:
	Iterator<T> GetIterator()
	{
		// ... 
	}
};

template<typename T>
class CollectionIterator :public Iterator<T>
{
	MyCollection<T> mc;
public:
	CollectionIterator(const MyCollection<T>& c): mc(c){ }
	void first() override
	{

	}
	void next() override
	{

	}
	bool isDone() override
	{

	}
	T& current() override
	{

	}
};

void MyAlgorithm()
{
	MyCollection<int> mc;
	Iterator<int> iter = mc.GetIterator();

	for (iter.first(); !iter.isDone(); iter.next())
	{
		cout << iter.current() << endl;
	}
}
```
- 老师：这个模式其实已经过时了，因为STL里面现在已经有了迭代器，不需要我们单独 再去写了。但是两者的思想是相通的。
- 老师：基于模板方式的迭代器要高于基于虚函数面向对象的方式的迭代器

### 要点总结
- 迭代抽象：访问一个聚合对象的内容而无需暴露它的内部表示
- 迭代多态：为遍历不同的集合结构提供一个统一的接口，从而支持同样的算法在不同的集合结构上进行操作
- 迭代器的健壮性考虑：遍历的同时更改迭代器所在的集合结构，会导致问题。

## 22 职责链（Chain of Resposibility）（“数据结构”模式）
### 动机（Motivation）
- 在软件构建过程中，一个请求可能被多个对象处理，但是每个请求在运行时只能有一个接受者，如果显式指定，将必不可少地带来请求发送者与接受者的紧耦合
- 如何使请求的发送者不需要指定具体的接受者？让请求的接受者自己在运行时决定来处理请求，从而使两者解耦。

### 模式定义
- 使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递请求，知道以一个对象处理它为止。		———— 《设计模式》 GoF

```C++
#include <iostream>
#include <string>
using namespace std;

enum class RequestType
{
	REQ_GANDLER1,
	REQ_GANDLER2,
	REQ_GANDLER3
};

class Request
{
	string description;
	RequestType reqType;
public:
	Request(const string& desc, RequestType type) 
		: description(desc), reqType(type) { }
	RequestType getReqType() const { return reqType; }
	const string& getDescription() const { return description; }
};

class ChainHandler
{
	ChainHandler* nextChain;  // 多态指针指向自身，形成了一个链表
	void sendReqestToNextHandler(const Request& req)
	{
		if (nextChain != nullptr)
		{
			nextChain->handle(req);
		}
	}

protected:
	virtual bool canHandleRequest(const Request& rep) = 0;
	virtual void processRequest(const Request& req) = 0;

public:
	ChainHandler() { nextChain = nullptr; }
	void setNextChain(ChainHandler* next) { nextChain = next; }
	void handle(const Request& req)
	{
		if (canHandleRequest(req))
		{
			// 能处理就不往下传递了
			processRequest(req);
		}
		else
		{
			// 职责链的处理逻辑，如果我当前这个链表不能处理，就交给下一个链表来处理
			sendReqestToNextHandler(req);
		}
	}
};

class Handler1 : public ChainHandler
{
protected:
	bool canHandleRequest(const Request& req) override
	{
		return req.getReqType() == RequestType::REQ_GANDLER1;
	}
	void processRequest(const Request& req) override
	{
		cout << "Hander1 is handle reqest: " << req.getDescription() << endl;
	}
};

class Handler2 : public ChainHandler
{
protected:
	bool canHandleRequest(const Request& req) override
	{
		// 在实际情况下，这个判断可能会复杂很多
		return req.getReqType() == RequestType::REQ_GANDLER2;
	}
	void processRequest(const Request& req) override
	{
		cout << "Hander2 is handle reqest: " << req.getDescription() << endl;
	}
};

class Handler3 : public ChainHandler
{
protected:
	bool canHandleRequest(const Request& req) override
	{
		return req.getReqType() == RequestType::REQ_GANDLER3;
	}
	void processRequest(const Request& req) override
	{
		cout << "Hander3 is handle reqest: " << req.getDescription() << endl;
	}
};

int main()
{
	Handler1 h1;
	Handler2 h2;
	Handler3 h3;

	h1.setNextChain(&h2);
	h2.setNextChain(&h3);

	Request req("process task ... ", RequestType::REQ_GANDLER3);
	h1.handle(req);
	return 0;

}
```

### 要点总结
- Chain of Responsibility 模式的应用场合在于“一个请求可能有多个接受者，但是最后真正的接受者只有一个”，这时候请求发送者与接收者的耦合有可能出现“变化脆弱”的症状，职责链的目的就是将二者解耦，从而更好的应对变化。
- 应用了Chain of ResPonsibility模式后，对象的职责分派将更具灵活性。我们可以在运行时动态添加/修改请求的处理职责。
- 如果请求传递到职责链的末尾仍得不到处理，应该有一个合理的缺省机制。这也是每一个接收对象的责任，而不是发出请求对象的责任。

## 23 命令模式
### “行为变化”模式
- 在组件的构建过程中，组件行为的变化经常导致组件本身剧烈的变化。“行为变化”模式将组件的行为和组件本身进行解耦，从而支持组件行为的变化，实现两者之间的松耦合。
- 典例模式：
	- Command
 	- Visitor

### 动机（Motivation）
- 在软件构建过程中，“行为请求者”与“行为实现者”通常呈现一种“紧耦合”。但在某些场合——比如需要对行为进行“记录、撤销/重做(ndo/redo)、事务”等处理，这种无法抵御变化的紧耦合是不适合的。
- 在这种情况下，如何将“行为请求者”与“行为实现者”解耦？将一组行为抽象为对象，可以实现二者之间的松耦合。

### 模式定义
将一个请求（行为）封装成一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤销的操作。		———— 《设计模式》 GoF

```C++
#include <vector>
#include <string>
#include <iostream>
using namespace std;

class Command
{
public:
	virtual void execute() = 0;
};

class ConcreteCommand1 : public Command
{
	string arg;
public:
	ConcreteCommand1(const string& a) : arg(a) {}
	void execute() override
	{
		cout << "#1 process..." << arg << endl;
	}
};

class ConcreteCommand2 : public Command
{
	string arg;
public:
	ConcreteCommand2(const string& a) : arg(a) {}
	void execute() override
	{
		cout << "#2 process..." << arg << endl;
	}
};

class MacroCommand : public Command
{
	vector<Command*> commands;
public:
	void addCommand(Command* c) { commands.push_back(c); }
	void execute() override
	{
		for (auto & c: commands)
		{
			c->execute();
		}
	}
};

int main()
{
	ConcreteCommand1 command1(receiver, "Arg ###");
	ConcreteCommand2 command2(receiver, "Arg $$$");

	MacroCommand macro;  // 既是对象，又同时表征行为
	macro.addCommand(&command1);
	macro.addCommand(&command2);

	macro.execute();
}
```

### 要点总结
- Command模式的根本目的在于将“行为请求者”与“行为实现者”解耦，在面向对象语言中，常见的实现手段是“将行为抽象为对象”。
- 实现COmmand接口的具体命令对象ConcreteCommand有时候根据需要可能会保存一些额外的状态信息。通过使用Composite模式可以将多个“命令”封装为一个“复合命令”MacroCommand。
- Command模式与C++中的函数对象有些类似。但两者定义行为接口的规范有所区别：Command以面向对象中的“接口——实现”来定义行为接口规范，更严格，但有性能损失；C++函数对象以函数签名来定义行为接口规范，更灵活，性能更高。
- 老师：很多模式如迭代器，Command在C++已经被泛型编程所替代。如函数对象
- 老师：有一种观点，设计模式是弥补语言设计的不足而出现的

## 24 访问器
### 动机（Motivation）
- 在软件构建过程中，由于需求的改变，某些类层次结构中常常需要增加新的行为（方法），如果直接在基类中做这样的更改，将会给子类带来很繁重的变更负担，甚至破坏原有设计。
- 如何在不更改类层次结构的前提下，在运行时根据需要透明地为类层次结构上的各个类动态添加新的操作，从而避免上述问题？

### 模式定义
- 表示一个作用于某个对象结构中的各个元素的操作。使得可以在不改变（稳定）各元素的类的前提下定义（扩展）作用域这些元素的新操作（变化）。		———— 《设计模式》 GoF
- 原代码
```C++
class Visitor;

class Element
{
public:
	virtual void Func1() = 0;
	virtual void Func2(int data) = 0;
	virtual void Func3(int data) = 0;
	virtual ~Element() {}
};

class ElementA : public Element
{
public:
	void Func1() override
	{
		// ...
	}

	void Func2(int data) override
	{
		// ...
	}
};

class ElementB : public Element
{
public:
	void Func1() override
	{
		// ***
	}

	void Func2(int data) override
	{
		// ...
	}
};
```
- 修改后
```C++
#include <iostream>
using namespace std;

// Visitor有一个前提：我能预料到未来为这个类添加新的操作
class Visitor;

class Element
{
public:
	virtual void accept(Visitor& visitor) = 0;  // 第一次多态辨析
	virtual ~Element() {}
};

class ElementA : public Element
{
public:
	void accept(Visitor& visitor) override
	{
		visitor.visitElementA(*this);  // 第二次多态辨析
	}
};

class ElementB : public Element
{
public:
	void accept(Visitor& visitor) override
	{
		visitor.visitElementB(*this);  // 第二次多态辨析
	}
};

class Visitor
{
public:
	virtual void visitElementA(ElementA& element) = 0;
	virtual void visitElementB(ElementB& element) = 0;
	virtual ~Visitor() {}
};

// -----------------------------------------------------
// 扩展1
class Visitor1: public Visitor
{
public:
	void visitElementA(ElementA& element) override
	{
		cout << "Visitor1 is processing ElementA" << endl;
	}

	void visitElementB(ElementB& element) override
	{
		cout << "Visitor1 is processing ElementB" << endl;
	}
};

// 扩展2
class Visitor2 : public Visitor
{
public:
	void visitElementA(ElementA& element) override
	{
		cout << "Visitor2 is processing ElementA" << endl;
	}

	void visitElementB(ElementB& element) override
	{
		cout << "Visitor2 is processing ElementB" << endl;
	}
};

int main()
{
	Visitor2 visitor;

	ElementB elementB;
	elementB.accept(visitor);  // double dispatch

	ElementA elementA;
	elementA.accept(visitor);
}
```
- 模式缺点：ConcreateElementA和ConcreteElementB必须也稳定1。也就是子类个数稳定，这是很大的一个前提，常常满足不了，导致这个模式很脆弱。
### 要点总结
- Visitor模式通过所谓双重分发（double dispatch）来实现在不更改（不添加新的操作-编译时）Element类层次结构的前提下，在运行室透明地为类层次结构上的各个类动态添加新的操作（支持变化）
- 所谓双重分发即Visistor模式中间包括了两个多态分发（注意其中的多态机制）：第一个为accept方法的多态辨析；第二个为visitElementX方法的多态辨析。
- Visitor模式的最大缺点在与扩展类层次结构（增添新的Element子类）。会导致Visitor类的改变。因此Visitor模式适用于“Element”类层次结构稳定，而其中的操作却经常面临频繁改动。
- 老师：Visitor模式前提太苛刻，导致用的很少。但是一旦用，就会用的很重，就是你整个类结构都要被“Visitor”所绑架

## 25 解析器
### “领域规则”模式
- 在特定领域中，某些变化虽然频繁，但可以抽象为某种规则。这时候，结合特定领域，将问题抽象为语法规则，从而给出在该领域下的一般性解决方案。
- 典例模式
	- Interpreter 

### 动机（Motivation）
-在软件构建过程中，如果某一特定领域的问题比较复杂，类似的结构不断重复出现，如果使用普通的编程方式来实现将面临频繁的变化。
- 在这种情况下，将特定领域的问题表达为某种语法规则下的句子，然后侯建一个解释器来解释这样的句子，从而达到解决问题的目的。

### 模式定义
给定一个语言，定义它的文法的一种表示，并定义一种解释器，这个解释器使用该表示来解释语言中的句子。		———— 《设计模式》 GoF
```C++
#include <iostream>
#include <map>
#include <stack>
#include <string>

using namespace std;

class Expression
{
public:
	virtual int interpreter(map<char, int>) = 0;
	virtual ~Expression() {}
};

// 变量表达式
class VarExpression :public Expression
{
	char key;
public:
	VarExpression(const char& key)
	{
		this->key = key;
	}

	int interpreter(map<char, int>var) override
	{
		return var[key];
	}
};

// 符号表达式（特点是左右两个节点又是表达式）
class SymbolExpression :public Expression
{
protected:
	// 运算符左右两个参数
	Expression* left;
	Expression* right;
public:
	SymbolExpression(Expression* left, Expression* right) :
		left(left), right(right) {}
};

// 加法运算
class AddExpression :public SymbolExpression
{
public:
	AddExpression(Expression* left, Expression* right) :
		SymbolExpression(left, right) {}

	int interpreter(map<char, int>var) override
	{
		return left->interpreter(var) + right->interpreter(var);
	}
};

// 减法运算
class SubExpression :public SymbolExpression
{
public:
	SubExpression(Expression* left, Expression* right) :
		SymbolExpression(left, right) {}

	int interpreter(map<char, int>var) override
	{
		return left->interpreter(var) - right->interpreter(var);
	}
};

Expression* analyse(string expStr)
{
	stack<Expression*> expStack;
	Expression* left = nullptr;
	Expression* right = nullptr;

	for (int i = 0; i < expStr.size(); i++)
	{
		switch (expStr[i])
		{
		case '+':
			// 加法表达式
			left = expStack.top();
			right = new VarExpression(expStr[++i]);
			expStack.push(new AddExpression(left, right));
			break;
		case '-':
			// 减法表达式
			left = expStack.top();
			right = new VarExpression(expStr[++i]);
			expStack.push(new SubExpression(left, right));
			break;
		default:
			// 终结表达式(叶子结点，即变量表达式）
			expStack.push(new VarExpression(expStr[i]));
		}
	}

	Expression* expression = expStack.top();
	
	return expression;
}

void release(Expression* expression)
{
	// 释放表达式树的节点内存...
}

int main()
{
	string expStr = "a+b-c+d-e";
	map<char, int>var;
	var.insert(make_pair('a', 5));
	var.insert(make_pair('b', 2));
	var.insert(make_pair('c', 1));
	var.insert(make_pair('d', 6));
	var.insert(make_pair('e', 10));

	Expression* expression = analyse(expStr);
	int result = expression->interpreter(var);
	cout << result << endl;
	release(expression);

	return 0;
}
```

### 要点总结
- Interpret模式的应用场合是Interpreter模式应用中的难点，只有满足“业务规则频繁变化，且类似的结构不断重复出现，并去容易抽象为语法规则的问题”才适合使用interpreter模式
- 使用Interpret模式来表示文法规则，从而可以使用面向对象技巧来方便地“扩展”文法。
- Interpreter模式比较适合简单的文法表示，对于复杂的文法表示，interpert模式会产生比较大的类层次结构，需要求助于语法分析生成器这样的标准工具。
- 老师：解析器模式其实今天不常用，如果问题复杂的话面向对象解析器是不够用的，性能、管理、调试都是问题。它比较适合简单的文法。

## 26 设计模式总结

### 一个目标
- **管理变化，提高复用！**

### 两种手段
- **分解 VS. 抽象**

### 八大原则
- 依赖倒置原则（DIP）
- 开放封闭原则（OCP）
- 单依职责原则（SRP）
- Liskov替换原则（LSP）
- 接口隔离原则（ISP）
- 优先使用对象组合，而不是继承
- 封装变化点
- 针对接口编程，而不是针对实现编程
- 总结：这些原则是相辅相成的，违背一个原则往往别的原则也会违背

### 重构记法
- 静态 -> 动态
- 早绑定 -> 晚绑定
- 继承 -> 组合
- 编译时依赖 -> 运行时依赖
- 紧耦合 -> 松耦合
- 
### 从封装变化角度对模式分类
- 组件协作
	- Template Method
	- Strategy
	- Observer / Event
- 单一职责
	- Decorator
	- Bridge
- 对象创建
	- Factory Method
	- Abstract Factory
	- Prototype
	- Builder（不常用）
- 对象性能
	- Singleton
	- Flyweight
- 接口隔离：
	- Facade
	- Proxy
	- Mediator（不常用）
	- Adapter
- 状态变化：
	- Memento（不常用）
	- State
- 数据结构
	- Composite
	- Iterator（不常用，被STL淘汰）
	- Chain of（不常用，被函数对象淘汰）
	- Responsibility（不常用）
- 行为变化
	- Command（不常用）
	- Visitor（不常用）
领域问题：
	- Interpreter（不常用）

### 关注变化点和稳定点
- 大多数情况下都是变化点和稳定点交错出现，很少有绝对稳定和绝对变化
- 设计模式最大的艺术就是把变化点和稳定点择出来，将其隔离。然后在变化点的地方应用设计模式

### 什么时候不用模式
- 代码可读性很差时
- 需求理解还很浅时
- 变化没有显现时
- 不是系统的关键依赖点
- 项目没有复用价值时
- 项目将要发布时
- 补充：不要为了装X而使用模式。看看马丁福勒的书

### 经验之谈
- 不要为模式而模式
- 关注抽象类&接口
- 理清变化点和稳定点
- 审视依赖关系
- 要有Framework和Application的区隔思维
- 良好的设计是演化的结果

### 设计模式成长之路
- “手中无剑，心中无剑”：见模式而不知
- “手中有剑，心中无剑”：可以识别模式，作为应用开发人员使用模式
- “手中有剑，心中有剑”：作为框架开发人员为应用设计某些模式
- “手中无剑，心中无剑”：忘掉模式，只有原则

### 老师再见，完结撒花^_^

















