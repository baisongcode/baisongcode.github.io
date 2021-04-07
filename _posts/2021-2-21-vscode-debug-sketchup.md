---
layout: post
title:  用VSCode debug Sketchup的几点注意事项
date:   2021-2-21
categories: sketchup二次开发
---

用VSCode开发Sketchup的Ruby插件，设置debug环境。主要步骤可以跟着官方的参考文档来：

[VSCode Debugger Setup](https://github.com/SketchUp/sketchup-ruby-api-tutorials/wiki/VSCode-Debugger-Setup) 

另外还有一篇中文的文章[《用vscode调试ruby 和 ruby for Sketchup》](https://blog.csdn.net/skybboy/article/details/80105524)也可供参考

但今天在摸索的过程中，还是遇到一些问题，特将解决过程记录如下:

## 一、配置Task时，SketchUp.exe的路径及参数

<!--more-->

开始按照官方文档，配置task.json如下：

![image](/img/2021/2021-2-21-vscode-debug-sketchup-1.png)

```
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Debug in SketchUp 2020",
      "type": "shell",
      "command": "", //Windows下不需要
      "windows": {
        "command": "&'C:/Program Files/SketchUp/SketchUp 2020/SketchUp.exe' -rdebug 'ide port=7000'"
      },
      "problemMatcher": []
    }
  ]
}
```

但实际运行时，要么报
> 此时不应有 &。

![image](/img/2021/2021-2-21-vscode-debug-sketchup-2.png)

去掉&，就报
>'C:' 不是内部或外部命令，也不是可运行的程序

![image](/img/2021/2021-2-21-vscode-debug-sketchup-3.png)

于是想了个**Workaround**：

1. 把“C:\Program Files\SketchUp\SketchUp 2020”加到path中去，避免盘符或路径空格引起的问题
  ![image](/img/2021/2021-2-21-vscode-debug-sketchup-4.png)

2. 用转义字符代替单引号

  这样，task.json变为：

```
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Debug in SketchUp 2020",
      "type": "shell",
      "command": "", //windows下不需要
      "windows": {
        "command": "SketchUp.exe -rdebug \"ide port=7000\""
      },
      "problemMatcher": []
    }
  ]
}
```
![image](/img/2021/2021-2-21-vscode-debug-sketchup-5.png)

就能成功唤起Sketchup了

## 二、添加SURubyDebugger.dll

这点在[官方文档](https://github.com/SketchUp/sketchup-ruby-api-tutorials/wiki/VSCode-Debugger-Setup#preparing-sketchup)上是有的，但容易漏掉或搞错。

在sketchup-ruby-debugger的[release页面](https://github.com/SketchUp/sketchup-ruby-debugger/releases)上，下载和自己SU版本对应的SURubyDebugger.dll文件，放到和Sketchup.exe同层的目录下，就可以了。否则无法建立和SU的通讯

## 三、进行调试

注意以上两点后，我在本地就能成功debug了

先设置好断点，然后运行Task

![image](/img/2021/2021-2-21-vscode-debug-sketchup-6.png)

等SU被唤起后，再按F5，就可以debug了

![image](/img/2021/2021-2-21-vscode-debug-sketchup-7.png)