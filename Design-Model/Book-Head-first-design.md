## 1.欢迎来到设计模式世界：设计模式入门
- 设计始终要面对的问题，也是很重要的问题就是“改变”。减少更改，使软件有兼容性，总是要考虑的。
- 软件开发不变的真理就是“变化”，人生也是如此呐。

### 1.1把问题归零
- 把变化的部分取出并“封装”起来，好让其他部分不会受到影响。
- **设计原则一：找出应用中可能需要变化的之处，把它们独立出来，不要和那些不需要变化的代码混在一起。**
- 另一种理解：把会变化的部分取出并封装起来，以便以后可以轻易的改动或扩充此部分，而不影响不需要变化的部分
- 这样的概念很简单，几乎是每个设计模式背后的精神所在。所有的模式提供了一套方法让“系统中的某些部分改变不会影响其他部分”

### 1.2 分开变化的和不会变化的部分

### 1.3 设计鸭子的行为
- 换句话说，我们应该在鸭子类中包含设定行为的方法，这样就可以在“运行时”动态地“改变”绿头鸭的飞行行为。
- **设计原则二：针对接口编程，而不是针对实现编程**
	- “针对接口编程”真正的意思是，**“针对超类型（supertype）编程”**
	- “针对接口编程”关键就在多态。利用多态，程序可以针对超类型编程，执行时会根据实际情况执行到真正的行为。
	- “针对超类型（supertype）编程”这句话，可以更准确的说成”变量的声明类型应该是是超类型，通常是一个抽象类或者是一个接口。
	- 说白了就是利用抽象类的多态性质，李建忠老师讲过这个。运行时直接执行具体的实现。
	
### 1.4 问答
- 通常设计系统时，就应预先考虑到有哪些地方未来可能需要变化，提前在代码中加入这些弹性。你就会发现，原则与模式可以应用在软件开发生命周期的任何阶段。

- Duck是不变的部分，因为我们从Duck的继承结构中删除了变化的部分，原先的问题都解决了。而且继承可以让衍生的特定类具有Duck共同的属性和方法。

- 在这个例子中“东西”碰巧是行为。行为也会有状态和方法。翅膀拍的次数，最大高度。

### 1.5 “有一个”（has-a） 可能比 “是一个”（is-a）更好
- **设计原则三：多用组合，少用继承**
- 使用组合建立系统具有很大的弹性，不仅可将算法族封装成类，更可以**“在运行时动态改变行为”**，只要组合的行为对象符合正确的接口标准即可

- 大师：继承有它的问题，还有一些其他的方式可以达到复用

- **策略模式（Strategy Pattern）**：定义了算法族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化独立于使用算法的客户

### 1.6 在附近餐厅中无意间听到……
- 设计模式可以把你的思考架构的层次提高到模式层面，而不是仅停留在琐碎的对象上

### 1.7 共享模式词汇的威力

### 1.8 我如何使用设计模式
- 我们想将应用组成成容易了解、容易维护、具有弹性的架构，所以需要设计模式。

- 设计模式是比库的等级更高的东西。库的设计也会用到设计模式。

- 一些构造OO系统的隐含经验被收集了起来，就成了一群“设计模式”

- **请记住**：知道抽象、继承、多态这些概念，并不会马上让你变成好的面向对象设计者。设计大师关心的是建立弹性的设计，可以维护，可以**应对变化**。

- **找不到模式**：一些面向对象原则适用于所有模式。当你无法找到适当的设计模式解决问题时，采用**这些原则（上面这些原则）**可以帮助你。

### 1.9 设计工具箱内的工具
- OO基础
	- 抽象
	- 封装
	- 多态
	- 继承
- OO原则
	- 封装变化
	- 多用组合，少用继承
	- 针对接口编程，不针对实现编程
	
- **阅读这本书时，时时刻刻要思考着：模式如何仰赖基础与原则**

- 良好的OO设计必须具备三个特性：可复用、可扩充、可维护
- 模式不是被发明，而是被发现

- 大多数模式都允许系统局部改变独立于其他模式

- 模式让开发人员之间有共享语言，能够最大化沟通价值

## 2 让你的对象知悉现状：观察者模式（Observer）模式
### 2.1 出版者 + 订阅者 = 观察者模式
- 出版社 -> "主题（Subject）"
- 订阅者 -> "观察者（Observer）"

### 2.2 定义观察者模式
- **观察者模式：定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，他的所有依赖者都会收到通知并自动更新**

- 稍后你会看到，实现观察者模式的方法不只一种，但是以包含Subject与Observer接口的类设计的做法最常见

### 2.3定义观察者模式：类图
- 利用观察者模式，主题是具有状态的对象，并且可以控制这些状态。也就是说，有“一个”具有状态的主题。另一方面，观察者使用这些状态，虽然这些状态并不属于他们。有许多的观察者，依赖主题来告诉他们状态何时改变了。这就产生了一个关系：“一个”主题对“多个”观察者
- 因为主题是真正拥有数据的人，观察者是主题的依赖者，在数据变化时更新，这样比起让许多对象控制一份数据来，可以得到更干净的OO设计
- 这就是主题接口，对象使用此接口注册为观察者，或者把自己从观察者中删除。许多观察者，潜在的观察者，具体的观察者 

### 2.4 松耦合的威力
- 当两个对象之间松耦合，它们依然可以交互，但是不太清楚彼此的细节。
- 观察者模式提供了一种对象设计，让主题和观察者之间松耦合。

- 假如我们有个新的具体类需要当观察者，我们不需要为了兼容新类型而修改主题的代码，所有要做的就是在新的类里实现此观察者接口，然后注册为观察者即可
- **设计原则四：为了交互对象之间的松耦合设计而努力**
- 松耦合的设计之所以能让我们建立有弹性的OO系统，能够应对变化，是因为对象之间的互相依赖降到了最低

### 2.5 实现气象站
```java
public interface Subject {
	// 这两个方法都需要一个观察者作为变量，该观察者是用来注册或被删除的
	public void registerObserver(Observer o);
	public void removeObserver(Observer o);
	
	// 当主题状态改变时，这个方法会被调用，以通知所有的观察者
	public void notifyObservers();
}

public interface Observer {
	/*
	当气象观测值改变时，主题会把这些状态值当做方法的参数，传送给观察者。
	所有的观察者都必须实现update()方法，以实现观察者接口。在这里，我们按照Mary和Sue的想法把观测值传入观察者当中。
	*/
	public void update(float temp, float humidity, float pressure);
}

// DisplayElement接口只包含了一个方法，当布告板需要显示时，调用此方法。
public interface DisplayElement {
	public void display();  
}
```

### 2.6 在WeatherData中实现主题接口
```java
public class WeatherData implements Subject {
	// 我们加上一个ArrayList来记录观察者，此ArrayList是在构造器中建立的
	private ArrayList observers;
	private float temperature;
	private float humidity;
	private float pressure;
	
	public WeatherData() {
		observers = new ArrayList();
	}
	
	// 这部分是Subject接口的实现
	public void registerObserver(Observer o) {
		observers.add(o);  // 当注册观察者时，我们只要把它加到ArrayList的后面即可。
	}

	public void removeObserver (Observer o) {
		int i = observers.indexOf(o);
		if (i >= 0) {  // 同样地，当观察者想取消注册，我们把它从ArrayList中删除即可。
			observers.remove(i);
		}
	}
	
	public void notifyObservers() {  
		for (int i = 0; i < observers.size(); i++) {
			Observer observer = (Observer)observers.get(i);
			observer.update(temperature, humidity, pressure);
		} // 有趣的地方来了！在这里，我们把状态告诉每一个观察者。因为观察者都实现了update(), 所以我们知道如何通知它们。
	}
	
	// 当从气象站得到观测值时，我们通知观察者。
	public void measurementsChanged() {
		notifyObservers();
	}
	
	public void setMeasurements(float temperature, float humidity, float pressure) {
		this.temperature = temperature;
		this.humidity = humidity;
		this.pressure = pressure;
		measurementsChanged();
	}
	
	// WeatherData的其他方法
}
```

### 2.7 现在，我们来建立布告板吧！
- 气象站订购了三个布告板：目前状况布告板、统计布告板和预测布告板
- 先看目前状况的布告板，你可以在代码目录中找到另外两个布告板的源代码

```java
// 此布告板实现了Observer接口，所以可以从WeatherData对象中获得改变。
// 它也实现了DisplayElement接口，因为我们的API规定所有的布告板必须实现此接口
public class CurrentConditionsDisplay implements Observer, DisplayElement {
	private float temperature；
	private float humidity;
	private Subject weatherData;
	
	// 构造器需要weatherData对象（也就是主题）作为注册之用。
	public CurrentConditionsDisplay(Subject weatherData) {
		this.weatherData = weatherData;
		weatherData.registerObserver(this);
	}
	
	// 当update被调用时，我们把温度和湿度保存起来，然后调用display()
	public void update(float temperature, float humidity, float pressure) {
		this.temperature = temperature;
		this.humidity = humidity;
		display();
	}
	
	// display()方法就只是把最近的温度和湿度显示出来
	public void display() {
		System.out.println("Current conditions: " + temperature 
		+ "F degrees and" + humidity + "% humidity");
	}
}
```
- Subject的引用在构造之后就用不到了。但是如果想取消注册，如果已经有了对Subject会比较方便
- [x] 一个疑问（p56)：为什么WeatherData不直接指向观察者Observer，而是指向Subject？？？因为WeatherData的作用就是对主题功能的具体实现

### 2.8启动气象站
```C++
public class WeatherStation {
	public static void main(String[] args) {
		WeatherData weatherData = new WeatherData();
		
		// 建立三个布告板
		CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
		StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
		ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
		
		// 模拟气象测量
		weatherData.setMeasurements(80, 65, 30.4f);
		weatherData.setMeasurements(82, 70, 29.2f);
		weatherData.setMeasurements(78, 90, 29.2f);
	}
}
```

### 2.9 Java内置的观察者模式如何运作
- setChangeed()方法用来标记状态已经改变的事实，好让notifyObserver()知道当它被调用时应该更新观察者。如果调用notifyObserver()之前没有先调用setChanged(),观察者就不会被通知。
- 举的这个例子很好说明。（很开心大学时候学了Java，不然现在的我看见Java代码肯定会恐惧的。很多经典的书都是用Java作为代码，所以感觉开启了很多扇大门。）

### 2.10 代码站
- 不要依赖于观察者被通知的次序

### 2.11 java.util.Observable的黑暗面
- Observable是一个类，而Java又不支持多继承，想具有多个类的行为就会陷入“两难”
- Observable违背了第二个原则：“多用组合，少用继承”

### 2.12 设计箱内的工具
- **观察者模式：定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，他的所有依赖者都会收到通知并自动更新**
- 观察者模式以松耦合方式在一系列对象之间沟通状态。我们目前还没看到观察者模式的代表人物——MVC，以后就会看到了。
- 观察者和可观察者之间用松耦合方式结合（losecoupling），可观察者不知道观察者的细节，只知道观察者实现了观察者接口。
- 使用此模式是，你可从被观察者处push或pull数据，然而push的方式被认为是正确的


## 3 装饰者模式
- 你将学到如何使用对象组和的方式，做到在运行时装饰类。

## 3.1 大师和门徒
- 一旦你熟悉了装饰的技巧，你将能够在不修改任何底层代码的情况下，给你的（或别人的）对象赋予新的职责。
- 继承是编译时静态决定，组合则可以在运行时动态扩展。我们可以利用此技巧把多个新职责，甚至是设计超类时还没想到的职责加在对象上。而且，可以不用修改原来的代码。

- 通过动态地组合对象，可以写新的代码添加新功能，而无需修改现有代码。既然没有修改现有代码，那么引进bug或产生意外副作用的机会将大幅度减少。

### 3.2 开放-封闭原则

- ** 设计原则五：类应该对扩展开放，对修改关闭**
- 这样的设计具有弹性可以应对改变，可以接受新的功能应对改变的需求
- 遵循开放-封闭原则，通常会引入新的抽象层次，增加代码的复杂度。你需要把注意力集中在设计中最有可能改变的地方，然后应用开放-封闭原则。

### 3.3 目前所知道的一切...
- 装饰者和被装饰对象有相同的超类型
- 你可以用一个或者多个装饰者包装一个对象
- 既然装饰者和被装饰对象有相同的超类型，所以在任何需要原始对象（被包装的）的场合，可以用装饰过的对象代替它
- *装饰者可以在所委托被装饰者的行为之前/之后加上自己的行为，以达到特定的目的*
- **装饰器模式：动态地将责任附加到对象上。若要扩展功能，装饰者提供了比继承更有弹性的替代方案。**

### 3.4 办公室隔间对话
- 星巴克咖啡例子用到了继承。这么做的重点在于，装饰者和被装饰者必须是一样的类型，也就是有共同的超类，这是相当关键的地方。在这里，我们利用继承达到“类型匹配”，而不用继承获得“行为”
- 因为装饰者必须能取代被装饰者，所以两者需要有相同的“接口”
- 行为则来自装饰者和基础组件，或与其他装饰者之间的组合关系。

### 3.5 写下星巴兹的代码
```java
// Beverage是一个抽象类，有两个方法：getDescription()及cost()
public abstract class Beverage {
	String description = "Unknown Beverage";
	
	// getDescription()已经在此实现了，但是cost()必须在子类中实现。
	public String getDescription() {
		return description;
	}
	
	public abstract double const();
}

// 让我们也来实现Condiment（调料）抽象类，也就是装饰者类吧：
public abstract class CondimentDecorator extends Beverage {
	
	// 所有的调料装饰者都必须重新实现getDescription()方法。稍后我们会解释为什么
	public abstract String getDescription();
}
```

### 3.6 写饮料的代码
```java
// 先从浓缩咖啡(Espresso)开始
public class Espresso extends Beverage {
	// 为了要设置饮料的描述，我们写了一个构造器。记住，description实例变量继承自Beverage
	public Espresso() {
		description = "Espresso";
	}
	
	public double cost() {
		return 1.99;
	}
}

public class HouseBlend extends Beverage {
	public HouseBlend() {
		description = "House Blend coffee";
	}
	
	public double cost() {
		return 0.89;
	}
}
```

### 3.7 写调料代码
```java
public class Mocha extends CondimentDecorator {
	Beverage beverage;
	
	/*
	要让Mocha能够引用一个Beverage，做法如下：
	（1）用一个实例变量记录饮料，也就是被装饰者
	（2）想办法让被装饰者（饮料）被记录到实例变量中。这里的做法是：把饮料当做构造器的参数，再由构造器将此饮料记录在实例变量中
	*/
	public Mocha(Beverage beverage){
		this.beverage = beverage;
	}
	
	/*
	我们希望叙述不只是描述饮料（例如“DarkRost”），而是完整地连调料都描述出来（例如“DarkRost, Mocha”）。
	所以首先利用委托的做法，得到一个叙述，然后在其后加上附加的叙述（例如“Mocha”）。
	*/
	public String getDescription() {
		return beverage.getDescription() + ", Mocha";
	}
	
	public double cost()
	{
		return .20 + beverage.cost();
	}
}
```

- 在下一页，我们会实例化一个饮料对象，然后用各种调料（装饰者）包装它。但是，在这么做之前，首先......

### 3.8 供应咖啡
```java
public class StarbuzzCoffee {

	public static void main(String args[]){
	
	// 订一被Espresso，不需要调料，打印出它的描述与价钱
	Beverage beverage = new Espresso();
	System.out.println(beverage.getDescription()
		+ " $" + beverage.cost());
		
	// 制造出一个DarkRoast对象
	Beverage beverage2 = new DarkRoast();
	// 用Mocha装饰它
	beverage2 = new Mocha(beverage2);
	// 用第二个Mocha装饰它
	beverage2 = new Mocha(beverage2);
	// 用Whip装饰它
	beverage2 = new Whip(beverage2);
	System.out.println(beverage2.getDescription()
		+ " $" + beverage2.cost());
		
	// 最后，再来一杯调料为豆浆、摩卡、奶泡的HouseBlend咖啡
	Beverage beverage3 = new HouseBlend();
	beverage3 = new Soy(beverage3);
	beverage3 = new Mocha(beverage3);
	beverage3 = new Whip(beverage3);
	System.out.println(beverage3.getDescription()
		+ " $" + beverage2.cost());
	}
}
```

### 3.9 问答
- 答1：的确是这样。如果你把代码写成依赖于具体的组件类型。那么装饰者就会导致程序出问题。只有在针对抽象组件类型编程时，才不会因为装饰者而受到影响。但是，如果的确针对特定的具体组件编程，就应该重新思考你的应用架构，以及装饰者是否合适。

### 3.10 模式访谈
- 问题1：如果在设计中加入大量的小类，偶尔会导致别人不容易了解我的设计方式。
- [ ] 问题2：有些代码依赖特殊类型就会出现问题
- 问题3：采用装饰者在实例化组件时，将增加代码的复杂度。一旦使用装饰者模式，不只需要实例化组件，还要把此组件包装进装饰者中，天晓得有几个！

### 3.11 设计箱内的工具
- **自己的总结：多态可以体现变化性，继承可以体现扩展性**
- 1. 继承属于扩展形式之一，但不见得是达到弹性设计的最佳方案。
- 2. 在我们的设计中，应该允许行为可以被扩展，而无须修改现有的代码
-[ ] 3. 组合和委托可用于在运动时动态地加上新的行为
- 4. 除了继承，装饰者模式也可以让我们扩展行为
- 5. 装饰者模式意味着一群装饰者类，这些类用来包装具体组件
- 6. 装饰者类反映出被装饰者的组件类型（事实上，它们具有相同的类型，都经过接口或继承实现）
- 7. 装饰者可以在被装饰者的行为前面与/或后面加上自己的行为，甚至将整个取代掉，而达到特定的目的。
- 8. 你可以用无数个装饰者包装一个组件。（装饰者模式就是**套娃**）
- 9. 装饰者一般对组件的客户是透明的，除非客户程序依赖于组件的具体类型。（**这个具体类型说了这么多我也没明白是啥意思，也没举个例子，整个代码**）
- 10. 装饰者会导致设计中出现许多小对象，如果过度使用，会让程序变得很复杂

## 4 工厂模式
- 除了使用new运算符之外，还有更多制造对象的方法。你将了解到实例化这个活动不应该总是公开地进行，也会认识到初始化经常造成“耦合”问题。

### 4.1 思考new
- 每次使用new都是在对具体编程。如果代码绑定着具体类会导致代码更脆弱，更缺乏弹性。
- 针对接口编程，可以隔离掉以后系统可能发生的一大堆改变。为什么呢？如果代码是针对接口而写，那么通过多态，它可以与任何新类实现该接口。但是，当代码使用大量的具体类时，等于自找麻烦。

- 如何将实例化具体类的代码从应用中抽离出来，或者封装起来，使它们不会干扰应用的其他部分。

- 工厂可能有许多客户

### 4.2 定义简单工厂
- 简单工厂不是一种模式，更像是一种习惯。
- “实现一个接口” 泛指 “实现某个超类型”（可以是类或接口的某个方法）








