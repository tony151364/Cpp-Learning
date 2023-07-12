[课程地址](https://www.bilibili.com/video/BV1dT4y1N7de)
[官方文档](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/Networking/)
# 01 内容概要
### 1.1前言:明确我们所讲的网络
- 实时（相对于弱联网）
- 不涉及Http以及数据库（注册登录增删改查等）
- 有“Session（会话）”概念。（CS里面的房间就是一个会话。弹幕说：房间不是会话，是一个频道。会话是一个房间客户端和服务器的交互）
- 适用于LAN和WAN
### 1.2 内容概要
- UE4网络架构
- Replication网络复制（不是传统的复制，是服务器到客户端的复制）
- Ownership所有权
- Actor Role（三种角色）
- RPC（Remote Procedure Cal）
- 影响网络游戏流畅度的因素
- 相关性(优化）
- 创建和加入Session
- Travelling
- 项目实例
- 打包和测试

# 02 网络构架
### 2.1 Server-Client构架
1. 一个服务器，一个或多个客户端（服务器是数据中心）
2. 不能信任客户端，所有重要信息都需要通过服务器验证（防止玩家作弊，必须服务器验证）
3. Listen Server (等待其他玩家加入） & Dedicated Server (专有服务器，没有玩家，只是用来计算，所有玩家作为它的客户端加入进来），Dedicated Server官方文档有，逻辑与Listen Server逻辑都一样。
4. 我们是客户端时，是在操纵本地角色还是远程角色？（replicate movement）
### 2.2 网络信息传递的主要方式
- Replication （单向的，只能从服务端复制到客户端）
- Rep_Notify（可以与上面的归为一类）
- RPC（多种方式）

# 03 Actor Replication
### 3.1 REPLICATION的含义
- 笼统来说，表示信息从服务端同步到客户端（单向）
- Actor及其派生类才有Replication的能力
### 3.2 REPLICATION的类型
- Actor Replication
- Property Replication
- Component Replication
### 3.2 Actor Replication的两层意义
1. 服务端生成，客户端也跟着生成（在服务端生成一个replicate对象）
2. 当前Actor的所有属性复制、组件复制、RPC的总开关
### 3.3 开启Actor REPLICATION
- 蓝图：勾选“Replicates”
- C++：bReplicates = true
- 角色的运动是在服务端检测的，客户端只是一个假象

# 04 Property Replication
- 蓝图节点``` Switch Has Authority  ``` 中Authority是服务端，Remote是客户端
### 4.1 属性复制的设置方法
- 蓝图：
  - 1.Replication 设置为Replicated
- C++：
  - 1.属性前面加上UPROPERTY(Replicated)
  - 2.cpp 文件中，在GetLifetimeReplicatedProps函数中添加：DOREPLIFETIME(类名称，变量名)，不需要声明该函数
```C++
// .h
UPROPERTY(Replicated)
float Health = 200;

// .cpp
#include "Net/UnrealNetwork.h"
void ATestCPPActor::GetLifetimeReplicatedProps(TArray< FLifetimeProperty >& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME(ATestCPPActor, Health);
}
```

# 05 Rep_Notify（复制通知）
- [ ] 蓝图和C++都只打印服务端，暂时不知道哪里出了问题

### 意义：
- 如果一个变量设置为Rep_Notify，当该变量发生复制时，服务端和收到该值的客户端都可以调用一个自定义的函数。
- 注意：C++版本略有区别，仅在客户端调用函数
 
### 设置方法：
- 蓝图：设置为RepNotify即自动生成
- C++：UPROPERTY(ReplicatedUsing=xxx)
```C++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyActor.generated.h"

UCLASS()
class UE4_NETWORK_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties
	AMyActor();

protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;

public:	
	// Called every frame
	virtual void Tick(float DeltaTime) override;

	void GetLifetimeReplicatedProps(TArray< FLifetimeProperty >& OutLifetimeProps) const;

	UPROPERTY(ReplicatedUsing= OnRep_Armor)
	float Armor = 0.0f;

	UFUNCTION()
	void OnRep_Armor();
};
```
```C++
// Fill out your copyright notice in the Description page of Project Settings.


#include "MyActor.h"
#include "Net/UnrealNetwork.h"

// Sets default values
AMyActor::AMyActor()
{
 	// Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	PrimaryActorTick.bCanEverTick = true;
	bReplicates = true;
	SetRootComponent(CreateDefaultSubobject<USceneComponent>(TEXT("Root")));
}

// Called when the game starts or when spawned
void AMyActor::BeginPlay()
{
	Super::BeginPlay();
	
}

// Called every frame
void AMyActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
	if (HasAuthority())
	{
		Armor = FMath::Rand();
	}
}

void AMyActor::GetLifetimeReplicatedProps(TArray< FLifetimeProperty >& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME(AMyActor, Armor);
}

void AMyActor::OnRep_Armor()
{
	// Server
	if (HasAuthority())
	{
		UE_LOG(LogTemp, Warning, TEXT("Server Armor:%f"), Armor);
	}
	// Client
	else
	{
		UE_LOG(LogTemp, Warning, TEXT("Client Armor:%f"), Armor);
	}
}


```
- 弹幕问：为什么屏幕上客户端和服务器的都打印？
- 弹幕答：因为PIE所有窗口运行在同一个Process里
- [ ] 暂时还不是太懂

# 06 Ownership 所有权
- [ ] Connection -> PlayerCOntroller -> Pawn
- 这个关系链就叫做Ownership所有权。场景中的建筑、NPC等是没有所有权的
- 官方文档：用Ownership来确定那些连接会获取Actor的更新。

### 6.1 为什么Ownership很重要

#### 6.1.1 RPC需要确定那个客户端将执行运行于客户端的RPC
- [ ] bOnlyRelevantToOwner设置为true的Actor，只有拥有此actor的连接才会接收这个actor的更新。默认情况下，所有Play二Controller都设置了此标志。正因如此，客户端才只会收到他们拥有的PlayerController的更新。这样做的最主要原因是防止玩家作弊和提高效率。
- 例子：当前玩家的弹夹中的弹药数量只有自己知道，其他玩家不知道也不能知道。

#### 6.1.2 Actor复制与连接相关性
- DOREPLIFETIME(AMyActor, Armor); 实际上有三个参数，第三个参数是条件复制。比较高阶的内容

#### 6.1.3 在涉及所有者时的Actor属性复制条件

### 6.2 如何设置/改变/获取Ownership
- SpawnActor(SpawnParameters中有Owner)，加入到关系链
- SetOwner()；C++在Actor里面
- Possess(OnPossess > PossessedBy > SetOwner), UnPossess；这个归根结底还是用的SetOwner
	- Possess用于一个PlayerController去拥有一个Pawn，UnProcess是取消拥有
- GetOwner()；C++在Actor里面
- 注：这四种蓝图和C++都有接口

# 07 Actor Role
- Role指的是在网络复制中，Actor可能扮演的三种角色：Authority、Simulated Proxy（模拟代理）、Autonomous Proxy（自主代理）
- Server: 只有Authority
- Client：只有Simulated和Autonomous

















