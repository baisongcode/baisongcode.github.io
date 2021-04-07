---
layout: post
title:  DXF文件格式的理解
date:   2021-2-5
categories: sketchup二次开发
---

本文是个人学习DXF文件格式的笔记，如有错误，欢迎指正！


## 一、DXF文件的基本结构

DXF文件在业务内容上，为一个树状结构，总体上分为6个Section（段）如下图：


![image](/img/2021/2021-2-5-dxf-understanding-1.png)


<!--more-->

由于DXF起源于上个世纪80年代，所以其内部的具体数据结构，在今天看来比较奇怪。用文本编辑器打开一个DXF文件，其最初的几行内容为（下面代码中“->”的后面为我的注释）：

```
  0         -> 组码（Group Code）0，表示下一行表示的，是哪个层级的业务内容
SECTION     -> 值。表明这是一个SECTION（最高层次的业务级别）的开始
  2         -> 组码2，表示下面的值是上面层级（SECTION）名字
HEADER      -> 值。结合前两行，表示这个SECTION的名字为HEADER（第一个段）
  9         -> 组码9。表示下面一行的内容为一个变量名
$ACADVER    -> 值。变量名为$ACADVER，指Autocad的版本号
  1         -> 组码1。表示下面一行的内容为上一行变量（$ACADVER）的值
AC1027      -> 值。AC1027表示Autocad 2013
```

从上面的例子，可以看出，DXF文件的具体实现为：
- 首行（奇数行）为Group Code（组码），规定了下一行具体是什么样的内容。
- 紧跟的下一行（偶数行），为具体的内容

所有Group Code的含义，都可以参考：
- autodesk官网中的[DXF参考文档](https://help.autodesk.com/view/OARX/2018/ENU/?guid=GUID-235B22E0-A567-4CF6-92D3-38A2306D73F3)
- autodesk官网中[下载版PDF文档](https://www.autodesk.com/developer-network/platform-technologies/autocad-dxf-archive)（更新到AutoCAD 2014）
- 可下载的中文翻译版，虽然旧点，但99%的内容都能覆盖

## 二、ENTITIES段的基本结构

图形信息都在ENTITIES段，对大部分开发者来说最为重要。

我用autocad 2014画了一条直线和一个圆。直线起点坐标[111,222]，终点坐标[333,444]。圆的圆心坐标[555,555]，半径50。如下图：

![image](/img/2021/2021-2-5-dxf-understanding-2.png)

另存为DXF文件，摘取其中ENTITIES段予以说明：

```
  0         -> 组码0，表示下一行表示的，是哪个层级的业务内容
SECTION     -> 值。表明SECTION的开始
  2         -> 组码2，表示下面的值是上面层级（SECTION）名字
ENTITIES    -> 值。SECTION的名字是ENTITIES
  0         -> 组码0，表示下一行表示的，是哪个层级的业务内容
LINE        -> 值。表明SECTION下的LINE（直线）开始
  5         -> 组码5，表示Entity handle（图元句柄，就是这条Line的一个ID）
243         -> 值。表示具体的ID
330         -> 组码330，表示此元素在内部字典的另外一个ID
1F          -> 值。表示具体的ID
100         -> 组码100，表示子类的开始。
AcDbEntity  -> 值。表示子类名
  8         -> 组码8，表示图层名
0           -> 值。表示图层为0
100         -> 组码100，表示子类的开始。所有Line对象特有的数据，都在这个子类里
AcDbLine    -> 值。表示子类名
 10         -> 组码10，表示起点X轴坐标
111.0       -> 值。X轴坐标值
 20         -> 组码20，表示起点X轴坐标
222.0       -> 值。Y轴坐标值
 30         -> 组码30，表示起点X轴坐标
0.0         -> 值。Z轴坐标值
 11         -> 组码11，表示终点X轴坐标
333.0
 21         -> 组码21，表示终点X轴坐标
444.0
 31         -> 组码31，表示终点X轴坐标
0.0
  0         -> 组码0，表示又开始了一个新层级的业务内容
CIRCLE      -> 值。表示一个圆，读者可以自行分析以下内容
  5
244
330
1F
100
AcDbEntity
  8
0
100
AcDbCircle
 10
555.0
 20
555.0
 30
0.0
 40
50.0
  0         -> 组码0，表示又开始了一个新层级的业务内容
ENDSEC      -> 值。ENDSEC表示ENTITIES SECTION结束
```
