---
author: GuoYS
layout: post
title:  "模板的介绍及使用"
date:   2016-09-16 16:36:10 +0800
categories: C++
tags: template
---
* content
{:toc}

  在C++中，适当的使用模板类或者模板函数能够大大的减少代码量的编写，这里介绍一些关于模板的使用方法和注意事项









- 模板定义

  - 模板是C++支持参数化多态的工具，使用模板可以使用户为类或者函数声明一种一般模式，使得类中的某些数据成员或者成员函数的参数、返回值取得任意类型
  - 模板是一种对类型进行参数化的工具
  - 函数模板针对仅参数类型不同的函数
  - 类模板针对仅数据成员和成员函数类型不同的类

    注意：模板的声明或定义只能在全局，命名空间或类范围内进行。即不能在局部范围，函数内进行，比如不能在main函数中声明或定义一个模版


- 函数模板

  - 基本格式

  ```
    template <class 形参名，class 形参名,......>
    returnType functionName(parament list)
    {
        body;
    }
  ```


    其中template和class是关见字，class可以用typename关见字代替，在这里typename 和class没区别，<>括号中的参数叫模板形参，模板形参和函数形参很相像，模板形参不能为空。一但声明了模板函数就可以用模板函数的形参名声明类中的成员变量和成员函数，即可以在该函数中使用内置类型的地方都可以使用模板形参名。模板形参需要调用该模板函数时提供的模板实参来初始化模板形参，一旦编译器确定了实际的模板实参类型就称他实例化了函数模板的一个实例。

    例如交换：

  ```
    template <class T>
    void swap(T& a,T& b){}
  ```

    当调用这样的模板函数时类型T就会被被调用时的类型所代替,不能在函数调用的参数中指定模板形参的类型，对函数模板的调用应使用实参推演来进

- 类模板通式

  - 基本格式

  ```
    template <class 形参名，class 形参名,......>
    class ClassName {
        .....
    };
  ```


    类模板和函数模板都是以template开始后接模板形参列表组成，模板形参不能为空，一但声明了类模板就可以用类模板的形参名声明类中的成员变量和成员函数，即可以在类中使用内置类型的地方都可以使用模板形参名来声明
  
  ```
    example：
    template<class T> 
    class A{
        public:
            T a;
            T b; 
            T hy(T c, T d);
    };
  ```

    类模板对象的创建：
    比如一个模板类A，则使用类模板创建对象的方法为A<int>;在类A后面跟上一个<>尖括号并在里面填上相应的类型，这样的话类A中凡是用到模板形参的地方都会被int 所代替。当类模板有两个模板形参时创建对象的方法为A<int, double> m;类型之间用逗号隔开


- 在类模板外部定义成员函数的方法为


  ```
    template<模板形参列表>
  　returnType 类名<模板形参名>::函数名(参数列表){函数体}；
    template<class T1> void A<T1>::hy(T1 c,T1 d){}
　```
　


- 模板的声明或定义只能在全局，命名空间或类范围内进行。即不能在局部范围，函数内进行



## 模板的形参

- 说明：有三种类型的模板形参：类型形参，非类型形参和模板形参
- 类型形参



- 非类型形参

  - 定义

    模板的非类型形参也就是内置类型形参，如template<class T, int a> class Temp{};其中int a就是非类型的模板形参

  - 说明

    - 非类型形参在模板的内部是常量
    - 非类型模板的形参只能是整型，指针和引用
    - 调用非类型模板形参的实参必须是一个常量表达式，即他必须能在编译时计算出结果
    - 任何局部对象，局部变量，局部对象的地址，局部变量的地址都不是一个常量表达式，都不能用作非类型模板形参的实参。全局指针类型，全局变量，全局对象也不是一个常量表达式，不能用作非类型模板形参的实参。
    - 全局变量的地址或引用，sizeof表达式的结果，全局对象的地址或引用const类型变量是常量表达式
    - 非类型形参一般不应用于函数模板中
    - 非类型模板形参的形参和实参间所允许的转换

  ```
    //函数模板
    template<typename T>
    const T & max1(const T& a,const T& b){
        return a>b ? a:b;
    }

    test:
    int a=2,b=8;
    max1(2.2,8.3)//模板实参被隐式推演成double或者max1(a,b)
    max1<double>(2.1,2.2)<//显示指定模板参数。
    max1<int>(2.1,2.2)//显示指定的模板参数，会将函数函数直接转换为int。

    把const去掉调用方式：
    int a=2,b=8;
    max1(a,b);不能直接使用常量

    //类模板
    template<class T,int MAXSIZE> class Stack{
        privare:
            T elements[MAXSIZE];
            int count;
        public:
            Stack():count(0){}
            void Push(T const& elem)
            {
                if(count == MAXSIZE)
                {
                    throw std::out_of_range("Stack<>::push(): stack is full");
                }
                elements[count]=elem;
                ++count;
            }
            void Pop()
            {
                if(count<=0)
                {
                    throw std::out_of_range("Stack<>::push(): stack is empty");
                }
            }
            T top() const
            {
                if(count<=0)
                {
                    throw std::out_of_range("Stack<>::push(): stack is empty");
                }
                return elements[count--];
            }
         bool empty() const{     
             return count == 0;
         }
         bool full() const{    
             return count == MAXSIZE;
         }
    };

    类外实现写法：
    template <class T,int MAXSIZE> 
    Stack<T,MAXSIZE>::Stack():numElems(0){  
    }

    test:
    #include<stdlib>
    int main(){

        try{
            Stack<int,20>  int20Stack;  // 可以存储20个int元素的栈
            Stack<int,40>  int40Stack;  // 可以存储40个int元素的栈
            Stack<std::string,40> stringStack; 
            // 可存储40个string元素的栈
      
        }catch(std::exception const& ex){
            std::cerr << "Exception: " << ex.what() << std::endl;
            return EXIT_FAILURE;
        }
    }
  ```

- 模板形参

  - 可以为类模板的类型形参提供默认值，但不能为函数模板的类型形参提供默认值。函数模板和类模板都可以为模板的非类型形参提供默认值
  - 类模板的类型形参默认值形式为：
      template<class T1, class T2=int> class A{};
  - 类模板类型形参默认值和函数的默认参数一样，不允许template < class T1=int, class T2>class A{};该写法，默认的写最右边
  - 在类模板的外部定义类中的成员时template 后的形参表应省略默认的形参类型，不需要写=int

  ```
    //类模板类型形参
    template<class T> 
    class A{
        public:
            T get(T a,T b)
            {
                return a>>b;
            }
    };
    调用：
    A<int> a;
    a.get(8,2);//结果为2

    //类模板的默认形参
    template<class T1,class T2=int>
    class B{
        public:
            int get1(T1 a,T2 b)
            {
                return a>>b;
            }
    };
    调用：
    B<int>b;
    b.get1(8,2); //结果为2s
  ```
  















