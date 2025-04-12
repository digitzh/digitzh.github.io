---
layout: post
title: SIMD parallel
tags: 多线程&并发
categories: 技术文章
---

### 1 基本概念

**SIMD(Single Instruction Multiple Data, 单指令流多数据流)：** 一种采用一个控制器来控制多个处理器，同时对一组数据（又称“数据向量”）中的每一个分别执行相同的操作从而实现空间上的[并行性](https://zhida.zhihu.com/search?content_id=100424657&content_type=Article&match_order=1&q=%E5%B9%B6%E8%A1%8C%E6%80%A7&zhida_source=entity)的技术。简单来说就是一个指令能够同时处理多个数据。

### 2 使用方法

### 2.1 IPP
使用Intel开发的跨平台函数库（IPP，Intel Integrated Performance Primitives ），里面的函数实现都使用了SIMD指令进行优化。

### 2.2 Auto-vectorization(自动矢量化)
借助编译器将标量操作转化为[矢量操作](https://zhida.zhihu.com/search?content_id=100424657&content_type=Article&match_order=1&q=%E7%9F%A2%E9%87%8F%E6%93%8D%E4%BD%9C&zhida_source=entity)。

### 2.3 编译器指示符(compiler directive)
如Cilk里的`#pragma simd`和OpenMP里的`#pragma omp simd`。如下所示，使用`#pragma simd`强制循环矢量化：

```cpp
void add_floats（float * a，float * b，float * c，float * d，float * e，int n）
{
    int i;
#pragma simd
    for（i = 0; i <n; i ++）
    {
        a [i] = a [i] + b [i] + c [i] + d [i] + e [i];
    }
}
```

### 2.4 内置函数(intrinsics)
如下所示，使用SSE `_mm_add_ps` 内置函数，一次执行8个单精度浮点数的加法：

```cpp
int  main()
{
	__m128 v0 = _mm_set_ps(1.0f, 2.0f, 3.0f, 4.0f);
	__m128 v1 = _mm_set_ps(1.0f, 2.0f, 3.0f, 4.0f);

	__m128 result = _mm_add_ps(v0, v1);
}
```

最后一种方法则是使用汇编直接操作寄存器（较麻烦）。
