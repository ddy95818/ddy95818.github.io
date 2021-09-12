# C++
## 3 类和对象
### 3.1 类和对象的基本概念

**C 和 C++ 中 struct 的区别：**  
* C 语言 struct 只有变量；  
* C++ 语言 struct 既有变量，又有函数；  

**class 和 struct 的区别：**  
* class 默认权限是私有权限；  
* struct 默认权限是公共权限；  

**访问权限：**  
* public 公共权限；类内类外都可以访问到成员；  
* private 私有权限；类内可以访问，类外不可以访问；儿子不可以访问父亲的 private 权限内容；  
* protected 保护权限；类内可以访问，类外不可以访问；（继承再学：儿子可以访问父亲的 protected 权限内容）；  

```cpp
class Person {
public:
	string m_Name;
protected:
	string m_Car;
private:
	int m_pwd;
public:
	void func()
	{
		m_Name = "张三";
		m_Car = "tesla";
		m_pwd = 123456;
	}
};
void test02()
{
	Person2 p;
	p.m_Name = "李四";//公共权限，类外可以访问
	//p.m_Car = "奔驰";//保护权限，类外访问不到
	//p.m_pwd = 123;//私有权限，类外不可以访问
}
```
C++ 中用了 string 定义变量的话，可以直接用等号来赋值字符串，但必须要加#include\<string\>!  

#### 3.1.1 类的封装
**C 语言封装缺点：**  
将属性和行为分离；  

**C++ 的封装理念：**  
第一层：将属性和行为作为一个整体，来表现生活中的事物；  
第二层：将属性和行为加以权限控制；  

```cpp
//c 代码实现封装
struct Person {
	char name[64];
	int age;
};
struct Dog {
	char name[64];
	int age;
};

void PersonEat(struct Person* p)
{
	printf("%s在吃饭\n", p->name);
}
void DogEat(struct Dog* p)
{
	printf("%s在吃狗粮\n", p->name);
}

void test01()
{
	struct Person p;
	strcpy(p.name, "张三");
	p.age = 25;
	PersonEat(&p);
}
void test02()
{
	struct Dog d;
	strcpy(d.name, "旺财");
	d.age = 2;
	DogEat(&d);

	struct Person p;
	strcpy(p.name, "张三");
	p.age = 25;
	DogEat(&p);
}

int main()
{
	test01();
	test02();
	return 0;
}
```
```cpp
>>张三在吃饭
>>旺财在吃狗粮
>>张三在吃狗粮
```
```cpp
//C++实现封装
struct Person {
	char name[64];
	int age;
	void PersonEat()
	{
		printf("%s在吃饭\n", name);
	}
};
struct Dog {
	char name[64];
	int age;
	void DogEat()
	{
		printf("%s在吃狗粮\n", name);
	}
};

void test01()
{
	struct Person p;
	strcpy(p.name, "老王");
	p.PersonEat();
}
int main()
{
	test01();
	return 0;
}
```
```cpp
//C++实现封装
class Person {
public:
	char name[64];
	int age;
	void PersonEat()
	{
		printf("%s在吃饭\n", name);
	}
};
class Dog {
public:
	char name[64];
	int age;
	void DogEat()
	{
		printf("%s在吃狗粮\n", name);
	}
};

void test01()
{
	struct Person p;
	strcpy(p.name, "老王");
	p.PersonEat();
}
int main()
{
	test01();
	return 0;
}
```
#### 3.1.2 成员属性尽量保证 private

**将成员属性都设置为私有的好处**：  
1. 自己可以控制读写权限；  
2. 可以对设置内容加有效性验证；  

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;
class Person {
public:
	//设置姓名
	void setName(string name)
	{
		m_Name = name;
	}
	//获取姓名
	string getName()
	{
		return m_Name;
	}
	//获取年龄
	int getAge()
	{
		return m_Age;
	}
	//设置年龄
	void setAge(int age)
	{
		if (age < 0 || age>150)
		{
			cout << "你这个有问题" << endl;
			return;
		}
		m_Age = age;
	}
	//设置情人
	void setLover(string lover)
	{
		m_lover = lover;
	}
private:
	string m_Name;	//可读可写
	int m_Age = 18;	//可读可写（0~150）
	string m_lover;	//只写
};
void test01()
{
	Person p;
	//可以将 char* 隐式转换为 string
	p.setName("张三");
	cout << "姓名： " << p.getName() << endl;
	//获取年龄
	p.setAge(25);
	cout << "年龄： " << p.getAge() << endl;
	//设置情人
	//情人是只写权限，外界访问不到，我们没有给外界提供接口
	p.setLover("aaa");
}
int main()
{
	test01();
	return 0;
}
```
```cpp
>>姓名： 张三
>>年龄： 25
```
### 3.2 面向对象的设计案例
一个类作为其他类的私有属性，那么这个类的私有属性在其他类中也是不可见的！  
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;
class Point {
public:
	void setX(int x)
	{
		m_X = x;
	}
	void setY(int y)
	{
		m_Y = y;
	}
	int getX()
	{
		return m_X;
	}
	int getY()
	{
		return m_Y;
	}
private:
	int m_X;
	int m_Y;
};
class Circle {
public:
	void setR(int r)
	{
		m_R = r;
	}
	int getR()
	{
		return m_R;
	}
	void setCenter(Point p)
	{
		m_Center = p;
	}
	Point getCenter()
	{
		return m_Center;
	}
private:
	int m_R;
	Point m_Center;//圆心
};
void test01()
{
	Point p;
	p.setX(10);
	p.setY(10);

	Circle c;
	Point pCenter;
	pCenter.setX(10);
	pCenter.setY(0);
	c.setCenter(pCenter);
	c.setR(10);
	Point pp = c.getCenter();
	cout << pp.getX() << "," << pp.getY() << endl;
}
int main()
{
	test01();
	return 0;
}
```
```cpp
>>10, 0
```
#### 3.2.1 分文件编写
```cpp
"point.h"
#pragma once
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;
class Point {
public:
	void setX(int x);
	void setY(int y);
	int getX();
	int getY();
private:
	int m_X;
	int m_Y;
};

"point.cpp"
#include"point.h"
void Point::setX(int x)
{
	m_X = x;
}
void Point::setY(int y)
{
	m_Y = y;
}
int Point::getX()
{
	return m_X;
}
int Point::getY()
{
	return m_Y;
}

"circle.h"
#pragma once
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
#include"point.h"
using namespace std;
class Circle {
public:
	void setR(int r);
	void setCenter(Point p);
	Point getCenter();
private:
	int m_R;
	Point m_Center;//圆心
};

"circle.cpp"
#include"circle.h"

void Circle::setR(int r)
{
	m_R = r;
}
void Circle::setCenter(Point p)
{
	m_Center = p;
}
Point Circle::getCenter()
{
	return m_Center;
}

"main.cpp"
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;
#include"point.h"
#include"circle.h"
void test01()
{
	Point p;
	p.setX(10);
	p.setY(10);

	Circle c;
	Point pCenter;
	pCenter.setX(10);
	pCenter.setY(0);
	c.setCenter(pCenter);
	c.setR(10);
	Point pp = c.getCenter();
	cout << pp.getX() << "," << pp.getY() << endl;
}
int main()
{
	test01();
	return 0;
}
```