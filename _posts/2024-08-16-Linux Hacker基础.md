---
layout: post
title: Linux Hacker基础
tags: Linux
categories: 技术文章
---

## 1 基础知识

### whereis和which

whereis和which均用于查找二进制文件。which查找PATH变量中的二进制文件。

### find

示例：
```sh
find / -type f -name apache
```
通配符：
`?` - 单个任意字符
`[]` - 匹配括号内的字符，举例：`[h,b]at`匹配hat和bat

### cat

显示文件内容：
```
cat file.txt
```
将内容写入文件（覆盖）：
```
cat > file.txt
Helloworld!
```
将内容写至文件末尾：
```
cat >> file.txt
Helloworld!
```

## 2 文本操作

### head、tail和nl

`head` - 查看前10行（默认），可指定行数：
```
head -20 snort.conf
```
`tail` - 默认查看后10行，可指定行数。
`nl` - 标显行数：
```
nl snort.conf
```

### 使用sed查找和替换

```
sed s/mysql/MySQL/g /etc/snort/snort.conf > snort2.conf
```
将`snort.conf`中的`mysql`替换成`MySQL`并保存到`snort2.conf`。其中`s`指search；`g`指global（全局替换）。
