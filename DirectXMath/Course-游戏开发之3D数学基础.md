- [游戏开发之3D数学基础](https://www.bilibili.com/video/BV1ib411K7TK/)

## 重点
- [ ] 向量：点乘、叉乘、投影

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
- **向量点乘**
- **向量叉乘**
- 向量投影
```C++
// Vector3.h
#ifndef _VECTOR3_H_
#define _VECTOR3_H_

#include <cmath>

class Vector3
{
public:
	float x, y, z;

	Vector3() { one(); };
	Vector3(const Vector3& a) : x(a.x), y(a.y), z(a.z) {}
	Vector3(float nx, float ny, float nz) : x(nx), y(ny), z(nz) {}

	void zero() { x = y = z = 0.0f; }
	void one() { x = y = z = 1.f; }

	Vector3 operator+(const Vector3& v) const
	{
		return Vector3(x + v.x, y + v.y, z + v.z);
	}

	Vector3 operator -() const { return Vector3(-x, -y, -z); }

	Vector3 operator-(const Vector3& v) const
	{
		return Vector3(x - v.x, y - v.y, z - v.z);
	}

	Vector3 operator *(float k) const
	{
		return Vector3(x * k, y * k, z * k);
	}

	Vector3 friend operator *(float k, const Vector3& v)
	{
		return v * k;
	}

	Vector3 operator / (float k) const
	{
		float oneOverA = 1.0f / k;
		return Vector3(x * oneOverA, y * oneOverA, z * oneOverA);
	}

	Vector3 operator+=(const Vector3& v)
	{
		x += v.x, y += v.y, z += v.z;
		return *this;
	}

	Vector3 operator-=(const Vector3& v)
	{
		x -= v.x, y -= v.y, z -= v.z;
		return *this;
	}

	Vector3 operator *=(float k)
	{
		// method 1
		x *= k; y *= k; z *= k;
		return *this;  // 改变了自身的值

		// method 2
		// *this = *this * k;
	}

	Vector3 operator /= (float a)
	{
		float oneOverA = 1.0f / a;
		x *= oneOverA; y *= oneOverA; z *= oneOverA;
		return *this;
	}

	float vectorMag()
	{
		return sqrt(x * x + y * y + z * z);
	}

	void normalize()
	{
		float mag = vectorMag();
		if (mag <= 0.0f)
		{
			return;
		}

		auto oneOverMag = 1.0f / mag;
		x *= oneOverMag, y *= oneOverMag, z *= oneOverMag;
	}

	float friend distance(const Vector3& v1, const Vector3& v2)
	{
		return (v1 - v2).vectorMag();
	}

	// 点乘（內积）
	float friend operator*(const Vector3& v1, const Vector3& v2)
	{
		return v1.x * v2.x + v1.y * v2.y + v1.z * v2.z;
	}

	// 向量叉乘（外积）
	Vector3 friend MultiCross(const Vector3& v1, const Vector3& v2)
	{
		return Vector3(
			(v1.y * v2.z - v1.z * v2.y),
			(v1.z * v2.x - v1.x * v2.z),
			(v1.x * v2.y - v1.y * v2.x)
		);
	}
};

#endif  // _VECTOR3_H_
```
```C++
// main.cpp
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
	std::cout << v4.vectorMag() << std::endl;;

	Vector3 v5;
	v5 = v4 * -5;
	print_v(v5);

	v5.one();
	v5 *= -6;
	print_v(v5);

	Vector3 v6(4.7f, -6, 8);
	v6 = v6 / 2;
	print_v(v6);

	v6 /= 3;
	print_v(v6);

	Vector3 v7;
	v7 = 7 * v7;
	print_v(v7);

	Vector3 v8(-12, -5, 0);
	v8.normalize();
	print_v(v8);

	Vector3 a(1, 2, 3);
	Vector3 b(4, 5, 6);
	print_v(a + b);
	print_v(b - a);

	Vector3 x(4, 5, 0);
	Vector3 y(1, 5, 0);
	std::cout << distance(x, y) << std::endl;

	// 点乘
	Vector3 v9(3, -2, 7);
	Vector3 v10(0, 4, -1);
	float dp = v9 * v10;
	std::cout << "点乘：" << dp << std::endl;
	float angle = acos(dp / (v9.vectorMag() * v10.vectorMag())) * 180 / 3.14f;
	std::cout << "角度：angle= " << angle << std::endl;

	// 叉乘
	Vector3 v11(1, 3, 4);
	Vector3 v12(2, -5, 8);
	std::cout << "叉乘：";
	print_v(MultiCross(v11, v12));

	return 0;
}
```

## 5. 矩阵
- 方阵
- 对角阵
- 单位阵
- 向量可以当作矩阵（行列向量）
- 转置:矩阵转置、向量转置
- 标量与矩阵相乘
- 向量与矩阵相乘：行向量在左边、列向量在右边
- 矩阵与矩阵相乘：第一个矩阵决定行，第二个矩阵决定列
- 矩阵乘法公式
- 矩阵和线性变换：旋转、缩放、投影、镜像、切变

```C++
// Matrix3X3.h
```

```C++
// Matrix3X3.cpp
```

```C++
// main.cpp
```
