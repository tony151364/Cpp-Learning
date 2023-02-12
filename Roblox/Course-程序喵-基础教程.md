## 7. Replication复制的概念
- 所有客户端的数据都是从服务器复制而来（在这个项目中）
- 在服务器新建一个部件，就会通过网络消息复制到客户端。这种对象数据在服务器和客户端之间同步的行为就叫做replication（复制）
- 反之，大部分客户端的修改都不会同步到服务器。这就是对客户端的过滤机制，原因在于客户端不总是可信的（脚本、外挂、换皮肤）
- 由于这些原因，通常我们都会把游戏的主要逻辑放在服务器运行。客户端主要处理玩家的输入

## 8 脚本基础 变量+时间回调
- script专门运行在服务器。放置在workspace和ServerScriptService里才会自动执行
- 不想自动运行可以放在ServerStoage里，这个专门用来存放预先制作好的对象

## 9.冒号和句号
- 用冒号时，会把表传到参数里，少写个参数，更方便了。
- 冒号走的就是对象了

## 19.脚本基础 不同脚本之间的数据
- Module Script只能在被别的脚本调用时才执行

## 36 CFrame介绍
- 模型有很多顶点，每个顶点都是一个位置。这些位置是在建模软件里定义好的，所以放在世界坐标中需要进行一个转换。从本地坐标转换到世界坐标通常包括了旋转、缩放和位移。
- 缩放由物体的Size属性定义，其他的旋转和位移则是由CFrame来定义的。
- 位移部分和部件的Position属性是相等的，是一个Vector3类型的变量
- 旋转部分是由一个3x3的旋转矩阵来表示的。
- 知道3个轴的方向就知道物体是怎么转的了。三个坐标轴向量刚好是9个值。旋转矩阵就是由这9个值得到的，也就是xyz轴的正方向的向量。
- Y轴正方向也叫UpVector、X轴正方向也叫RightVector。Z轴负方向叫做LookVector
- 物体的旋转都是由CFrame来表示的，如果你设置了物体Orientation属性，它会自动被转换为CFrame的旋转矩阵。如果你设置了新的CFrame，Orientation的值也会跟着变化 
- 但是由于CFrame有9个值，Orientation只有3个值，所以二者并非一一对应。有时候不同的两个Orientation是同一个旋转方向。也就是转换结果不唯一。
- Orientation只有在表示单一轴旋转的时候比较方便。如果旋转方向比较任意，你很难计算这三个旋转值的。所以游戏开放的时候更多的是使用坐标系来表示旋转。
- 四元数只使用4个值来表示旋转，它的好处是可以实现任意两个旋转方向的线性插值
```lua
print("Hello world!")

local Ball = script.Parent
local Burger = Ball.Parent

local rotation = 0
while wait() do
	rotation = rotation + 0.02  -- 循环增加旋转值
	
	local objectSpaceRotationCFrame = CFrame.fromOrientation(0, rotation, 0)
	local look = objectSpaceRotationCFrame.LookVector
	local objectSpaceCFrame = objectSpaceRotationCFrame + look * 5  -- 朝look方向位移5个格子
	local worldSpaceFrame = Burger.CFrame * objectSpaceCFrame  -- 本地坐标转换为世界坐标
	Ball.CFrame = worldSpaceFrame -- 球的位置
end
```
