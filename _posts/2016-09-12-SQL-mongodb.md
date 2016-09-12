---
author: GuoYS
layout: post
title:  "MongoDB"
date:   2016-09-12 20::28:16 +0800
categories: MongoDB SQL
tags: SQL
---
* content
{:toc}


这是最近使用mongodb的一些学习笔记,也遇到了一些问题，以后会在这里陆续更新



## 启动MongoDB
- 给MongoDB指定文件夹db,用来存放MongoDB数据
- 在cmd指定path路径

```
    c:\Users\Administraror>D:
    D:\>cd mongodb\bin
    D:\mongodb\bin>mongod --dbpath=D:\mongpdb\db
```

- 验证是否开启成功：默认端口27017，在浏览器输入http://localhost:27017/测试

## MongoDB三元素

- 数据库
- 集合 -> 表
- 文档 -> 行

## 基本操作  

- 启动
   win+R 打开cmd,进入D:\mongodb\bin,输入mongo命令打开shell,shell就是mongodb的客户端，也是一个JS的编译器，默认连接"test"数据库
  注意：下次用同样方式启动时，在db文件下有lock file文件，删除即可

- insert()

```
    一般写法:
    db.student.insert({"name":"zhangsan","age":"19"})
    db.student.insert({"name":"lisi","age":"20"})
    js语法
    var data={"name":"zhangsan","age":20,"address":{"province":"jiangxi","city":"nanchang"},"class":["Java","SQL"]}
    db.student.insert(data)
    data.name="lisi"
    data.age=25
    data.address={"province":"zhejiang","city":"hangzhou"}
    data.class=["C","C++"]
    db.student.insert(data)
```
    
- find()

  - 比较操作>=,>, <, <=, !=, =

    ```
    db.student.find({"name":"lisi"});
    //find age>22
    db.student.find({"age":{$gt:23}});
    //find age<22
    db.student.find({"age":{$lt:23}});
    //find age!=22
    db.student.find({"age":{$ne:23}});
    //find age==22
    db.student.find({"age":23});  
    ```

    ​

  - 无关键字查询：

    ```
    //find name='zhangsan' && province="jiangxi"
    db.student.find({"name":"zhangsan","address.provice":"jiangxi"})
    //find province='jiangxi' || 'beijing'
    db.student.find({$or:[{"address.province":"jiangxi"},{"address.province":"beijing"}]})
    //find province in ["jiangxi","beijing"]
    db.student.find({"address.province":$in:["jiangxi","beijing"]})
    //find province not in ["jiangxi","beijing"]
    db.student.find({"address.province":$nin:["jiangxi","beijing"]})
    ```

  - 正则表达式

    ```
          find name start z end n ->zhangsan
          db.student.find({"name":/^z/,"name":/n$/})
    ```
    
- $where语句

  ```
  find name='zhangsan'
  db.student.find({$where:function(){return this.name=='zhangsan'}})
  ```

- update()
   第一个参数为查找条件，第二个则为更新的值

  - 整体更新

    ```
        //整体更新：普通方式
        db.student.update({"name":"age"},{"name":"lisi","age":"18"});
        //js更新
        var example=db.student.findOne({"name":"zhangsan"})
        example.age = 30
        db.student.update({"name":"zhangsan"},example)
    ```


- 局部更新
  - $inc  增加指定值
 
    ```
        db.student.update({"name":"zhangdan"},{$inc:{"age":10}})   结果  age = 40
    ```

  - $set 

    ```
          db.student.update({"name":"zhangdan"},{$inc:{"age":10}})   结果  age = 10
    ```

  - upsert操作
如果没有查到，就在数据库里面新增一条
好处：避免在数据库里面判断是update还是add操作，单将update的第三个参数设为true即可。

    `db.student.update({"name":"wangwu"},{$inc:{"age":17}},true)`
    增加新纪录： name:wangwu age:17

  - 批量更新
    如果匹配多条，默认的情况下只更新第一条，如果我们有批量更新在update的第四个参数中设为true即可

    `db.student.update({"name":"wangwu"},{$inc:{"age":19}},true，true)`
​    

- remove()

    注意：remove()不带参数则删除所有数据，该操作为不可撤回操作
    
   ` db.student.remove({"name:joe"});`


## 高级操作

- 聚合  
  常见的聚合操作：count，distinct，group，mapReduce


- count

    ```
  //统计表中行数
  db.student.count()
  //统计age=20的行数
  db.student.count("age":20)
    ```

- distinct  

    功能：指定了哪列，哪列就不能重复
    `db.student.distinct({"age"})`

- group

参数介绍：key:分组的key,这里指年龄

          initial：每组都有一个初始化函数
          $reduce:第一个参数为当前文档对象，第二个为上一次function操作的累计对象
可选参数：condition:  这个就是过滤条件。
finalize函数：每一组文档执行完后，多会触发此方法，那么在每组集合里面加上count也就是它的功能。

    db.student.group({
        "key":{"age":true},
        "initial":{"student":[]},
        "$reduce":function(cur,prev){
            prev.student.push(cur,prev);
        }
        })


增加功能：过滤age>20的人
          有时student数组中数据多，加count标识
   

    db.student.group({
        "key":{"age":true},
        "initial":{"student":[]},
        "reduce":function(cur,prev){
            prev.student.push(cur,prev);
        }，
        "finalize":function(prev){
            prev.count = prev.person.length;
        },
        "condition":{"age":{$lt:25}}
        })

- mapReduce

​    

    1.map:称为映射函数，里面调用emit(key,value),按照key进行映射分组
    2.reduce：称为简化函数，会对map分组后的数据进行简化，reduce(key,value)中的key就是emit中的key，value就是emit分组后的emit(value)的集合
    3.mapReduce：执行函数，参数为map,reduce和可选参数


    >map
    function(){
        emit(this.name,{count:1});
    }
    >reduce
    function(){
        var result = {count:0};
        for(var i=0;i < value.length;i++){
            result.count += value[i].count;
        }
        return result;
    }
    >db.student.mapReduce(map,reduce,{"out":"collect_value"})

看collect_value中的结果：

    db.collect_value.find()



- 游标
- 全部查询


    var list=db.student.find();
    遍历：
    lsit.forEach(function(x){print(x.name);
    })
    while(list.hasNext()){
        var dox = list.next();
    }


针对这样的操作，list其实并没有获取到person中的文档，而是申明一个“查询结构”，等我们需要的时候通过for或者next()一次性加载过来，然后让游标逐行读取，当我们枚举完了之后，游标销毁，之后我们在通过list获取时，发现没有数据返回了

​    

- 分页、排序查询

​    
limit是限制游标返回的数量，指定了上限；
skip是忽略前面的部分文档，如果文档总数量小于忽略的数量，则返回空集合；
sort对得到的子集合进行排序，可以按照多个键进行正反排序

    var single=db.person.find().sort({"name",1}).skip(2).limit(2);


## 索引

- 插入1w条数据


    db.student.remove();
    for(var i=0;i<10000;i++)
        db.student.insert({"name":"abc"+i,"age":i});

- 性能分析函数(explian)
  在这里没有建立索引，直接查询abc10000的姓名


    db.student.find({"name":"abc"+10000}).explain();

参数介绍：
    cursor:BasicCursor表示表查找，也就是顺序查找
    nscanned:表示数据库浏览文档的个数(10000)
    n:表示最终返回一个文档
    milis:总共耗时114ms

- 建立索引


    db.student.ensureIndex({"name":1})
    db.student.find({"name"+10000}).explain()
    
    cursor:BTreeCursor ,MongDB采用B树的结构存放索引
    nscanned:数据库只浏览一个
    n：直接定位返回

- 唯一索引  
  作用：唯一的键值不能插入


    db.student.remove()
    db.student.ensureIndex({"name":1},{"unique":true})

- 组合索引


    多条件查找：
    db.student.ensureIndex({"name":1,"birthday":1})
    db.student.ensureIndex({"birthday":1,"name":1})

使用getindexes查看生成的索引：

    db.student.getIndexes()
    结果有：name_1_birthday_1、birthday_1_name_1
    
    不过查询优化器会选择最优的索引查询

也可以使用hint方法强制执行

    db.student.find({"birthday":"1995-02-07","name":"gys"}).hint({"birthday":1,"name":1})

- 删除索引
  查看索引


    db.student.getIndexes()
    db.student.dropIndexes("name_1");

## 主从复制

- 原理

从数据库 》》 主数据库  《《 从数据库

- 好处
  - 数据备份
  - 数据恢复
  - 读写分离


- 在一台机器模拟主从复制
  - 把MongDB复制一份到E盘，模拟多服务器
  - 启动D盘MongDB，指定为主数据库




    >mongod --dpath=D:\mongdb\db --master
- 同样的方式启动E盘下的MongDB，指定该数据库为从属数据库，需换端口


    >mongod --dpath=E:\mongdb\db --port 8888 --slave --source=127.0.0.1:27017

4.寻找"applied 1 operations"

测试：

    分别执行 db.student.find()

- 在增加一台从数据库


- 读写分离


## 副本集

- 和主从复制的区别：
  - 该集群没有特定的主数据库
  - 如果主数据宕机了，集群自动选择一个从数据库作为主数据库，实现自动故障恢复功能


- 实现步骤
  - 设置集群名gys,--replSet表示让服务器知道gys下还有其他数据库，把D盘的MongoDB启动，   端口1111，知道端口6666是gys集群下的另一个数据库服务器

    ```
    mongod --dpath=D:\mongdb\db --port 1111 --replSet gys/127.0.0.1:6666
    ```

  - 启动6666端口数据库 

    ```
    >mongod --dpath=E:\mongdb\db --port 6666 --replSet gys/127.0.0.1:1111
    ```

  - 初始化集群：随便连接一下哪个服务器都行，不过一定要进入admin

    ```
     mongo 127.0.0.1:1111/admin
    ```

  - 开启成功后，可以看到端口为1111的已经成为主数据库服务器

  - F盘的mongodb作为仲裁服务器，然后指定shopex集群中的任一个服务器端口-1111,admin集合中使用rs.addArb()追加即可

    ```
    mongo  127.0.0.1:1111/admin
    PRIMARY> rs.addArb("127.0.0.1:4444")
    ```

    查看状态：
    `rs.status()`



















