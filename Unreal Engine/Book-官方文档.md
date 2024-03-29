## 一、理解基础概念

### 1.查找帮助和答案

- 编译器内部帮助
- 富文本提示(Rich Tooltip)
  - hold(Ctrl + Alt)for more
  - see full documentation
- 帮助图标(Help Icon)
- 文档Actor
- 帮助(Help)菜单
  - 当你位于新的编辑窗口中，并且不确定该窗口的用途或使用方法时，你可以点击F1来获取帮助
- 编辑器内部教程
- 内容示例
  - [ ] 可以研究下
- 外部帮助
  - 文档
  - 问答中心
  - 论坛
  - 
### 2.虚幻引擎4术语

#### 2.1 项目
- 每个项目都有一个与之对应的 ``` .uproject ```文件。 ``` .uproject ``` 文件是你创建、打开或保存项目必须用到的文件。

#### 2.2 蓝图
- **蓝图可视化脚本(Blueprint Visual Scripting)** 系统（或缩写**蓝图（Blueprints）**）是一种功能齐全的游戏脚本系统，它允许你在Unreal Editor中通过基于节点的界面来创建游戏元素。和许多常见脚本语言一样，你可以用它在引擎中定义面向对象的类或object。在使用UE4时，你会发现使用蓝图定义的类一般也统称为蓝图

#### 2.3 对象
- 在虚幻引擎中，最基本的类叫做Object。虚幻引擎中的大多数类都继承自Object（或从中获取部分功能）
- 在C++中， ``` UObject ``` 是所有Object的基类，包括各类功能，诸如垃圾回收、通过元数据（UProperty）将变量公开给编辑器

#### 2.4 类

#### 2.5 Actor
- 所有可以放入关卡的对象都是Actor，比如摄像机、静态网格体、玩家起始位置。Actor支持三维变换，例如平移、旋转和缩放。你可以通过游戏逻辑代码（C++或蓝图）创建（生成）或销毁Actor。
- 在C++中，AActor是所有Actor的基类。

#### 2.6 类型转换
- 类型转换(Casting)本质上是获取某个特定Actor（或类），然后将它视为另一种类型处理。类型转换可以成功，也可以失败。如果成功，你就能访问目标Actor的特有函数和功能。

#### 2.7 组件
- 组件（component）是可以添加到Actor上的一项功能


#### 2.8 Pawn
- Pawn是Actor的子类，它可以充当游戏中的化身或人物（例如游戏中的角色）。Pawn可以由玩家控制，也可以有游戏AI控制并以非玩家角色（NPC）的形式存在于游戏中。

#### 2.9 角色
- 角色（Character）是Pawn Actor的子类，旨在用作玩家角色。

#### 2.10 玩家控制器
- 玩家控制器(Player Controller)会获取游戏中玩家的输入信息……。
- 相关的C++类是 ``` PlayerController ```

#### 2.11 AI控制器
- 默认情况下，Pawn和角色最终都会由基本的AI控制器控制，除非他们被指定通过玩家控制器控制，或被告知不需要为它们自己创建AI控制器。
- 关联的C++类是 ``` AIController ```

#### 2.12 玩家状态（Player State）
- 玩家状态能包括的玩家信息包括：
  - 名称
  - 当前关卡
  - 生命值
  - 分数
- 在多人游戏中，所有电脑都保存着所有玩家的玩家状态，并且玩家状态可以从数据到服务器复制到客户端以同步。这点与玩家控制器不同，它只会保存在玩家所在的客户端上。


### 3 Actor和几何体

#### 3.1 Actor移动性（Mobility）

### 4 组件

### 5 关卡


## 三 编程

## 四 Gameplay框架
- Gameplay类（如**游戏模式、游戏状态 和 玩家状态**）可用于设置游戏规则，并追踪游戏和玩家的进展情况

- 控制器（Controller）
  控制器是负责定向Pawn的Actor。它们通常有两种风格：AI控制器和玩家控制器。一个控制器可以"拥有"一个Pawn来控制它。

- 玩家控制器
  PlayerController(玩家控制器)是Pawn和控制Pawn的人类玩家之间的界面。玩家控制器基本上代表人类玩家的意愿。

- AI控制器
  AI控制器和听起来一样；可以控制Pawn的模拟"意愿"。

