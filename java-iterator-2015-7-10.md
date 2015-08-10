title: java-iterator-2015-7-10
date: 2015-07-10 15:11:52
tags: [java,数据结构]
categories: code
---
##Iterator迭代器##
###什么是Iterable？###
有一个返回一个Ierator的方法
```
//Iterable interface
public interface Ierable<Item>
{
	Iterator<Item> iterator();
}
```
###什么是Ierable？###
实现了hasNext()和next()方法,remove()是可选的。
###为什么要让数据结构Iterable?###
可以写出优美简介的代码。