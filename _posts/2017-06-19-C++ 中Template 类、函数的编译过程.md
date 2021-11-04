---
layout: post
title: C++ 中Template 类、函数的编译过程
tags: C++
math: true
date: 2017-06-19 15:32 +0800
---




初学模板的时候为了方便理解可以把模板视为某种数据类型。但是到后来会发现没那么简单，这样的理解方式会带来很多问题。其实模板不是数据类型，模板就是模板，恰如其名。
先来了解一些基本的概念：
编译器使用模板，通过模板参数来创建数据类型，这个过程就是模板实例化（instactiation）。
从模板类创建得到的类型称之为特例（specialization），也称具现化。
模板实例化取决于编译器能够找到可用代码 来创建特例（称之为实例化要素，point of instantiation）。
要创建特例编译器不但要看到模板的声明，还要看到模板的定义。
模板的实例化过程是迟钝的，只有需要用的时候（调用 ，或定义）才会去实例化。

用来个例子来说明模板的编程过程。
在 array.hpp中声明一个模板类array，类中有几个模板函数，这个函数声明和定义全都写在这个文件中，代码如下所示：
array.hpp
template <typename T, int SIZE>
class array
{
    T data_[SIZE];
    array (const array& other);
    const array& operator = (const array& other);
public:
    array(){};
    void print_array(T b);
    T& operator[](int i) {return data_[i];}
    const T& get_elem (int i) const {return data_[i];}
    void set_elem(int i, const T& value) {data_[i] = value;}
    operator T*() {return data_;}
};       

            
然后在main.cpp文件中的主函数中使用上述模板：
main.cpp

#include "array.hpp"

using namespace std;
int main(void)
{
    array<int, 50> intArray;
    intArray.set_elem(0, 2);
    int firstElem = intArray.get_elem(0);
    int* begin = intArray;
    //intArray.printarray(5);
    return 0;
}
这时编译和运行都是正常的。程序先创建一个含有50个整数的数组，然后设置数组的第一个元素值为2，再读取第一个元素值，最后将指针指向数组起点。

问题1： 在array.hpp中我们有很多函数没有只有声明没有定义 ，如void print_array(T b)，编译器为什么不给报错说有函数没有实现呢？

问题2：为什么当我们把main.cpp的intArray.print_array(5);去掉注释之后为什么才会报：main.cpp:12: error: undefined reference to `array<int, 50>::printarray(int)'？

带着这些两问题我们来看另一种情况，也就是传统编写头文件和cpp的方式。将array.hpp文件分裂成为array.h和array.cpp二个文件。

main.cpp

#include "array.h"
using namespace std;
int main(void)
{
    array<int, 50> intArray;
    intArray.set_elem(0, 2);
    int firstElem = intArray.get_elem(0);
    int* begin = intArray;
    //intArray.print_array(5);
    return 0;
}
array.h        

template <typename T, int SIZE>
class array
{
    T data_[SIZE];
    array (const array& other);
    const array& operator = (const array& other);
public:
    array(){};
    void print_array(T b);
    T& operator[](int i);
    const T& get_elem (int i) const;
    void set_elem(int i, const T& value) ;
    operator T*() ;
};       

array.cpp

#include "array.h"
template<typename T, int SIZE> T& array<T, SIZE>::operator [](int i)
{
    return data_[i];
}
template<typename T, int SIZE> const T& array<T, SIZE>::get_elem(int i) const
{
    return data_[i];
}
template<typename T, int SIZE> void array<T, SIZE>::set_elem(int i, const T& value)
{
    data_[i] = value;
}
template<typename T, int SIZE> array<T, SIZE>::operator T*()
{
    return data_;
}
问题3：为什么这种声明和实现分开的方式会报错，而写在一起就不会？  

问题4：这种写法在编译时会出现下面的错误：
main.cpp:9: error: undefined reference to `array<int, 50>::set_elem(int, int const&)'

main.cpp:10: error: undefined reference to `array<int, 50>::get_elem(int) const'

main.cpp:11: error: undefined reference to `array<int, 50>::operator int*()'

error: ld returned 1 exit status

可是我们明明已经在array里写了这些函数的实现啊，这是为什么 ？

问题5：array中有多于3个函数，为什么只有3个没有找到定义？


现在一一解释这5个问题。问题3和4是整个编译原理的关键，所以先说问题3，4。假设c++在编译时，把main.cpp编译成main.obj，array.cpp编译成array.obj。

问题3，4：

第一种hpp写法，编译器知道模板array<int,SIZE>，array::set_elem(int , const T& )，array::get_elem (int i) const

array::operator int*()的定义，所以能够具现化，main.obj的符号导出表中就有这些符号的地址，于是就不存在链接问题，所以能运行。

第二种声明和实现（定义）分离的方式，编译器在array.h文件中看到了这些模板类和函数的声明，但没有模板的定义，这样编译器就不能创建类型array<int,SIZE>，array::set_elem(int , const T& )，array::get_elem (int i) const

array::operator T*()，即无法具现化这些符号。但这时并不出错，因为编译器认为模板定义在其它文件中，就把问题留给链接程序处理，希望它能够在其他.obj里面找到的实现体,在本例中就是array.obj（详细可参考c++编译过程）

编译array.cpp时编译器可以解析模板定义并检查语法，但不能生成成员函数的代码，因为要生成代码，需要知道模板参数，即需要一个类型，而不是模板本身。array.cpp中有所有模板函数的实现，但这不是具现，这种实现但不会产生二进制代码。这样在main.obj和array.obj中都没有array<int,SIZE>，array::set_elem(int , const T& )，array::get_elem (int i) const，array::operator int*()这些符号的具体(实现)，即没有它们的二进制代码，所以链接器会报错。

问题1,2,5：

模板的实例化过程是迟钝的，只有需要用的时候（调用 ，或定义）才会去实例化它的定义。

所以当array<int,SIZE>::intArray.print_array(int)不被调用时，编译器不会去检查它是还被实现或具现化。当调用时

编译器发现它没有实现这时候才会报错。


此外，operator T*() ;这个函数，没有参数没有返回值，这是一个类型转换函数,将Array转换成 T*类型。
参考operator的用法。
参考： http://blog.csdn.net/liuzhi1218/article/details/7657586
本文整理自： http://blog.csdn.net/look01/article/details/3228134
