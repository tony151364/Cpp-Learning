- [游戏开发之3D数学基础](https://www.bilibili.com/video/BV1ib411K7TK/)

## 重点
- [ ] 向量：点乘、叉乘、投影
- [ ] 矩阵：正交投影(投影矩阵）

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
### 1. 基本运算：
- 方阵
- 对角阵
- 单位阵
- 向量可以当作矩阵（行列向量）
- 转置:矩阵转置、向量转置

### 2. 乘法编程：
- 标量与矩阵相乘
- 向量与矩阵相乘：
	- 行向量要左乘：vABC
 	- 列向量要右乘：CBAv
 	- DirectX使用行向量
  	- OpenGL使用列向量  
- 矩阵与矩阵相乘：
	- 第一个矩阵决定行，第二个矩阵决定列
```C++
int main()
{
	// 矩阵与矩阵相乘
	Matrix3x3 a, b, c;
	a.m11 = 1, a.m12 = -5, a.m13 = 3;
	a.m21 = 0; a.m22 = -2, a.m23 = 6;
	a.m31 = 7; a.m32 = 2, a.m33 = -4;

	b.m11 = -8, b.m12 = 6, b.m13 = 1;
	b.m21 = 7; b.m22 = 0, b.m23 = -3;
	b.m31 = 2; b.m32 = 4, b.m33 = 5;
	
	c = a * b;  
	print_m(c);

	a *= b;
	print_m(a);

	// 矩阵与向量相乘，课程使用行向量
	Matrix3x3 m;
	Vector3 v(3, -1, 4);
	m.m11 = -2, m.m12 = 0, m.m13 = 3;
	m.m21 = 5, m.m22 = 7, m.m23 = -6;
	m.m31 = 1; m.m32 = -4, m.m33 = 2;

	Vector3 r = v * m;  
	print_v(r);

	v *= m;
	print_v(v);

	return 0;
}
```

### 3. 矩阵和线性变换：
- 旋转（后面课程会用欧拉角来计算3D中绕任意轴旋转的矩阵）
```C++
int main()
{
	// 旋转
	Matrix3x3 M;
	Vector3 a(10, 0, 0), b;

	M.SetRotation(3, kPIOver2); // 绕Z轴，旋转90°
	print_m(M);

	b = a * M;
	print_v(b);

	M.SetRotation(3, kPI); // 绕Z轴，旋转180°
	print_m(M);

	b = a * M;
	print_v(b);

	M.SetRotation(1, -22 * kPI / 180); // 绕X轴，旋转-22°
	print_m(M);

	M.SetRotation(2, 30 * kPI / 180); // 绕Y轴，旋转-22°
	print_m(M);

	return 0;
}
```
- 缩放
- 投影：正交投影，向量垂直于投影平面。投影矩阵
```C++
int main()
{
	// 缩放
	Vector3 a(10, 20, 30), b;
	print_v(a);

	Matrix3x3 M;
	M.SetupScale(Vector3(1, 2, 3));
	print_m(M);

	b = a * M;  // 对向量a缩放，得到向量b
	print_v(b);

	// 投影
	Vector3 n(0, 0, 1);  // 垂直于xy平面的向量
	M.SetProjection(n);  // 构造一个投影矩阵
	print_m(M);

	// 把a投影到xy平面
	b = a * M;
	print_v(b);
	return 0;
}
```
- 镜像：就像旋转了180°一样；镜像矩阵；2D平面做镜像比较简单，3D较为复杂
- 切变

```C++
// Matrix3X3.h
```

```C++
// Matrix3X3.cpp
```


