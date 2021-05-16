---
title: win10 查看历史 WIFI 密码
date: 2021-5-16
tags:
  - WIFI 密码
categories:
  - 其它
---

## 无线属性



xp、win7 都可以使用类似操作查看密码，原理都是查看无线属性



右击 wifi 标识 ->“网络和 Internet”->“网络和共享中心”-> 选择连接的网络 ->“无线属性”->“安全”



1. 右击 wifi 标识——打开“网络和 Internet”设置



![2021/05/16/TOIMG490a40516081348N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG490a40516081348N.png)



1. 单击“网络和共享中心”



![2021/05/16/TOIMGfeaf40516081412N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMGfeaf40516081412N.png)



1. 单击连接的网络



![2021/05/16/TOIMG008f30516081444N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG008f30516081444N.png)



1. 选择“无线属性”



![2021/05/16/TOIMG396bd0516081507N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG396bd0516081507N.png)



1. 选择“安全”选项



![2021/05/16/TOIMG90aa20516081526N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG90aa20516081526N.png)



1. 勾选“显示字符串”



![2021/05/16/TOIMG2fca50516081548N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG2fca50516081548N.png)



有很多途径才能查看无线属性，例如，控制面板 -> 网络和 Internet -> 网络连接，以上只是举其中一个



## 命令提示符



该方法可以查询所有连接过的 WiFi，然后使用用命令查询具体要查询密码的 WiFi



打开命令提示符窗口 -> 输入 `netsh` -> 输入 `wlan show profile` -> 输入 `wlan show profile "WIFI 名称``" key=clear`



1. Win+R 运行“cmd”打开命令提示符窗口（也可以使用 Windows PowerShell）



![2021/05/16/TOIMGbc14e0516081610N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMGbc14e0516081610N.png)



![2021/05/16/TOIMG8d43f0516081631N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG8d43f0516081631N.png)



1. 在窗口中输出 `netsh` 后按 Enter 键（回车键）



![2021/05/16/TOIMGf621b0516081657N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMGf621b0516081657N.png)



1. 在 “netsh>”后面输入`wlan show profile` 后按 Enter 键



![2021/05/16/TOIMGde3c30516081726N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMGde3c30516081726N.png)



此时可以看到连接过的历史 wifi 列表



1. 例如此时我需要查看“Redmi Note 7”的 wifi 密码，输入命令 `wlan show profile "``Redmi Note 7``" key=clear`，敲下回车键



![2021/05/16/TOIMG64bc80516081748N.png](https://picturebed.tumiblog.top/2021/05/16/TOIMG64bc80516081748N.png)



“关键内容”就是该 wifi 的密码 