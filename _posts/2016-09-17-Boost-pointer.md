---
author: GuoYS
layout: post
title:  "Boost之智能指针"
date:   2016-09-17 22:48:25 +0800
categories: Boost
tags: pointer
---
* content
{:toc}

  关于boost中智能指针特点及Demo实例


#  Boost智能指针

## shared_ptr< T >

- 功能

  内部维护一个引用计数器来判断此指针是不是需要被释放，是boost中最常用的智能指针了

- 特点

  - 在内部维护一个引用计数器， 当有一个指针指向这块内存区域是引用计数+1， 反之-1， 如果没有任何指针指向这块区域， 引用计数器为0，释放内存区域。


- 可以共享和转移所有权。


- 可以被标准库的容器所使用


- 不能指向一块动态增长的内存(用share_array代替)

- Demo

  ```
  #include<boost\shared_ptr.hpp>
  class Test
  {
  public:
  	Test(){
  		count++;
  	}
  	~Test(){
  		count--;
  	}
  	void print(){
  		cout << "引用计数值为:" << count<<endl;
  	}
  private:
  	static int count;
  };

  int Test::count = 0;
  int main()
  {
  	boost::shared_ptr<Test> A(new Test);
  	A->print();
  	boost::shared_ptr<Test> B(new Test);
  	B->print();
  	return 0;
  }
  ```



## scoped_ptr< T >

- 功能

  当这个指针的作用域消失之后自动释放，相当于作用域只在{}中，不能做参数


- 特点

  - 该指针的效率和空间的效率和空间的消耗内置的指针差不多。
  - 该指针不能用在标准库的容器上。(用shared_ptr代替)
  - 该指针不能指向一块能够动态增长的内存区域(用scoped_array代替)

- Demo

  ```
  #include <boost\scoped_ptr.hpp>
  class Test{
    public:
    	void print(){
    		cout<<"test scoped_ptr pointer"<<endl;
  	}
  };
  int main(){
    boost::scoped_ptr<Test> A(new Test);
    A->print();
  }
  ```


## intrusive_ptr< T >

- 功能

  同样维护一个引用计数器，比shared_ptr有更好的性能。但是要求T自己提供这个计数器



## weak_ptr< T >

- 功能

  弱指针，要和shared_ptr 结合使用，weak_ptr 被shared_ptr控制， 它可以通过share_ptr的构造函数或者lock成员函数转化为share_ptr

- 特点

  weak_ptr的一个最大特点就是它共享一个share_ptr的内存，但是无论是构造还是析构一个weak_ptr 都不会影响引用计数器。

- Demo

  ```
  #include <boost\weak_ptr>
  Test类为shared_ptr中类一致
  int main()
  {
  	boost::shared_ptr<Test> A(new Test);
  	A->print();
  	boost::shared_ptr<Test> B(new Test);
  	B->print();
  	A->print();
  	boost::weak_ptr<Test> weakPtr(A);
  	boost::shared_ptr<Test> C = weakPtr.lock();
  	if (C)
  		C->print();
  	return 0;
  }
  ```




##  shared_array< T >

- 功能

  和shared_ptr相似，但是访问的是数组

- 特点

  该指针可以访问数组形式(new [])

- Demo

  ```
  #include<boost\shared_array>
  int main{
  	const int size = 10;   
  	boost::shared_array<test> a(new Test[]);  
  	for (int i = 0; i < size; ++i)  
  	     a[i].print();  
  	return 0;
   }
  ```

## scoped_array< T >

- 功能

  和scoped_ptr相似，但是访问的是数组

- Demo

  ```
  #include<boost\scoped_array>
  int main{
  	const int size = 10;   
  	boost::shared_array<test> a(new Test[]);  
  	for (int i = 0; i < size; ++i)  
  	     a[i].print();  
  	return 0;
   }
  ```
