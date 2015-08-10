title: 看看java是如何分配内存的
date: 2015-01-30 09:59:53
tags: java
categories: code
---
1. ##java各类型占用的内存##
 Primitive Type       Memory Required(bytes) 
 boolean                 1
 byte                        1
short                       2
char                        2
int                           4
float                        4
long                        8
double                    8
2. ##64位虚拟机下java对象与数组的内存分配##  
 **Object reference(对象引用,因为地址是64位的) : 8bytes**  
*64位虚拟机下地址用64位标记,所以对象引用需要8bytes, 当对象中内嵌对象时会出现这种对象引用*  
 **Array:24bytes+memory for each array entry(数组后通常要加8bytes的引用)**
 *因为也是需要地址来指向数据真正存放的位置*
 **Object(对象):16bytes+memory for each+8(如果有内嵌的类)**
 *对象需要附加的16bytes*
**Padding: 对齐8bytes(64位)**
  *别忘了对齐* 