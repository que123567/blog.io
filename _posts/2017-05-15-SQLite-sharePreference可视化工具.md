---
layout: post
title: SQLite  数据库可视化工具推荐
date: 2017-05-15
categories: blog
tags: [Android]
description: 安卓数据库可视化工具推荐

---

### 1.SQLite  数据库可视化工具推荐
- SQLiteStudio 下载地址
https://sqlitestudio.pl/index.rvt

- 1.打开Android studio，tools下的ADM工具
![打开tools下ADM工具](http://img.blog.csdn.net/20170615213603992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 2.导出.db文件到电脑（导出按钮在右上角）
 ![这里写图片描述](http://img.blog.csdn.net/20170615213715168?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 3.将文件导入SQLiteStudio即可视（点击要查看的表，选择上方“数据”列，默认显示的是“结构”列，不显示数据）
![这里写图片描述](http://img.blog.csdn.net/20170615213754262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 2.SQLite /sharePreference 可视化工具推荐

- 第二种方法更方便，直接通过浏览器方式查看

![这里写图片描述](http://img.blog.csdn.net/20170615214834985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

功能强大：

- 可以查看你的应用中所有的数据库。

 可以查看你的应用中所有的shared preferences。

 对你指定的数据库执行SQL语句。

 对你指定的数据库中的数据进行可视化的编辑。

 可以将数据库直接下载下来。

#### **如何用？**

- 1.在build.gradle添加如下依赖即可：
**debugCompile 'com.amitshekhar.android:debug-db:1.0.0' **

- 2.程序运行时会在logcat中输出
 D/DebugDB: Open http://XXX.XXX.X.XXX:8080，直接用浏览器访问

 **如果连接的是手机，需要保证电脑和手机在同一局域网**

 **如果连接虚拟设备，请在terminal中输入如下指令 adb forward tcp:8080 tcp:8080 ，然后访问地址 localhost:8080  .(否则会出现禁止访问提示)**

 ![这里写图片描述](http://img.blog.csdn.net/20170615215203503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 3.如果logcat下没有出现D/DebugDB: Open http://XXX.XXX.X.XXX:8080,只需要将xxx的地址改成你的手机的IP地址即可，虚拟设备地址则为 localhost:8080
