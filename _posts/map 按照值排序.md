
---
layout:     post                    # 使用的布局（不需要改）
title:      map 按照值排序               # 标题 
date:       2018-05-30              # 时间
author:     GR                      # 作者
header-img: img/post-bg-rwd.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - C++
---


虽然是个比较简单的问题，还是记录一下，以免以后又忘记了又浪费时间去百度。

## **严格的说在map、multimap并不能按照值排序，只能按照键排序** ##

在STL库里面map和multimap是有序排列的容器，比较器默认使用`less<T>`，而且规定了只能使用键来排序，当然我们可以自己定义[函数对象/仿函数/谓词][]来比较，但是比较的也只键。默认的定义如下：
```C++
template < class Key,                                     // map::key_type
           class T,                                       // map::mapped_type
           class Compare = less<Key>,                     // map::key_compare
           class Alloc = allocator<pair<const Key,T> >    // map::allocator_type
           > class map;

template < class Key,                                     // multimap::key_type
           class T,                                       // multimap::mapped_type
           class Compare = less<Key>,                     // multimap::key_compare
           class Alloc = allocator<pair<const Key,T> >    // multimap::allocator_type
           > class multimap;           
```

## 一定要排序的变通方式：转换到其他容器 ##

**使用`vector`**：使用`vector<pair<key, value> >`来存储，然后在对`vector`进行排序，此时需要自己定义函数对象来排序，因为`pair`实现的`<`是首先按照第一个参数来比较的，只有第一个参数相等才比较第二个参数。如果不想自己实现函数对象的话，可以存储为`vector<pair<value, key> >`就可以使用`<functional>`里面的函数对象模板进行排序。

**使用`multimap`**：将`map<key,value>/multimap<key, value>`交换键和值存储为`multimap<value, key>`(从小到大排序)即可，如果对于排序顺序有要求可以在定义的时候自己修改比较器，例如：
`multimap<value, key, greater<value> >`就会从大到小排序。

**不能使用map交换键-值来进行排序**：在map里面键是唯一的不能重复，但是值有可能重复，如果值有重复的，交换键-值之后，容器的大小就不一样了。



[C++ STL中Map的按Key排序和按Value排序](https://www.cnblogs.com/lakeone/p/5599047.html)

[函数对象/仿函数/谓词]:https://blog.csdn.net/u011711479/article/details/77620120

