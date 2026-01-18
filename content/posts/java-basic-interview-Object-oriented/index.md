---
author: ["ChienTD"]
date: '2026-01-18T23:51:00+07:00'
title: 'Java Basic Interview Object Oriented'
description: 'Summary of common interview questions for Java basics - Object-oriented foundation'
summary: 'Tiếp tục series java interview. Phần này nói về Object-Oriented'
tags: ['java', 'interview', 'summary', 'basic', 'object-oriented']
categories: ['interview','java', 'object-oriented']
series: ['Java Interview']
ShowToc: true
TocOpen: true
---
# Object-oriented foundation

## What is the difference between member variables and local variables?

- Member variable: 
  - Thuộc về `class`
  - Khai báo trong `class` nhưng ngoài `method`
  - Nếu có `static` thì lưu trong Method Area (metaspace) và dùng chung cho các object
  - Nêu không có `static` thì thuộc về instance và lưu trong heap
  - Tồn tại cùng với object. Nếu là `static` thì tồn tại từ lúc class được load tới khi JVM kết thúc.
- Local variable:
  - Định nghĩa trong code blocks hoặc paramter của method
  - Lưu trong stack, gắn với stack frame của method
  - Tạo khi method được gọi, huỷ khi method kết thúc

## How is the static method different from the instance approach?
Static method vs Instance method  

## What is the difference between overloading and Overriding?

Overloading: -> cùng method name, khác danh sách tham số
```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

> **Note** Thứ tự ưu tiên trong overloading: Exact match  -> Widening (int -> long) -> Boxing (int -> Integer) -> Varargs
```java
void foo(long x) { }
void foo(Integer x) { }

foo(10); // gọi foo(long) (widening ưu tiên hơn boxing)
```
```java
void foo(Integer x) { }
void foo(int... x) { }

foo(10); // gọi foo(Integer) (boxing ưu tiên hơn Varargs)
```

Overriding -> Class con kế thừa và viết lại class cha
```java
void test(long x) {
    System.out.println("long");
}

void test(int x) {
    System.out.println("int");
}

test(10); // gọi test(int)
```
 - Kiểu trả về của method con phải giống hoặc hẹp hơn method cha. Exception range throw phải giống hoặc hẹp hơn method cha. Access modifier phải giống hoặc lớn hơn method cha.

///reference type, runtime type
