---
author: GuoYS
layout: post
title:  "C/C_Plus_Plus知识点"
date:   2016-09-10 21:01:32 +0800
categories: C_Plus_Plus
tags: C_Plus_Plus
---
* content
{:toc}

  这里主要是一些关于在笔试面试中遇到的一些问题，在学习中总结，在总结中学习



## 指针和引用的区别 ##
-	非空区别:在任何情况下都不能使用指向空值的引用，指针可以为空
  -合法性区别:在使用引用之前不需要测试它的合法性。相反，指针则应该总被测试，防 止其为空
  -可修改区别:指针可以被重新赋值以指向另一个不同的对象，引用在初始化之后不可以在改变
  -应用区别:在以下情况下应该使用指针，一是考虑到存在不指向任何对象的可能(在这种情况下可以设置指针为空)，二是需要能够在不同的时候指向不同对象(可以改变指针指向)
## 交换两个数，不使用第三个变量 ##

-	算术运算


   void fun(int a,int b)
   {
       a = b - a;
       b = b - a;
       a = b + a;
   }
- 指针地址运算


    void fun(int *a, int *b)
    {
        if(a < b){
            a = (int *)(b - a);
            b = (int *)(b - ((int) a & 0x0000ffff));
            a = (int *)(b + ((int) a & 0x0000ffff));
        }else{
            b = (int *)(a - b);
            a = (int *)(a - ((int) b & 0x0000ffff));
            b = (int *)(a + ((int) b & 0x0000ffff));
        }
    }

- 位运算


    void fun(int a, int b)
    {
        a = a^b;
        b = a^b;
        a = a^b;
    }

- 栈实现


    void fun(int a,int b)
    {
        stack s;
        Push(s,a);
        Push(s,b);
        a = Pop(s);
        b = Pop(s);
    }
## const与#define相比有什么不同 ##
- C++语言可以用const定义常量，也可以用#define定义常量,但是前者比后者有更多的优点
  - const常量有数据类型，而宏常量没有数据类型。编译器可以对前者进行类型安全检查，而对后者只进行字符替换，没有类型安全检查，并且在字符替换中可能会产生意想不到的错误(边际效应)
  - 有些集成化的调试工具可以对const常量进行调试，但不能对宏常量进行调试。在C++程序中只使用const常量而不使用宏常量，即const常量完全取代宏常量
  - const定义的常量可被修改  

```
 const int a = 10;
 *((int *)&a) = 12;
```

## 循环队列 ##

- 入队和出队的操作


    rear = (rear+1)%m;
    front = (front+1)%m;
- 删除操作：front指针
- 插入操作：rear指针
- 判断队列已满


    front == (rear+1)%m;
- 队列长度


   (rear-front+m)%m;

## union、struct、enum大小区别 ##

- 枚举enum：大小实质上就是整形


    typedef enum{
                ANUnknow,ANShape,ANData}ANDataType;
    sizeof(ANDataType) = 4;
- 联合union：大小则以定义中最大的数据类型为准


    union AAA{
        double d;
        int i;
    }
    sizeof(AAA) = 8;

- 结构体struct：字节对齐原则


    struct BBB{
        double dd;
        int i;
    }
    sizeof(BBB) = 16;//空结构体的大小为1字节

---

## 编写类String的构造、析构、拷贝构造和赋值函数 ##
类原型：

    class String{
        public:
            String(const char *str = NULL);//普通构造函数
            String(const String &other);//拷贝构造函数
            ~String(void);//析构函数
            String & operator =(const String &other);//赋值函数
        private:
            char *m_data;
    };


- 普通构造函数


   String::String(const char *str)
   {
       if(str == NULL)
       {
           m_data = new char[1];
           *m_data = '\0';
       }
       else{
           int length = strlen(str);
           m_data = new char[length+1];
           strcpy(m_data, str);
       }
   }

   

- 析构函数


    String::~String(void)
    {
        delete [] m_data;
    }


- 拷贝构造函数


    String::String(const String &other)
    {
        int length = strlen(other.m_data);
        m_data = new char[length+1];
        if(m_data == NULL)
        {
            printf("申请内存失败\n");
        }else{
            strcpy(m_data, other.m_data);
        }
    }

- 赋值函数


    String & String::operator =(const String &other)
    {
        if(this == other)
        {
            return *this;
            delete [] m_data;
        }
        int length = strlen(other.m_data);
        m_data = new char[length+1];
        if(m_data == NULL)
        {
            printf("申请内存失败\n");
        }else{
            strcpy(m_data,other.m_data);
        }
        return *this;
    }

## 拷贝构造函数 ##
使用场合：

- 新建一个类，并用另一个同类的对象对它初始化
- 当函数参数为类对象时，在调用时需要将实参对象传给形参


    extern void show(Circle cir);
    Circle cir1;
    show(cir1);

- 当函数返回值是类对象时


    extern Circle getCircle();
    Circle cir = getCircle();


## volatile(JD笔试题) ##

作用:作为指令关键字，确保本条指令不会因编译器的优化而省略，且要求每次直接读值
一个定义为volatile的变量是说这变量可能会被意想不到地改变，这样，编译器就不会去假设这个变量的值了。精确地说就是，优化器在用到这个变量时必须每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份

- 一个参数既可以是const还可以是volatile吗？解释为什么？
   答：是的，一个例子是只读的状态寄存器。它是volatile因为它可能被意想不到地改变。它是const因为程序不应该试图去修改它。
- 一个指针可以是volatile 吗？解释为什么？
   答:是的,尽管这并不很常见。一个例子是当一个中断服务子程序修改一个指向一个buffer的指针时
## mutable的作用 ##
在C++程序中，类里面的数据成员加上mutable后，修饰为const的成员变量，就可以修改它了

## C++菱形继承问题(JD笔试题) ##

- 什么是菱形继承
  假设我们有类B和类C，它们都继承了相同的类A。另外我们还有类D，类D通过多重继承机制继承了类B和类C
- 产生的问题(二义性)
  问题是：因为Liger多重继承了Tiger和Lion类，因此Liger类会有两份Animal类的成员（数据和方法），Liger对象"lg"会包含Animal基类的两个子对象。
  所以，你会问Liger对象有两个Animal基类的子对象会出现什么问题？再看看上面的代码-调用"lg.getWeight()"将会导致一个编译错误。这是因为编译器并不知道是调用Tiger类的getWeight()还是调用Lion类的getWeight()。所以，调用getWeight方法是不明确的，因此不能通过编译。
- 解决方案
  如果Lion类和Tiger类在分别继承Animal类时都用virtual来标注，对于每一个Liger对象，C++会保证只有一个Animal类的子对象会被创建


    class Tiger : virtual public Animal { /* ... */ };

    class Lion : virtual public Animal { /* ... */ } 

因为Java不支持多继承，所以不会出现菱形继承问题。但是Java可以通过接口间接实现多重继承

    Class Mule implements Horse,Donkey  
    {  
      /* Horse和Donkey是接口*/  
    } 


## 结构体的定义方式 ##

- 方式一:先定义结构体类型，在定义变量


    struct Student{
        Type data;
    };
    struct Student stu1,stu2;

- 方式二:在定义结构体的同时定义变量


   struct Student{
       Type data;
   }stu1,stu2;

- 方式三:定义匿名结构体(只能一次性声明结构体变量，不能另外声明)


    struct {
        Type data;
    }stu1,stu2;    

- 使用typedef声明->推荐该方法


   typedef struct{
       Type data;
   }Student;
   Student stu1,stu2;

- 结构体的自引用


    struct SELF_REF2{
        Type data;
        struct SELF_REF2 *b;
    };
    或者：
    typedef struct SELF_REF3_TAG {
        Type data;
        struct SELF_REF3_TAG *b;        
    }SELF_REF3;

---
## C++多线程 ##




- 线程的创建


```
#include<pthread.h>
   void* say_hello(void* arg)
   {
       cout<<"Hello....."<<endl;
   }
   pthread_t tids;
   int ret = pthread_creat(&tips,NULL,say_hello,NULL);
```
  如果线程调用到函数在一个类中，那必须将该函数声明为静态函数函数
  因为静态成员函数属于静态全局区，线程可以共享这个区域，故可以各自调用

- 在线程调用函数时传入参数



​    

    void* say_hello(void* arg)
    {
        int i = *( (int *)arg );//对传入的参数进行强制转换
        cout<<"Hello....."<<endl;
    }
    
    int ret = pthread_creat(&tips,NULL,say_hello,(void *)&i);


- 使用pthread_join等待线程结束


    pthread_join(tips,NULL);

​    

- 线程创建时属性参数的设置pthread_attr_t及join功能的使用


    typedef struct
    {
         int detachstate;   //线程的分离状态
         int schedpolicy;   //线程调度策略
         struct sched_param schedparam;   //线程的调度参数
         int inheritsched; //线程的继承性 
         int scope; //线程的作用域 
         size_t guardsize; //线程栈末尾的警戒缓冲区大小 
         int stackaddr_set; 
         void * stackaddr; //线程栈的位置 
    }pthread_attr_t;
使用方法：

    pthread_attr_t attr; //线程属性结构体，创建线程时加入的参数  
    pthread_attr_init( &attr ); //初始化  
    pthread_attr_setdetachstate( &attr, PTHREAD_CREATE_JOINABLE ); //是设置你想要指定线程属性参数，这个参数表明这个线程是可以join连接的join功能表示主程序可以等线程结束后再去做某事，实现了主程序和线程同步功能


## 分别给出BOOL，int，float，指针变量 与“零值”比较的 if 语句（假设变量名为var） ##

- BOOL型变量


    if(!var)
 

- int型变量


   if(var==0)



- float型变量：


    const float EPSINON = 0.00001;
    if ((x >= - EPSINON) && (x <=EPSINON)


​    

- 指针变量


    if(var==NULL)

## this指针 ##
- This指针本质是一个函数参数，只是编译隐藏起来形式的，语法层面上的参数。this只能在成员函数中使用，全局函数、静态函数不能使用this。
   实际上，成员函数默认第一个参数为T* const this
- this在成员函数的开始前构造，在成员的结束后清除。当调用一个类的成员函数时，编译器将类的指针作为函数的this参数传递进去


    A a;
    a.func(10);
    //编译器会编译成：
    A::func(&a,10);

- this指针并不占用对象的空间
   所有成员函数的参数，不管是不是隐含的，都不会占用对象的空间，只会占用参数传递时的栈空间，或者直接占用一个寄存器
- this指针是什么时候创建的
   this在成员函数的开始前构造，在成员的结束后清
- this指针存放在何处？堆、栈、还是其他？
   this指针会因编译器不同而有不同的放置位置，可能是堆、栈，也有可能是寄存器
- this指针如何是传递给类中的参数的?
   大多数编译器通过ecx寄存器传递给this指针，一般来说不同编译器都会遵从一致的传参规则，否则不同编译器产生的obj就无法匹配了
- 如果我们知道一个对象this指针的位置，可以直接使用吗？
   this指针只有在成员函数中才有定义，因此，你获得一个对象后，也不能通过对象使用this指针。
  只有在成员函数中采用this指针的位置，通过&this获取，也可以直接使用


## 内存的分配方式有几种 ##

 1. 从静态存储区域分配:
                   内存在程序编译的时候就已经分配好，这块内存在程序的整个运行期间都存在。例如全局变量
                   1. 在栈上创建:
                           在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限
                        1. 从堆上分配，亦称动态内存分配:
                               程序在运行的时候用malloc或new申请任意多少的内存，程序员自己负责在何时用free或delete释放内存。动态内存的生存期由我们决定，使用非常灵活，但问题也最多。
## 内存操作 ##
 1. 给绝对地址进行赋值操作


    1.法一：
    int *ptr;
    ptr = (int *)0x10000;
    *ptr = 1234;
    2.晦涩的方法是:(建议使用第一中)
    (unsigned int*)0x100000 = 1234;

 1. 程序跳转到绝对地址执行


       1.//首先要将0x100000强制转换成函数指针,即:
       (void (*)())0x100000
       //然后再调用它:
       *((void (*)())0x100000)();
    
       2.用typedef可以看得更直观些:
       typedef void(*)() voidFuncPtr;
       *((voidFuncPtr)0x100000)();

## 利用位运算实现加法 ##

    int Add(int a, int b)
    {
        if(b == 0)
            return a;
        int sum,carry;
        sum = a ^ b;
        carry = (a & b) << 1;
        return Add(sum, carry);
    }

## 判断计算机是大端存储还是小端存储 ##

    void checkCPU()
    {
        int i = 0x11223344;
        char *p;
        p = (char *)&i;
        if(*p == 0x44)
        {
            printf("小端存储\n");
        }else{
            printf("大端存储\n");
        }
    }

---
## Socket编写步骤 ##
服务器程序编写步骤：

 

- 调用socket()函数创建一个用于通信的套接字
- 给已经创建的套接字绑定一个端口号，这一般通过设置网络套接口地址和调用bind()函数来实现。
- 调用listen()函数使套接字成为一个监听套接字
- 调用accept()函数来接受客户端的连接，这是就可以和客户端通信了。
- 处理客户端的连接请求。read()/write()
- 终止连接 close


客户端程序编写步骤：

- 调用socket()函数创建一个用于通信的套接字
- 通过设置套接字地址结构，说明客户端与之通信的服务器的IP地址和端口号
- 调用connect()函数来建立与服务器的连接。
- 调用读写函数发送或者接收数据。
- 终止连接。

---

# 				数据结构

---

## 树的基本操作 ##

- 定义


    typedef struct BNode  
    {  
      char value; 
      struct BNode *left;  
      struct BNode *right;  
    } Node,*pNode,**ppNode; 

   

- 获取二叉树叶子总数


    int GetLeafeNum(pNode root)  
    {  
        if(root==NULL)  //空节点叶子数为0  
        {  
            return 0;  
        }  
        else if(root->left==NULL&&root->right==NULL)  
        {  
           return 1;  
        }  
        return GetLeafeNum(root->left)+GetLeafeNum(root->right);  
    }  

- 获取二叉树的高度(左右子树最高高度+1)


    int GetHeight(pNode root)  
    {  
        if(root==NULL)  
        {  
            return 0;  
        }  
        // max(left,right)+1;  
        return (GetHeight(root->left)>=GetHeight(root->right)?(GetHeight(root->left)+1):(GetHeight(root->right)+1));  
    }  

 

- 交换左右子树


    void swap_tree(pNode root)  
    {  
        if(root==NULL)  
            return;  
        else  
        {  
            pNode temp=root->left;  
            root->left=root->right;  
            root->right=temp;  
            swap_tree(root->left);  
            swap_tree(root->right);  
        }  
    } 

## 链表的基本操作 ##

- 定义


    typedef struct student{
        int data;
        struct student *next;
    }node;

- 单链表逆置


    node *reverse(node *head)
    {
        node *p1,*p2,*p3;
        if(head == null || head->next == null)
            return head;
        p1 = head;
        p2 = p1->next;
        while(p2){
            p3 = p2->next;
            p2->next = p1;
            p1 = p2;
            p2 = p3;
        }
        head->next = NULL;
        head = p1;
        return head;
    }

---
# 				STL 
---

## string库 ##

- string::npos
   1、std:: string ::npos的定义：


   static const size_t npos = -1;

表示 size_t 的最大值（ Maximum value for size_t ） ，如果对 -1 表示size_t的最大值
代码验证：

    #include <iostream>
    #include <limits>
    #include <string>
    using namespace std;
    int main()
    {
        size_t npos = -1;
        cout << "npos: " << npos << endl;
        cout << "size_t max: " << numeric_limits<size_t>::max() << endl;
    }//结果：4294967295
也就是说npos表示size_t的最大值

2、std::string::npos使用
    如果作为一个 返回值 （return value） 表示没有找到匹配项 

​    
    if(idx == string::npos)

​    
但是string::npos作为string的成员函数的一个 长度参数 时，表示“ 直到字符串结束 （until the end of the string）

    tmpname.replace(idx + 1, string::npos, "xxx"); //string::npos作为长度参数，表示直到字符串结束

- 将字符串内容反转  string::rbegin()/string::rend();


   string str ("now step live...");
   string::reverse_iterator rit;
   for ( rit=str.rbegin() ; rit < str.rend(); rit++ )
        cout << *rit;

- string::resize()
   resize函数用来改变string的大小，如果size小于来string的size大小，则截取前size个字符，如果大于，则用空格补充


   
