---
layout: post
title: int, long与long long
tags: 
categories: 技术文章
---

### int
int最大值/最小值的宏定义为`INT_MAX`/`INT_MIN`。

### long
在64位机器中，long int和int均为4字节，取值范围相同（均为$[-2^{32}, 2^{32}-1]$），可等同于int。

### long long
long long为8字节，取值范围是$[-2^{64}, 2^{64}-1]$。最大值/最小值的宏定义为`LLONG_MAX`/`LLONG_MIN`。

