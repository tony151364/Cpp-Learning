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
### 5.1. 基本运算：
- 方阵
- 对角阵
- 单位阵
- 向量可以当作矩阵（行列向量）
- 转置:矩阵转置、向量转置

### 5.2. 乘法编程：
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

### 5.3. 矩阵和线性变换：
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
int main()
{
	// 镜像
	Vector3 a(10, 20, 30), b;
	Matrix3x3 M; 

	M.SetReflection(1);  // x
	b = a * M;
	print_v(b);

	M.SetReflection(2);  // y
	b = a * M;
	print_v(b);

	M.SetReflection(3);  // z
	b = a * M;
	print_v(b);

	// 3D空间任意方向进行镜像，需要用到较为复杂的镜像矩阵
	Vector3 n(0, 0, 1);
	M.SetReflection(n);  // 和M.SetReflection(3);是一样的
	b = a * M;
	print_v(b);

	// 切变
	M.SetupShear(1, 1, 2);
	b = a * M;
	print_v(b);
}
```

### 5.4. 矩阵的行列式
- 只有方阵才有行列式
- 任意的方阵中都存在一个标量，这个标量就是行列式的值
- 几何意义：2阶行列式的值是两个行向量对应的平行四边形的面积。3阶行列式对应了3维空间立方体的体积
```C++
int main()
{
	Matrix3x3 m; 

	m.m11 = 3, m.m12 = -2, m.m13 = 0;
	m.m21 = 1, m.m22 = 4, m.m23 = -3;
	m.m31 = -1; m.m32 = 0, m.m33 = 2;

	// 矩阵的行列式
	std::cout << "Determinant: " << m.Determinant() << std::endl;;
}
```

```C++
// MathUtil.h
#pragma once
#ifndef _MATHUTIL_H_
#define _MATHUTIL_H_

#include <cmath>

const float kPI = 3.14159265f;
const float k2PI = 2.0f * kPI;
const float kPIOver2 = kPI / 2.0f;

inline void SinCos(float& returnSin, float& returnCos, float theta)
{
	returnSin = sin(theta);
	returnCos = cos(theta);

	returnSin = fabs(returnSin) < 1e-6 ? 0 : returnSin;
	returnCos = fabs(returnCos) < 1e-6 ? 0 : returnCos;
}

#endif // _MATHUTIL_H_

```

```C++
// Matrix3X3.h
#ifndef _MATRIX3X3_H_
#define _MATRIX3X3_H_

#include "Vector3.h"
#include "MathUtil.h"

class Vector3;

class Matrix3x3
{
public:
	float m11, m12, m13;
	float m21, m22, m23;
	float m31, m32, m33;

public:
	void SetRotation(int axis, float theta);  // 旋转：绕哪个轴，旋转多少角度
	void SetupScale(const Vector3& v);  // 缩放
	void SetProjection(const Vector3& n);  // 投影
	void SetReflection(int axis);  // 镜像
	void SetReflection(const Vector3& n);  // 3D空间任意方向进行镜像，需要用到较为复杂的镜像矩阵
	void SetupShear(int axis, float s, float t);  // s和t决定切变的幅度
	float Determinant();  // 矩阵的行列式

	friend Matrix3x3 operator*(const Matrix3x3& a, const Matrix3x3& b);
	friend Matrix3x3 operator*=(Matrix3x3& a, const Matrix3x3& b);

	friend Vector3 operator*(const Vector3& v, const Matrix3x3& m);
	friend Vector3 operator*=(Vector3& v, const Matrix3x3& m);
};

#endif  // _MATRIX3X3_H_
```

```C++
// Matrix3X3.cpp
#include "Matrix3x3.h"
#include <cassert>

void Matrix3x3::SetRotation(int axis, float theta)
{
	float s, c;
	SinCos(s, c, theta);

	switch (axis)
	{
	case 1:  // x轴
		m11 = 1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = 0.0f, m22 = c,	m23 = s;
		m31 = 0.0f, m32 = -s,	m33 = c;
		break;
	case 2:  // y轴
		m11 = c,	m12 = 0.0f, m13 = -s;
		m21 = 0.0f, m22 = 1.0f, m23 = 0.0f;
		m31 = s,	m32 = 0.0f, m33 = c;
		break;
	case 3:  // z轴
		m11 = c,	m12 = s,	m13 = 0.0f;
		m21 = -s,	m22 = c,	m23 = 0.0f;
		m31 = 0.0f, m32 = 0.0f, m33 = 1.0f;
		break;
	default:
		assert(false);
	};
}

void Matrix3x3::SetupScale(const Vector3& v)
{
	m11 = v.x,	m12 = 0.0f, m13 = 0.0f;
	m21 = 0.0f, m22 = v.y,	m23 = 0.0f;
	m31 = 0.0f, m32 = 0.0f, m33 = v.z;
}

void Matrix3x3::SetProjection(const Vector3& n)
{
	// n应当是一个单位向量，需要做一个检查
	assert(fabs(n * n - 1.0f) < 1e-6f);

	// 先计算对角线
	m11 = 1.0f - n.x * n.x;
	m22 = 1.0f - n.y * n.y;
	m33 = 1.0f - n.z  * n.z;

	// 再计算上三角和下三角
	m12 = m21 = -n.x * n.y;
	m13 = m31 = -n.x * n.z;
	m23 = m32 = -n.y * n.z;
}

void Matrix3x3::SetReflection(int axis)
{
	switch (axis)
	{
	case 1:  // x坐标发生镜像
		m11 = -1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = 0.0f, m22 = 1.0f, m23 = 0.0f;
		m31 = 0.0f, m32 = 0.0f, m33 = 1.0f;
		break;
	case 2:  // y坐标发生镜像
		m11 = 1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = 0.0f, m22 = -1.0f, m23 = 0.0f;
		m31 = 0.0f, m32 = 0.0f, m33 = 1.0f;
		break;
	case 3:  // z坐标发生镜像
		m11 = 1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = 0.0f, m22 = 1.0f, m23 = 0.0f;
		m31 = 0.0f, m32 = 0.0f, m33 = -1.0f;
		break;
	default:
		assert(false);
		break;
	}
}

void Matrix3x3::SetReflection(const Vector3& n)
{
	// n应当是一个单位向量，需要做一个检查
	assert(fabs(n * n - 1.0f) < 1e-6f);

	float _2nx = -2.0f * n.x;
	float _2ny = -2.0f * n.y;
	float _2nz = -2.0f * n.z;

	// 先计算对角线
	m11 = 1.0f + _2nx * n.x;
	m22 = 1.0f + _2ny * n.y;
	m33 = 1.0f + _2nz * n.z;

	// 再计算上三角和下三角
	m12 = m21 = _2nx * n.y;
	m13 = m31 = _2nx * n.z;
	m23 = m32 = _2ny * n.z;
}

void Matrix3x3::SetupShear(int axis, float s, float t)
{
	switch (axis)
	{
	case 1:  // 用x去切变y和z
		m11 = 1.0f, m12 = s,	m13 = t;
		m21 = 0.0f, m22 = 1.0f, m23 = 0.0f;
		m31 = 0.0f, m32 = 0.0f, m33 = 1.0f;
		break;
	case 2:  // y去切变x和z
		m11 = 1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = s,	m22 = 1.0f, m23 = t;
		m31 = 0.0f, m32 = 0.0f, m33 = 1.0f;
		break;
	case 3:  // z去切变x和y
		m11 = 1.0f, m12 = 0.0f, m13 = 0.0f;
		m21 = 0.0f, m22 = 1.0f, m23 = 0.0f;
		m31 = s,	m32 = t, m33 = 1.0f;
		break;
	default:
		assert(false);
		break;
	}
}

float Matrix3x3::Determinant()
{
	return m11 * (m22 * m33 - m23 * m32)
		 + m12 * (m23 * m31 - m21 * m33)
		 + m13 * (m21 * m32 - m22 * m31);
}

Matrix3x3 operator*(const Matrix3x3& a, const Matrix3x3& b)
{
	Matrix3x3 r;

	r.m11 = a.m11 * b.m11 + a.m12 * b.m21 + a.m13 * b.m31;
	r.m12 = a.m11 * b.m12 + a.m12 * b.m22 + a.m13 * b.m32;
	r.m13 = a.m11 * b.m13 + a.m12 * b.m23 + a.m13 * b.m33;

	r.m21 = a.m21 * b.m11 + a.m22 * b.m21 + a.m23 * b.m31;
	r.m22 = a.m21 * b.m12 + a.m22 * b.m22 + a.m23 * b.m32;
	r.m23 = a.m21 * b.m13 + a.m22 * b.m23 + a.m23 * b.m33;

	r.m31 = a.m31 * b.m11 + a.m32 * b.m21 + a.m33 * b.m31;
	r.m32 = a.m31 * b.m12 + a.m32 * b.m22 + a.m33 * b.m32;
	r.m33 = a.m31 * b.m13 + a.m32 * b.m23 + a.m33 * b.m33;

	return r;
}

Matrix3x3 operator*=(Matrix3x3& a, const Matrix3x3& b)
{
	a = a * b;
	return a;
}

Vector3 operator*(const Vector3& v, const Matrix3x3& m)
{
	return Vector3(
		(v.x * m.m11 + v.y * m.m21 + v.z * m.m31),
		(v.x * m.m12 + v.y * m.m22 + v.z * m.m32),
		(v.x * m.m13 + v.y * m.m23 + v.z * m.m33)
	);
}

Vector3 operator*=(Vector3& v, const Matrix3x3& m)
{
	v = v * m;
	return v;
}
```


