---
title: Linux configuring boot autostart
tags: linux
---


## 使用/etc/rc.d/rc.local文件
```
具体步骤如下：

1、我是提前编辑好shell的脚本文件，例如test.sh，也可直接把shell命令写入/etc/rc.d/rc.local文件中，个人不推荐；

2、授予test.sh、/etc/rc.d/rc.local用户执行权限：

chmod +x test.sh

chmod +x /etc/rc.d/rc.local

3、在/etc/rc.d/rc.loacl中执行test.sh；

至此配置完成！


查看是否开机自启动成功：

1、查看rc-local服务状态，此服务执行的就是/etc/rc.d/rc.local文件：

systemctl status rc-local


本人通过此方式开机自启动没有成功，原因是开启paddle的tensorrt加载报错，错误如下：

RuntimeError: (Unavailable) Load tensorrt api getInferLibVersion failed
[Hint: p_getInferLibVersion should not be null.] (at/paddle/paddle/fluid/platform/dynload/tensorrt.h:114)


本人也没有解决掉，放弃了此方式。
```
## 添加给chkconfig管理服务
```
​

具体步骤如下：

1、编写好shell脚本，如test.sh，此脚本可以添加如下两行：

#chkconfig: 2345 90 10
#description: Face Recognition Service

其中2345表示运行级别，运行级别有：

0：关机
1：单用户模式
2：无网络连接的多用户命令行模式
3：有网络连接的多用户命令行模式
4：保留，未使用
5：带图形界面的多用户模式
6：重新启动

90表示启动优先权，10表示停止优先权，数值在0-100（不包含100,0没有试过），数值越大，优先权越小！

2、增加可执行权限：

chmod +x test.sh

2、将脚本移动到/etc/rc.d/init.d目录下：

mv test.sh /etc/rc.d/init.d（需要把文件移动到此目录才可以）

3、交于chkconfig进行管理：

chkconfig --add test.sh

4、此命令可选，因为在添加到chkconfig管理时已解析开启2345运行级别：

chkconfig test.sh on

至此配置完成！

chkconfig其他命令：

--del 　删除所指定的系统服务，不再由chkconfig指令管理，并同时在系统启动的叙述文件内删除相关数据。

本人实际遇到的问题：由于使用到rabbitmq，所以要确保其服务已经跑起来了，在启动连接服务端时先阻塞一段时间避免连接失败，shell阻塞命令：

sleep number[smhd]

number:时间长度，后面可接s、m、h或d

其中s代表秒，m代表分钟，h代表小时，d代表日数。
```
## 本人实际遇到未解决问题：
```
1、在脚本里使用conda命令时提示需要先conda init，根据提示也未解决，更换成了source；

2、在python脚本执行shell脚本时提示source activate命令找不到；

3、在切换好的虚拟环境中使用shell脚本提示python包找不到。
```