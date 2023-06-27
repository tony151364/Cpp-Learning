- [游戏开发之3D数学基础](https://www.bilibili.com/video/BV1ib411K7TK/)

## 1. 坐标系
### 2D
- 2D有8种方案
 
### 3D
- 3D有48种方案（左右手坐标系之和）
- 右手坐标系：OpenGL
- 左手坐标系：DirectX
- 东，+X
- 北，+Z

## 2. 多坐标系
- 在数学上，从一个坐标系变换到另一个坐标系叫做**坐标变换**
- 摄像机坐标系 <--> 世界坐标
- 世界坐标 <- 移动 -> 惯性坐标系 <- 旋转 -> 物体坐标系；先旋转和先移动都是可以的

## 3. 向量
- 1维向量叫做标量
- 向量的几何意义：大小和方向；位移

## 4. 向量运算
- 零向量
- 负向量
- 向量大小、长度、模
- 标量与向量的乘除法（改变原向量的大小，反方向）
- 单位向量（标准化向量）
- 向量的加法和减法
- 距离公式
- 向量点乘
- 向量叉乘
- 向量投影
```C++
#ifndef _VECTOR3_H_
#define _VECTOR3_H_

#include <cmath>

class Vector3
{
public:
	float x, y, z;

	Vector3() {};
	Vector3(const Vector3& a) : x(a.x), y(a.y), z(a.z) {}
	Vector3(float nx, float ny, float nz) : x(nx), y(ny), z(nz) {}

	void zero() { x = y = z = 0.0f; }
	Vector3 operator -() const { return Vector3(-x, -y, -z); }
};

inline float vectorMag(const Vector3& a)
{
	return sqrt(a.x * a.x + a.y * a.y + a.z * a.z);
}

#endif  // _VECTOR3_H_
```
```C++
#include <iostream>
#include "Vector3.h"

using namespace std;

void print_v(const Vector3& v)
{
	cout << "[ " << v.x << ", " << v.y << ", " << v.z << " ]" << endl;
}

int main()
{
	cout << "hello vector" << endl;

	Vector3 v1(10, 20, 30);
	print_v(v1);

	Vector3 v2(v1);
	print_v(v2);

	Vector3 v3(-v1);
	print_v(v3);

	v2.zero();
	print_v(v2);

	Vector3 v4( 5, -4, 7 );
	std::cout << vectorMag(v4) << std::endl;;
	return 0;
}
```
