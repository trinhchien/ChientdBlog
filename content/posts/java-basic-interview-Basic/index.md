---
author: ["ChienTD"]
date: '2026-01-18T17:19:48+07:00'
title: 'Java Interview - Basic concepts and common sense'
description: 'Summary of common interview questions for Java basics - Basic concepts and common sense'
summary: 'Phần này nói về các khái niệm chung và cơ bản trong java'
tags: ['java', 'interview', 'summary', 'basic']
categories: ['interview','java']
series: ['Java Interview']
ShowToc: true
TocOpen: true
---

# Basic concepts and common sense

## JVM vs JDK vs JRE

### JVM

JVM (Java Virtual Machine) -> Là virtual machine dùng để chạy Java bytecode.  
Implement của JVm trên các hệ diều hành (Windows, Linux, macOS) là khác nhau.  
File `.java` được javac biên dịch (compile) thành file `.class` chứa bytecode. Bytecode này được `JVM` thực thi

```text
.java (source code)
   ↓ javac (compiler)
.class (bytecode – platform independent)
   ↓ JVM
Machine code (native, platform dependent)
```

Có nhiều JVM khác nhau do các tổ chức/cá nhân phát triển tuân theo rule thống nhất để chạy nhưng có thể khác nhau về tối ưu trong GC, JIT, Memory usage, Startup time, Cloud suitability.

### JDK and JRE

JDK (Java Development Kit) => Bộ công cụ phát triển ứng dụng java => Dùng để tạo và compile java.  
JDK bao gồm JRE (Java Runtime Environment), javac và các công cụ khác (javadoc, jdb, jconsole, javap, ...)  
JRE là môi trường runtime để chạy ứng dụng java bao gồm: JVM và các thư viện Java basic khác.  

![JDK-overview](jdk-overview.png)

> **Note:**  
>- Từ java 9, có thể dùng jlink để tạo Java runtime tối giản (đóng gói ứng dụng java cùng JVM + module cần thiết). Không cần thiết phải có đầy đủ JRE  
> - Từ java 11, Oracle không cho phép download JRE riêng lẻ.


### What is bytecode? What are the benefits of adopting bytecode?

![JVM-model](jvm-model.png)

### Why is it said that the Java language "compiles and interprets"?

### What are the advantages of AOT? Why not use AOT altogether?

`AOT (Ahead of Time Compilation)` xuất hiện từ JDK 9 -> compile code sang machine code hoàn toàn -> tránh được long warm-up times. Ngoài ra còn có thể giảm memory footprint (bỏ JIT -> bỏ cache của JIT) và tăng bảo mật (khó decompile và chỉnh sửa), phù hợp với cloud-native (khởi động nhanh).

![JIT-vs-AOT](JIT-vs-AOT.png)


## Basic syntax

### Self-addition and self-subtraction operators

```java
int a = 9;
int b = a++;
int c = ++a;
int d = c--;
int e = --d;
```
Answer:
```java
a = 11b = 9c = 10d = 10e = 10
```

### shift operator

```java
<<, >> (có dấu), >>> (không dấu)
```

> **Note** Trong java, nếu dịch bit vượt quá độ rộng kiểu dữ liệu -> chỉ dịch phần dư của phép chia lấy dư
> với `int x` (32 bit) thì `x<<42` và `x<<10` là tương đương


## Basic data types

| Basic Type | Digits (bit) | Bytes | Default Value | Range |
|-----------|--------------|-------|---------------|-------|
| `byte`    | 8            | 1     | `0`           | -128 ~ 127 |
| `short`   | 16           | 2     | `0`           | -32768 (-2^15) ~ 32767 (2^15 - 1) |
| `int`     | 32           | 4     | `0`           | -2147483648 ~ 2147483647 |
| `long`    | 64           | 8     | `0L`          | -9223372036854775808 (-2^63) ~ 9223372036854775807 (2^63 - 1) |
| `char`    | 16           | 2     | `'\u0000'`    | 0 ~ 65535 (2^16 - 1) |
| `float`   | 32           | 4     | `0.0f`        | 1.4E-45 ~ 3.4028235E38 |
| `double`  | 64           | 8     | `0.0d`        | 4.9E-324 ~ 1.7976931348623157E308 |
| `boolean` | 1          | —     | `false`       | `true`, `false` |

### What is the difference between the basic type and the packaging type?

-  Primitives type:
   - Không phải object
   - Nhẹ, nhanh, ít tốn bộ nhớ
   - Biến cục bộ (local variable) của primitives type được lưu trong stack còn member variables của primitives type lưu trong heap
   - Compare: ==
- Wrapper class:
   - Là class thuộc java.lang
   - Dùng để "bọc" các kiểu primitive thành object
   - Wrapper dùng nhiều trong `Generics (<T>), Collection (Map, Set), Framework, Reflection, ...`
   - Wrapper có giá trị null
   - Default value là null
   - Compare: == -> so sánh địa chỉ, equals() -> so sánh giá trị

> **Note**:  
> - Phần lơn object nằm trong heap. JIT có thể optimize bằng `Escape Analysis`. => Kiểm tra xem một object có thoát (escape) ra khỏi phạm vi sử dụng hiện tại hay không. Nếu không, JVM có thể không cấp phát trên heap, không GC, tách object thành các biến primitive.  
> - Basic data types are stored in the stack is a common misconception! Dữ liệu kiểu primitive lưu trong stack nếu là local variables. Nếu là member variables thì được lưu trong heap/method area/metaspace.


### Do you know the caching mechanism of package types?

| Wrapper    | Có cache? | Phạm vi mặc định            |
|------------|-----------|-----------------------------|
| Integer    | ✅        | [-128, 127]                 |
| Long       | ✅        | [-128, 127]                 |
| Short      | ✅        | [-128, 127]                 |
| Byte       | ✅        | toàn bộ                     |
| Character  | ✅        | [\u0000, \u007F] (0–127)    |
| Boolean    | ✅        | true, false                 |
| Float      | ❌        | —                           |
| Double     | ❌        | —                           |  
 - `-XX:AutoBoxCacheMax=<size>` có thể dùng để tăng giới hạn dương của cache cho Integer  
 - Cache chỉ hoạt động khi dùng factory method (valueOf() hoặc autoboxing (`Integer a = 100`))

### Do you understand automatic packing (boxing) and unpacking (unboxing) ? What is the principle?

 - Boxing: chuyển `primitive -> wrapper`
```java
int a = 10;
Integer b = Integer.valueOf(a); // packing (boxing)
Integer c = 10; // Autoboxing: compiler tự gọi Integer.valueOf(10)
```
 - Unboxing: chuyển `wraper -> primitive`
```java
Integer b = 10;
int a = b.intValue(); // unpacking (unboxing)
int c = b; // Auto-unboxing: compiler tự gọi a.intValue()
```
> **Note**: Không nên Boxing ↔ Unboxing thường xuyên sẽ gây giảm hiệu năng do phải gọi method, có thể gây NullPointerException, gây áp lực GC.  
 ```java
Integer sum = 0;
for (Integer i = 0; i < n; i++) {
    sum += i; // unboxing + boxing mỗi vòng
}
```

### Why is there a risk of loss of accuracy when calculating floating-point numbers?
- Do hệ cơ số 2. Một số thập phân không thể biểu diễn **chính xác** bằng nhị phân hữu hạn. Ví dụ:  
  - 0.5 = 0.1₂ → biểu diễn chính xác.  
  - 0.25 = 0.01₂ → chính xác.  
  - 0.1 = 0.0001100110011…₂ → vô hạn lặp.  
 => So sánh bị sai:
 ```java
double a = 0.1 + 0.2;
if (a == 0.3) { // ❌ rất nguy hiểm
}
```
=> Cách đúng  
 - Dùng sai số nhỏ:
```java
boolean nearlyEqual(double a, double b) {
    return Math.abs(a - b) < 1e-9;
}
```
 - Dùng `BigDecimal` cho tiền tệ:
```java
BigDecimal a = new BigDecimal("0.1");
BigDecimal b = new BigDecimal("0.2");
System.out.println(a.add(b)); // 0.3
```

## Object-oriented foundation

### What is the difference between member variables and local variables?

