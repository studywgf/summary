---
title: Conda basic command
tags: conda
---
## 添加频道
>conda config --add channels channel_url
>
channel_url：频道URL
## 创建环境
>conda create -n my_env

my_env：创建的环境名
## 删除环境安装的包：可以指定包或者全部包
>conda remove [-n env_name | -p path] [packages] [--all]

## conda activate my_env报错：LookupError: unknown encoding: 65001
执行下列命令解决：
>set pythonioencoding=utf-8
