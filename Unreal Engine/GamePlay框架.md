- [UE甜筒](https://www.bilibili.com/video/BV12Y411a75k?spm_id_from=333.1007.top_right_bar_window_history.content.click)
- [大钊](https://www.zhihu.com/people/fjz13)

## [一、GamePlay框架](https://www.bilibili.com/video/BV1Gr4y1D77u)

### GamePlay框架
- GameMode（游戏模式）
- GameState（游戏状态）
- DefaultPawn（玩家）
- PlayState（玩家状态）
- PlayConroller（玩家控制器）
- HUD（用户界面）

### 1.GameMode 
- 主要负责管理游戏的规则：
  - 出现的玩家和观众数量，以及允许的玩家和观众最大数量
  - 玩家进入游戏的方式，可包含选择生成地点和其它生成/重生成行为的规则
  - 游戏是否可以暂停，以及如何处理游戏暂停
  - 关卡之间的过渡，包括是否以动画模式开场

- GM的主要任务就是构建和实现游戏的规则，并将游戏中的其他角色进行注册:例如默认的玩家，HUD类，玩家控制器，观众类，GameState类，PlayerState类
- 游戏规则及获胜条件这样的内容。它仅存在与服务器上。它一般在游戏过程中不会有太多数据改变，并且它一定不应该具有客户端需要的临时数据。

### 2.GameState
- GameState主要负责游戏中共享信息的记录，游戏的信息可以通过GameState进行存储和同步。包括：
  - 游戏已运行的时间（包括本地玩家加入前的运行时间）
  - 每个个体玩家加入游戏的时间和玩家的当前状态
  - 当前Game Mode的基类
  - 游戏是否已开始

- GameState是虚幻中为我们进行游戏信息记录的载体。包括像关联玩家的列表、分数、象棋游戏中的象棋位置或者在开放世界游戏中已经完成的任务列表。GameState存在于服务器和所有客户端上，可以自由地进行复制来保持同步

### 3.Default Pwan
- 默认玩家，不管任何游戏，都应当存在一个默认的玩家，这是肯定的！即使是最开放的游戏，也需要存在一个上帝时间进行游戏的指令发布。在虚幻中默认玩家扮演的就是这样的角色。
- **comment：** 因为游戏都是围绕人进行的。（至少有一个人在玩游戏吧）
- 注意：只有继承在Pawn的对象才能成为默认玩家。
- 默认玩家需要通过控制器进行控制，他们可以轻松地接受输入，并且可以执行各种各样的类似于玩家的动作。

### 4.Player Controller
- PlayerController是Actor的一种子类型，负责控制玩家使用的Pawn
- 控制器，是负责管理玩家的Actor类型对象。主要目的是将真实玩家的意愿进行收集，最后汇总转头给虚拟角色。这个过程是游戏中的玩家交互过程。
- PlayerController（玩家控制器）是Pawn和控制它的人类玩家间的接口。PlayerController本质上代表了人类玩家的意愿。

### 5.Player State
- PlayerState是游戏中的一个参与者（比如人类玩家或者模拟人类玩家的机器人）的状态。状态体现在计算机内部就是数据，这些数据有：
  - 玩家姓名
  - 分数
  - 多人在线竞技场这样的比赛中的级别
  - 或者夺旗模式游戏中玩家是否正在占领旗帜。 
- 所有玩家的PlayState在所有机器上都存在（这点是和PlayerController不同），并且可以自由地进行复制来保持进步。
- **注意：** 游戏数据和玩家数据是两个不同的数据点



### HUD(Head-up Display)
- 抬头显示或平视显示。就是游戏中的UI。
  - 生命值、魔法值、体力值，当前的武器，第一人称射击瞄准的准心。
- 现在虚幻中是用UMG（虚幻新的UI系统）来实现的，因为HUD（这里特指的是虚幻老的UI系统）学起来复杂而且并不是很好用。


## [二、中文直播 26期 | 虚幻引擎GamePlay框架理解与应用 | Epic 大钊 马骥](https://www.bilibili.com/video/BV1ED4y1D7Sf)
- [ ] 感觉虚幻至少做一个项目之后，再来看比较好。
- 什么逻辑写在哪里
  - 各自的逻辑
  - 从属交互关系
  - 如何表现一个系统的多方面
  - 那一些写法是不太好的
  - 常见的一些问题和误区

### 1.Actor-演员/个体
- 组成世界的单位个体，但**不是所有的Actor都看得见**
- 可互相嵌套
- 组件的容器
  - 根据不同的功能可以定义不同的Component，有一个通用的Component可以Add到不同的Actor
- Spawn & Tick
- 网络通信的通道：联机，同步对象以Actor为单位
- 派生出各种不同职责的Actor

### 2.Component-组件
- 组件是种能力
- 为Actor提供能力
- 能力一般跟业务逻辑无关
- Component是跨游戏的、Actor是跨关卡的
  - 比如某个功能，你觉得可以搬到另外一个游戏当中 
- Actor Component与Scene Component
  - Actor Component：不带有空间的关系，没有世界坐标。比如移动
  - Scene Component：有空间的关系
- Actor之间的嵌套也是一种能力，通过child Actor Component来嵌套的





