---
title: 无所不能的java反射
date: 2016-09-27 17:53:06
tags: [java,反射,动态修改]
---

## 前言

方寸惯例，本文不讲述基本的使用方法，

## 反射机制

### 类结构
一个类由方法、字段和参数组成：
- 类
    - 方法
        - 构造方法
        - 普通方法
        - 静态方法
    - 字段
        - 普通字段
        - 静态字段
    - 参数


#### 如何获取一个类
在 java 中，我们正常途径实例化一个类是通过 **new** 关键字，如：
```java
    Object obj = new Object();
```
那使用反射可以用：
```java
    Class<?> cls = Class.forName("java.lang.Object");
```

### 反射使用



###

## 动态代理

## 总结



## 参考
- [java反射详解](http://www.cnblogs.com/rollenholt/archive/2011/09/02/2163758.html)
