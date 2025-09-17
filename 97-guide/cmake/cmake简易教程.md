---
title: "[[cmake简易教程]]"
tags: ["cmake"]
author: kaylor
date_creation: 2025-09-17
aliases:
---

## 指令
### 简单编译
```bash
mkdir build -pv # 进入源码目录并建立build文件夹作为cmake的编译缓存
cd build # 进入该文件夹
cmake -DCMAKE_BUILD_TYPE=Debug .. # 构建编译环境，同时添加工程宏定义CMAKE_BUILD_TYPE=Debug, 这是可选的可能为Release等。 .. 代表源码在这个目录上一层
make # 编译该工程
```

```bash
cmake -B build ./ # 构建编译目录，目录名字是build
cmake --build build # 编译该工程
```

## 基本语法