## [UE4 C++ 入门（无参考项目）](https://www.bilibili.com/video/BV1RE411d7J8)

### P5 反射与垃圾回收
- 反射：通过类名去获取类以及类里的属性方法等
- 垃圾收集：原理就是指针计数，如果一直指针被引用，就不能被收集
- Unreal Header Tool（UHT）：建立反射以及开放蓝图的一些功能
- ".generated.h"是引擎自动生成的，用于帮助开放蓝图或者建立反射的一些功能

### P6 建立一个UObject
```C++
// MyObject.h
UCLASS(Blueprintable) // 使得C++类可以创建对应的蓝图 （蓝图化）
class AGUN_API UMyObject : public UObject
{
	GENERATED_BODY()

public:
	UMyObject();

	UPROPERTY(BlueprintReadWrite)
	float MyFloat;

	UFUNCTION(BlueprintCallable)
	void MyFunction();
};
```
- 只有父类是Actor类才能挂“组件”，才能拖到场景中。所以UObject是不能挂组件的。
- 暴露在蓝图中的变量默认可能看不见，需要在“可视选项（左侧的“眼睛”图标）”设置显示继承变量（Show Inherited Variables）

### P7 在蓝图中使用UObject
- 1.分类
```C++
// 分类（中文可能乱码）
// BlueprintReadOnly
UPROPERTY(BlueprintReadWrite, Category="My Variables")
float MyFloat;

UFUNCTION(BlueprintCallable, Category="My Functions")
void MyFunction();
```

- 2.打印日志
```C++
void UMyObject::MyFunction()
{
	UE_LOG(LogTemp, Warning, TEXT("This is a Warning"));
}
```
### P8 Actor
- Actor只有含有有“根组件”才能有一些基础的“变换”（位置、旋转、缩放）
- 在“根组件”的基础上附加一个“cube”，游戏运行时Actor就可以看到了。而且DefaultSecneRoot 和 Cube 有不同的坐标，它们之间的变换也存在父子关系。

### p9 场景空间

### p12 sweep
- 对碰撞的两个基本概念，一个是重叠，一个是阻挡
- 忽略重叠阻挡优先级从高到低。
- 两个都设为阻挡结果才为阻挡，一个为重叠结果就是重叠

### p13 局部和世界坐标系
- 世界坐标：地球；局部坐标：自己
- 坐标：上下、左右、前后

### p14力和扭矩
- 陀螺：力和扭矩 = 陀螺移动的力 + 陀螺旋转的力
- 约束旋转：x，y；就只会从z轴旋转
- 这些都是在考虑重力的前提下。如果没重力，直接位移好了，还搞什么力和扭矩。质量应该也考虑了

### p15数学函数
- actor的波动是由正弦函数来确定的


### p16删除c++类
- 略

### p17初识Pawn类
- 当游戏运行的时候，游戏模式会把默认的Pawn赋给这个玩家起点（Player Start）。也就是说默认的Pawn会出现在玩家起点的位置，代替这个玩家起点然后开始游戏。如果这个Pwan身上有镜头的话，我们就拥有了那个镜头的视野

### p18简单WASD移动

### p19镜头摇臂，自制移动组件










