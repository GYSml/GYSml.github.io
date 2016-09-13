---
author: GuoYS
layout: post
title:  "STL_Container_map"
date:   2016-09-13 17:29:20 +0800
categories: C++
tags: STL 
---
* content
{:toc}

  C++中STL库中关于container的map学习笔记
  
  
  
  
  
  
- 说明
   map内部本身就是按序存储的（比如红黑树）。在我们插入<key,value>键值对时，就会按照key的大小顺序进行存储。这也是作为key的类型必须能够进行<运算比较的原因

- begin()/end()


    map<char,int> mymap;
    map<char,int>::iterator it;
    mymap['a']=100;
    mymap['b']=200;
    mymap['c']=300
    
    for(it=mymap.begin();it<mymap.end();it++)
        cout<< (*it).first << (*it).second<<endl;
    
    //不使用iterator遍历见empty()

  ​      

- clear()
  清空map中的数据

- count()
  查看map中是否存在某个key值,无法定位数据位置


    map<char,int> mymap;

    mymap ['a']=101;
    mymap ['c']=202;
    mymap ['f']=303;
    
    mymap.count('a')  返回1
    mymap.count('x')  返回0

- empty()

返回0 表示空


    //不使用iterator遍历map
     while (!mymap.empty())
     {
         cout << mymap.begin()->first << " => ";
         cout << mymap.begin()->second << endl;
         mymap.erase(mymap.begin());
     }

- equal_range()

lower_bound返回区间A的第一个迭代器，算法upper_bound返回区间A的最后一个元素的下一个位置，算法equal_range则是以pair的形式将两者都返回

    map<char,int> mymap;
    pair<map<char,int>::iterator,map<char,int>::iterator> ret;
    
    mymap['a']=10;
    mymap['b']=20;
    mymap['c']=30;
    
    ret = mymap.equal_range('b');
    
    cout << "lower bound points to: ";
    cout << ret.first->first << " => " << ret.first->second << endl;
    // 'b' => 20
    cout << "upper bound points to: ";
    cout << ret.second->first << " => " << ret.second->second << endl;
    // 'c' =>30

- erase()


    map<char,int> mymap;
    map<char,int>::iterator it;
    mymap['a']=10;
    mymap['b']=20;
    mymap['c']=30;
    mymap['d']=40;
    mymap['e']=50;
    mymap['f']=60;
    //按位置删除
    it = mymap.find('b');
    mymap.erase(it);
    //按关键字删除
    mymap.erase('c');
    //按区间删除
    it = mymap.find('e');
    mymap.erase(it,mymap.end());

- find()
  查找关键字返回对应位置


    it = find('c');

- get_allocator()


    int psize;
    map<char,int> mymap;
    pair<const char,int>* p;
    p = mymap.get_allocator().allocate(5);
    
    psize = (int)sizeof(map<char,int>::value_type)*5; 
    mymap.get_allocator().deallocate(p,5);

- insert()


    map<char,int>mymap;
    map<char,int>::iterator it;
    pair<map<char,int>::iterator,bool> ret;
    
    //插入方式1，单个参数
    mymap.insert(pair<char,int>('a',100));
    mymap.insert(pair<char,int>('z',200));
    ret = mymap.insert(pair<char,int>('z',500));//返回false
    //插入方式二,iterator定位
    it = mymap.begin();
    mymap.insert(it,pair<char,int>('b',300));
    mymap.insert(it,pair<char,int>('c',400));
    //插入方式三
    map<char,int> othermap;
    othermap.insert(mymap.begin(),mymap.find('c'));


- key_comp()
  关键字比较


    map<char,int>::key_compare mycomp;
    char highest;
    mycomp = mymap.key_comp();
    highest = mymap.rbegin()->first;
    用法：
    it=mymap.begin();
    do {
         cout << (*it).first << " => " << (*it).second << endl;
    } while ( mycomp((*it++).first, highest) );

- lower_bound()/up_bound()
  lower_bound:直向当前值
  up_bound:指向当前的下一个值



    map<char,int> mymap;
    map<char,int>::iterator it,itlow,itup;
    
    mymap['a']=20;
    mymap['b']=40;
    mymap['c']=60;
    mymap['d']=80;
    mymap['e']=100;
    
    itlow=mymap.lower_bound ('b');  // itlow points to b
    itup=mymap.upper_bound ('d');   // itup points to e (not d!)

- max_size()
  返回map容器所支持的最大元素数量

- operator=


    map<char,int> first;
    map<char,int> second;
    
    first['x']=8;
    first['y']=16;
    first['z']=32;
    
    second=first;          second.size() = 3
    first=map<char,int>();  first.size() = 0

- operator[]


    map<char,string> mymap;
    map<char,string>::iterator it;
    
    mymap['a']="an element";
    mymap['b']="another element";
    mymap['c']=mymap['b'];    mymap['c']  也是"another element"


- rbegin()/rend()
  反向遍历map


- size()
  返回map中数据条目的数量

- swap()

两个map之间的数据交换

- value_comp()


      map<char,int> mymap;
      map<char,int>::iterator it;
      pair<char,int> highest;
    
      mymap['x']=1001;
      mymap['y']=2002;
      mymap['z']=3003;
    
      cout << "mymap contains:\n";
    
      highest=*mymap.rbegin();          // last element
    
      it=mymap.begin();
      do {
        cout << (*it).first << " => " << (*it).second << endl;
      } while ( mymap.value_comp()(*it++, highest) );


- 自定义compare()函数


    struct CmpByKeyLength {  
      bool operator()(const string& k1, const string& k2) {  
        return k1.length() < k2.length();  
      }  
    };
    
    使用：
    map<string,int,CmpByKeyLength>mymap;
    功能：map会按照key关键字的长度进行排序

