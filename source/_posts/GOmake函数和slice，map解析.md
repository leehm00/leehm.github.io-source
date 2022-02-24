---
title: GOmake函数和slice，map解析
top: false
cover: false
toc: true
mathjax: true
tags:
  - GO
categories:
  - 学习
typora-root-url: ../
date: 2022-02-21 09:51:37
password:
summary:
---

# make函数

- 首先看个例子



```go
package main
import (
 "fmt"
)
func main() {
 var i *int
 *i=10
 fmt.Println(*i)
}
```

- 这个例子会打印出什么？0还是10?。以上全错，运行的时候会painc，原因如下：



```go
panic: runtime error: invalid memory address or nil pointer dereference
```

- 从这个提示中可以看出，对于引用类型的变量，我们不光要声明它，还要为它分配内容空间，否则我们的值放在哪里去呢？这就是上面错误提示的原因
- 对于值类型的声明不需要，是因为已经默认帮我们分配好了
- 分配内存，Go提供了两种方式，分别是new和make

## new

Go提供内建函数new



```swift
func new(Type) *Type
```

- 它只接受一个参数，这个参数是一个类型，分配好内存后，返回一个指向该类型内存地址的指针。同时请注意它同时把分配的内存置为零，也就是类型的零值。那么上面的函数可以改写成



```go
func main() {
 var i *int
 i=new(int)
 *i=10
 fmt.Println(*i)
}
```

- 这就是new，它返回的永远是类型的指针，指向分配类型的内存地址

## make

- make也是用于内存分配的，但和new不同，它只用于通道chan、映射map以及切片slice的内存创建
- 它的返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型本身就是引用类型
- 注意，因为这三种类型是引用类型，所以必须得初始化，但不是置为零值



```swift
func make(t Type, size ...IntegerType) Type
```

## make和new对比

- 二者都是内存的分配（堆上），但是make只用于slice、map以及channel的初始化（非零值）；而new用于类型的内存分配，并且内存置为零
- make返回的还是这三个引用类型本身；而new返回的是指向类型的指针
- new不常用，通常都是采用短语句声明以及结构体的字面量达到我们的目的，比如：



```go
i:=0
u:=user{}
```

- make函数是无可替代的

# 切片slice

- Slice和数组类似，也是表示一个有序元素，但这个序列的长度可变，具有动态长度特性
- 切片（slice）是对底层数组一个连续片段的引用，所以切片是一个引用类型
- 内部实现的数据结构通过指针引用底层数组，设定相关属性将数据读写操作限定在指定的区域内
- 切片本身是一个只读对象，其工作机制类似数组指针的一种封装
- 多个切片可以指向同一个底层数组，实现了内存共享
- Slice 的数据结构定义如下:



```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```

- 切片的结构体由3部分构成，Pointer 是指向一个底层数组的指针，len 代表当前切片的长度，cap 是当前切片的容量，cap 总是大于等于 len 的

## 切片的使用

- Go语言提供的内置函数make()可以用于灵活地创建数组切片
- 创建方式make([]type, len, cap)，其中，type表示数组元素类型，len表示长度，cap表示容量
- 还有一种切片字面量方式创建切片



```go
//创建一个初始元素长度为5的数组切片，元素初始值为0： 
mySlice1 := make([]int, 5) 
//创建一个初始元素长度为5的数组切片，元素初始值为0，并预留10个元素的存储空间： 
mySlice2 := make([]int, 5, 10) 
//切片字面量创建长度为5容量为5的切片,需要注意的是 [ ] 里面不要写数组的容量，因为如果写了个数以后就是数组了，而不是切片了。
mySlice3 := []int{10,20,30,40,50}
```

# 映射map

- map是一种数据结构，用于存储一系列无序的键值对，类似java的HashMap
- 通过散列表实现，使用两个数据结构来存储数据，一个数组用于选择桶的散列键的高八位值，可以区分每个键值属于哪个桶；另一个字节数组，用于存储键值对
- 创建方式make(map[keyType] valueType, cap)，其中keyType表示键类型，valueType表示值类型，cap表示初始存储能力



```go
//创建了一个键类型为string、值类型为PersonInfo
myMap = make(map[string] PersonInfo) 
//也可以选择是否在创建时指定该map的初始存储能力，创建了一个初始存储能力为100的map.
myMap = make(map[string] PersonInfo, 100) 
//创建并初始化map的代码.
myMap = map[string] PersonInfo{ 
  "1234": PersonInfo{"1", "Jack", "Room 101,..."}, 
} 
```

# 通道channel

- channel的内容在《浅谈Go并发编程》中已经介绍过了 不累赘
- 创建方式make(chan type, cap)，其中type表示通道数据类型，cap表示缓存容量



```go
//创建有缓存通道
ch := make(chan int, 10)
//创建无缓存通道
ch := make(chan int)
```

转自[Go内建函数make及切片slice、映射map详解](https://www.jianshu.com/p/f01841004810)
