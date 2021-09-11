### 2.9 设计一个简单的类
```cpp
class + 类名
{
    成员变量
    成员函数
};
```
如果不设置公共属性，那么出了类的范围就访问不到成员了！
```cpp
class + 类名
{
public:
    成员变量
    成员函数
};
```
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

const double PI = 3.14;
//class + 类名
class Circle
{
public:	//公共权限，为了让外部能访问到类中的属性

	//类中的函数称为成员函数，或者也可以成为成员方法
	//求圆周长
	double calculateZC()
	{
		return 2 * PI * m_R;
	}
	//设置半径
	void setR(int r)
	{
		m_R = r;
	}
	//获取半径
	int getR()
	{
		return m_R;
	}
	//类中的变量称为成员变量，或者成员属性
	//半径
	int m_R;
};
```
通过类来创建一个对象：实例化对象
```cpp
void test01()
{
	Circle c1;//通过类来创建一个对象；实例化对象
	//给c1的半径赋值
	//c1.m_R = 10;
	c1.setR(10);
	//求c1的周长
	cout << "圆的半径： " << c1.getR() << endl;
	cout << "圆的周长： " << c1.calculateZC() << endl;
}
int main()
{
	test01();
	return 0;
}
```
### 2.10 宏定义缺陷
1、宏缺陷1：必须要添加括号保证运算完整；  
2、宏缺陷2：即使加了括号，有些运算依然与预期不符；  

宏函数优势：不会进行入栈和出栈，直接做宏替换，直接跑原码，不会带来时间上的开销
* 注意宏不属于C语言，所以结尾没有封号  
```cpp
#define MYADD(x,y) ((x)+(y))
void test01()
{
	int a = 10;
	int b = 20;
	int ret = MYADD(a, b)*20;
	cout << ret << endl;
}
#define MYCOMPARE(a,b) (((a)<(b))?(a):(b))
void test02()
{
	int a = 10;
	int b = 20;
	int ret = MYCOMPARE(++a, b);
	cout << ret << endl;
}
int main()
{
	test01();
	test02();
	return 0;
}
```
```cpp
>>600
>>12
```
### 2.11 内联函数
在 C++ 中，预定义宏是用内联函数来实现的，而内联函数本身也是一个真正的函数。内联函数具有普通函数的所有行为，**唯一不同之处在于内联函数会在适当的地方像预定义宏一样展开，所以不需要函数调用的开销。**  
在普通函数前加 inline 关键字使之成为内联函数。但是必须注意的是：函数体必须和函数声明结合在一起！！！且函数的声明和实现必须同时加关键字inline。  
内联函数的确占用空间，但是内联函数相对于普通函数的优势知识省去了函数调用时候的压栈、跳转、返回的开销。相当于直接将函数代码复制到被调用处，省去了函数调用所花费的开销，我们可以理解为内联函数是以空间换时间。  
```cpp
inline void func(int a);
inline void func(int a)
{
	return a;
}
```

类内部的成员函数在函数前都隐式加了关键字 inline；  
但是 C++ 内联编译会有一些限制，以下情况编译器可能考虑不会将函数进行内联编译：  
1. 不能存在任何形式的循环语句；
2. 不能存在过多的条件判断语句；
3. 函数体不能过于庞大；
4. 不能对函数进行取址操作；

内联仅仅是给编译器一个建议，编译器不一定接受这种建议，如果你没有将函数声明为内联函数，那么编译器也可能将此函数做内联编译。一个好的编译器将会内联小的、简单的函数。  

### 2.12 函数的默认参数和占位参数
#### 2.12.1 默认参数
语法：形参中：类型 变量 = 默认值  

>注意事项:
>>如果有一个位置有了默认参数，那么从这个位置起，从左到右都必须有默认值；
>>函数声明和实现只能有一个提供默认参数，不可以同时加默认参数
```cpp
int func(int a=10, int b=10)
{
	return a + b;
}
void test01()
{
	cout << func(20) << endl;
	cout << func(20,30) << endl;
	cout << func() << endl;
}

void myFunc(int a, int b);
void myFunc(int a = 10, int b = 10) {}
```
```cpp
>>30
>>50
>>20
```
#### 2.12.2 占位参数
占位参数：只写一个类型进行占位，调用时必须要传入占位值。  
占位参数用途：目前没用，学习运算符重载时会用。  
占位参数也可以有默认值，如 int = 1;
```cpp
void func2(int a, int)
{
	return;
}
void test02()
{
	func2(10, 1);
}
```

### 2.13 函数重载
在传统 C 语言中，函数名必须唯一，程序中不允许出现同名函数；在 C++ 中允许出现同名的函数，这种现象称为函数重载。函数重载的目的就是为了方便的使用函数名。  
函数重载条件：  
1、在同一个作用域；  
2、函数名称相同；  
3、参数个数、类型、顺序不同；  
4、返回值类型不同不可以作为函数重载的条件，会出现二义性，因为在调用的时候可以不接收函数的返回值；  
```cpp
//class Person {
//public:
//	void func()	//成员函数，而不是全局函数
//	{
//		;
//	}
//};
void func()
{
	cout << "func()调用" << endl;
}
void func(int a)
{
	cout << "func(int a)调用" << endl;
}
void func(double a)
{
	cout << "func(double a)调用" << endl;
}
void func(int a, double b)
{
	cout << "func(int a, double b)调用" << endl;
}
void func(double a, int b)
{
	cout << "func(double a, int b)调用" << endl;
}
//int func(double a, int b)
//{
//	cout << "func(double a, int b)调用" << endl;
//}
void test01()
{
	func();
	func(1);
	func(1.2);
	func(1, 3.0);
	func(1.0, 2);
}
```
```cpp
>>func()调用
>>func(int a)调用
>>func(double a)调用
>>func(int a, double b)调用
>>func(double a, int b)调用
```
#### 2.13.1 函数重载之引用
```cpp
//函数重载中 引用的两个版本
//void myFunc(int a)	//可以同时存在，但是最好不要，避免二义性
//{
//	cout << "myFunc(int a)调用" << endl;
//}
void myFunc(int& a)	//int &a = 10 不合法
{
	cout << "myFunc(int& a)调用" << endl;
}
void myFunc(const int& a)	//const int &a = 10 合法
{
	cout << "myFunc(const int& a)调用" << endl;
}
void test02()
{
	int a = 10;
	myFunc(a);
	//本身a属于可读可写的变量，所以在调用的时候也会寻找可读可写的版本，因此会调用 myFunc(int &a)
	myFunc(10);
}
```
```cpp
>>myFunc(int& a)调用
>>myFunc(const int& a)调用
```
#### 2.13.2 函数重载遇到默认参数
```cpp
//函数重载碰到默认参数，注意避免二义性出现
void func2(int a, int b = 10)
{
	cout << "func(int a, int b = 10)调用" << endl;
}
void func2(int a)
{
	cout << "func(int a)调用" << endl;
}
void test03()
{
	//func2(10);	//出现二义性
	func2(10, 10);
}
```
```cpp
>>func(int a, int b = 10)调用
```

#### 2.13.3 函数重载实现原理
编译器为了实现函数重载，它用不同的参数类型来修饰不同的函数名，比如 void func()；编译器可能会将函数名修饰成 _func，当编译器碰到 void func(int x) 时，编译器可能将函数名修饰为 _func_int，当编译器碰到 void func(int x, char c)，编译器可能会将函数名修饰为 _func_int_char，这里的“可能”是因为编译器如何修饰重载的函数名称并没有一个统一的标准，所以不同的编译器会产生不同的内部名。
```cpp
void func(){}
void func(int x){}
void func(int x, char y){}
//linux下生成的编译之后的函数名为
_Z4funcv	//v代表void，无参数
_Z4funci	//i代表参数为int类型
_Z4funcic	//c代表参数为char类型
```
#### 2.13.4 extern 浅析
在 C++ 中调用 C 语言 文件  
C++ 中有函数重载，会对函数名称做修饰，导致调用 C 语言的函数链接失败，利用 extern C 可以解决。  
* 方法一：  
  在 C++ 代码下加入： extern "C" void show();
* 方法二：  
  在 C 语言头文件中加入6行代码  
  #ifdef __cpluscplus  
  extern "c" {  
  #endif  
  #ifdef __cpluscplus  
  }  
  #endif  
```cpp
//方法一：
"test.h"
#pragma once
#include<stdio.h>
void show();

"test.c"
#include"test.h"
void show()
{
	printf("hello world\n");
}

"extern 浅析"
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;
//#include"test.h"
//告诉编译器，show函数用C语言的方式做链接
extern "C" void show();//加上这句话，就不能包含头文件，test.h已经包含过头文件
void test01()
{
	show();	//c++会将函数名函数重载成 _Z4showv，而C语言并没有函数重载，因此找不到函数原型，因此连接失败
}
int main()
{
	test01();
	return 0;
}
```
```cpp
//方法二：
“test.h"
#pragma once
#ifdef __cplusplus
extern "C" {
#endif

#include<stdio.h>
	void show();

#ifdef __cplusplus
}
#endif

"test.c"
#include"test.h"
void show()
{
	printf("hello world\n");
}

"extern 浅析"
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;
#include"test.h"

void test01()
{
	show();
}
int main()
{
	test01();
	return 0;
}
```