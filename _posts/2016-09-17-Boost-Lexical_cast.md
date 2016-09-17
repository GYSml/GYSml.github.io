---
author: GuoYS
layout: post
title:  "Boost组件之lexical_cast"
date:   2016-09-17 10:38:45 +0800
categories: Boost
tags: lexical_cast
---
* content
{:toc}

  在C++中Boost里面的lexical_cast实现了数值与字符串之间的转换，使其更加方便与简洁








## 字符串→数值

- 如何将字符串"123"转换为int类型整数123？答案是，用标准C的库函数atoi
- 如果要转换为long类型呢？标准C的库函数atol
- 如何将"123.12"转换为double类型呢？标准C的库函数atod
- 如果要转换为long double类型呢？标准C的库函数atold

C++:

  ```
    #include <boost/lexical_cast.hpp>
    #include <iostream>
    int main()
    {
            using boost::lexical_cast;
            int a = lexical_cast<int>("123");
            double b = lexical_cast<double>("123.12");
            std::cout<<a<<std::endl
            std::cout<<b<<std::endl;
            return 0;
    }
  ```



## 数值→字符串

  sprintf()函数：

  ```
  char item[100];
  sprintf(item,"%s",12345.678);
  ```

  C++标准字符串string:

  ```
  #include <boost/lexical_cast.hpp>
  #include <string>
  #include <iostream>
  int main()
  {
          using std::string;
          const double d = 123.12;
          string s = boost::lexical_cast<string>(d);
          std::cout<<s<<std::endl;
          return 0;
  }
  ```

## 异常

  如果转换失败，则会有异常bad_lexical_cast抛出。该异常类是标准异常类bad_cast的子类

  ```
  #include <boost/lexical_cast.hpp>
  #include <iostream>
  using namespace td;
  int main()
  {
          int i;
          try{
                  i = boost::lexical_cast<int>("abcd");
          }
          catch(boost::bad_lexical_cast& e)
          {
                  cout<<e.what()<<endl; 
                  return 1;
          }
          cout<<i<<endl;
          return 0;
  }
  ```

  异常消息：bad lexical cast: source type value could not be interpreted as target

- 注意事项

  lexical_cast依赖于字符流std::stringstream

  - 原理

    把源类型读入到字符流中，再写到目标类型中

    ```
    int m = boost::lexcal_cast<int>("123456");
    ```

    等价：
    ```
    int item;
    std::stringstream ss;
    ss<<"123";
    s>>item;
    ```

  - 输入数据必须“完整”地转换，否则抛出bad_lexical_cast异常

    ```
    int a = boost::lexical_cast<int>("123.456");
    ```

    会将123.456强制转成123，导致部分转换

  - 浮点数精度问题

    ```
    string item = boost::lexical_cast<string>("123.456789")
    ```

    理论值等于“123.456789”,实际值只有“1233.456”，默认情况下std::stringstream精度6位，打开头文件<boost/lexical_cast.hpp>修改

    ```
     interpreter.precision(std::numeric_limits<Source>::digits10);
    ```



