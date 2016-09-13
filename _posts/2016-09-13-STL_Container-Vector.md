---
author: GuoYS
layout: post
title:  "STL_Container_vector"
date:   2016-09-13 10:38:09 +0800
categories: Linux
tags: centos
---
* content
{:toc}

  C++中STL库中关于container的vector学习笔记




- vector初始化

  ```
      vector<int> first;                  // 空集合
      vector<int> second(4,100);          // 100,100,100,100
      vector<int> third(second.begin(),second.end());
      vector<int> fourth(third);

      int myints[] = {1,2,3,4};
      vector<int> five(myints,myints+sizeof(myints)/sizeof(int));
  ```
  
- assign()


   ` c.assign(beg,end)   //将[beg; end)区间中的数据赋值给c`

- at()

  ```
      //得到某一位置中对应的值
      for(int i=0;i<myints.size();++i)
          myints.at[i] = i;
      for(int i=0;i<myints.size();++i)
          cout<< myints.at(i);
  ```
- back()
  返回当前vector容器中末尾元素的引用

  ```
      myVector.push_back(10);
      while(myVector.back()!=0){
          myVector.push_back(myVector.back()-1);
      }
      //  10 9 8 7 6 5 4 3 2 1 0
  ```
- begin()/end()

  ```
    vector<int>::iterator it;
    for(it=myVector.begin();it<myVector.end;it++)
        cout<<*it;
  ```
  
- clear()
  
清空vector中的数据

- empty
 
 判断vector是否为空  vector.size() == 0

- erase()
 
 删除指向的元素

  ```
      for(int i=1;i<=10;i++);
          myVector.push_back(i);
      myVector.erase(myVector.begin()+5);  //删除第6个
      myVector.erase(myVector.begin(),myVector.begin()+3);//删除前三个
  ```
  
-front()

获得vector中第一个元素数据

- get_allocator()

   allocator是一个类,有着叫allocate()和deallocate()成员函数（相当于malloc和free）。它还有用于维护所分配的内存的辅助函数和指示如何使用这些内存的typedef（指针或引用类型的名字）。如果一个STL容器使用用户提供的allocator来分配它所需的所有内存(预定义的STL容器全都能这么做；他们都有一个模板参数，其默认值是std::allocator)，你就能通过提供自己的allocator来控制它的内存管理。

  ```
      vector<int>myints;
      int *p;
      p = myints.get_allocator().allocate(5);
      for(i=0;i<5;i++)p[i]=i;
      myints.get_allocator().deallocate();
  ```
  
- insert()

  ```
      方式一：
      vector<int>myvector(3,100); // 100 100 100
      vector<int>::iterator it;
      it = myvector.begin();
      it = myvector.insert(it,200); // 200 100 100 100
      方式二：
      myvector.insert(it,2,300); // 300 300 200 100 100 100
      方式三：
      vector<int> othervector(2,400);
      myvector.insert(it+2,othervector.begin(),othervector.end());
      // 300 300 400 400 200 100 100 100
      //插入数组
      int item[]={123,456,789};
      myvector.insert(myvector.begin(),item,item+3);
      // 123 456 789 300 300 400 400 200 100 100 100
  ```
- max_size()

  返回vector所能承受的最大个数

- operator=

  ```
      vector<int>first(5,0);
      vector<int>second(3,0);
      first = second;
      first = vector<int>()
      first.size()    结果为0
      second.szie()   结果为3
  ```
  
- operator[]

  ```
      vector<int>myvector(10);
      vector<int>::size_type sz = myvector.size();
      for(i=0;i<sz;i++)
          myvector[i]=i;  //0 1 2 3 4 5 6 7 8 9
      翻转：
      for(i=0;i<sz/2;i++)
      {
          int temp;
          temp = myvector[sz-1-i];
          myvector[sz-1-i] = myvector[i];
          myvector[i] = temp;
      }// 9 8 7  6 5 4 3 2 1 0
  ```
  
- push_back()/pop_back()

  push_back(x)：将x追加到vector末尾，长度加1
  pop_back():将vector最后一位元素弹出，长度减1

- rbegin()/rend()

  反向遍历vector

  ```
      vector<int>myvector;
      for(int i=1;i<=5;i++) 
          myvector.push_back(i);
      vector<int>::reverse_iterator rit;
      for(rit=myvector.rbegin();rit<myvector.rend();rit++)
              cout<<*rit
      // 5 4 3 2 1
  ```
  
- resize()

  改变当前使用数据的大小，如果它比当前使用的大，者填充默认值0
  
- size()

    表示数组中元素的多少
    
- capacity()

    表示数组有多大的容量

- swap

  两个vector相互交换数据

  ```
      vector<int> first(3,100);
      vector<int> second(5,200);
      fisrt.swap(second);
      first: 200 200 200 200 200
      second:100 100 100
  ```
  
- reserve()

  - 当我们知道容器中存放元素的大致数量时，可以通过reserve()方法减少系统对容器进行资源再分配的次数；

  - 当我们能够确定容器中存放元素的最大数量后，可以通过reserve()修剪超额资源，减少不必要开销




