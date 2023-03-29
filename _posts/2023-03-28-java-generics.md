---
title: Java泛型理解
date: 2023-03-28
categories: [Java]
tags: [java, generics]

author: <author_id>

math: true
mermaid: true
pin: true
---



## <? extends T>

```java
List<? extends Number> list = new ArrayList<Number>();
List<? extends Number> list = new ArrayList<Integer>();
List<? extends Number> list = new ArrayList<Double>();
```

- **定义**

    创建对象时，类型可以是`T`以及`T`的子类

- **读取**

    由于可以用父类引用子类，因此可以用`T`接收读取的类型（**明确可读**）

- **写入**

    不可写入，会报编译错误

## <? super T>

```java
List<? super Integer> list = new ArrayList<Integer>();
List<? super Integer> list = new ArrayList<Number>();
List<? super Integer> list = new ArrayList<Object>();
```

- **定义**

    创建对象时，类型可以是`T`以及`T`的父类

- **读取**

    虽然不能确定存储的类型，但Object是所有java类型的父类，所以可以用Object接收

- **写入**

    可写入Integer类型（**明确可写**）
