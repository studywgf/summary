---
title: Visual Studio Code config workspace
tags: vs code
---


## python程序中运行相对路径设置：
默认的工作空间为打开的文件夹，更改为程序运行的文件夹，操作对象为Python插件，操作如下：
## 打开Python插件：
![enter description here](https://img-blog.csdnimg.cn/77fde25d8f074c8e9b0a79745dc38b5c.png)
## 点击设置图标：
![enter description here](https://img-blog.csdnimg.cn/23de418e045f4ede9afbbfbda1b4eb12.png)
## 选择扩展设置，将Python > Terminal:Execute In File Dir勾选上：
![enter description here](https://img-blog.csdnimg.cn/efdbf72129fe4d468f729c81abcd91bc.png)
## 若不行可用代码更改工作空间：
```
"""
Virtual Studio Code python程序调试设置工作空间
"""
import os

cwd = os.path.dirname(__file__)
os.chdir(cwd)
```