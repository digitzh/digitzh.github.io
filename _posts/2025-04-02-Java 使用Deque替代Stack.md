---
layout: post
title: Java 使用Deque替代Stack
tags: 
categories: 技术文章
---

参考链接：[算法动画 | 被 "废弃" 的 Java 栈，为什么还在用](https://mp.weixin.qq.com/s/TSpWaYSUpjeR_wzSclCTQA)

### Stack被废弃的原因

1. 性能弱。Stack继承自Vector，是线程安全的，但性能弱于Deque。
2. 有无关的方法。Stack继承自Vector，有add, remove等方法，但作为栈数据结构，应该只能通过push, pop方法操作栈顶元素。
### 使用Deque替代Stack

使用Deque替代。常用的Deque子类是ArrayDeque。定义：
```java
Deque<Character> stack = new ArrayDeque<Character>();
```
Deque方法：

| 操作   | 方法                                          |
| ---- | ------------------------------------------- |
| 入栈   | `push(E item)`                              |
| 出栈   | `poll()` 栈为空时返回 null  <br>`pop()` 栈为空时会抛出异常 |
| 查看栈顶 | `peek()` 为空时返回 null                         |

Deque本质是双端队列，所以还有`offerLast()`、`pollLast()`、`peekLast()`方法。作为栈时，这些方法不应该被用到。