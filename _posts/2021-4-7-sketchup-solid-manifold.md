---
layout: post
title:  Sketchup中的实体（Solid Manifold）
date:   2021-4-7
categories: sketchup二次开发
---
这是近期学习中，对实体（Solid Manifold）理解的一个记录。

## 一、什么是实体（Solid Manifold）？

在SketchUp中，实体是任何具有有限封闭体积的3D模型（组件或组）。

详细定义的话，包括：
1. 必须是组件或群组
2. 不能有破面
3. 不能有多余的线
4. 不能有其余的隐藏物体
5. 每条线（edge）都只连接两个面（两个立方体，如果有任何一条边相切的话，尽管这两个立方体各自是一个实体，但合起来却不是）

如果做3D打印的话，模型中的零件必须都是实体才可以。

<!--more-->

顺便说一下，有些中文的技术文章，在翻译“entity”这个词时，也翻译成“实体”，是不准确的。
- Entity，包含了所有的几何对象，在官方翻译中，译为“图元”。详细理解可以见[《Ruby for SketchUp之Entities对象》](https://blog.csdn.net/bobo_993/article/details/106800161)
- Solid Manifold，也可以简称为manifold，或solid。其层级可以说是Entity下的孙子类了，翻译为“实体”

## 二、实体的判断及操作

判断一个Entity是否实体，最简单的方法是选中，然后看其“图元信息”中，是否有体积的数值。如果有，它就是实体。

实体的布尔操作工具，可以参考视频：[SketchUp基础入门-工具篇 1-40-实体工具](https://www.bilibili.com/video/BV1Jp4y117EU)

## 三、实体相关的Ruby API

因为实体只可能是ComponentInstance或Group，所以只在这两个类下找即可，主要的有：

- #manifold? ⇒ Boolean   判断是否为实体
- #volume ⇒ Float    求体积
- #outer_shell、#union、#intersect等   布尔操作，与图形界面上的实体工具相对应。
