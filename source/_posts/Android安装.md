---
title: Android安装
date: 2026-06-02 20:06:11
tags: Android
excerpt: Android
categories: 
    - Android
---
不装虚拟环境

直接“真机调试”

手机开发模式打开USB调试

选clumn

Android Studio文件名

文件SDK名字不能改（软件开发工具包）

`https://developer.android.com/studio?hl=zh-cn`

# 配置国内阿里云/清华镜像源

在欢迎界面左侧菜单，点击 Customize（自定义）。

在右侧找到并点击 All settings...（所有设置）。

在弹出的庞大设置窗口中，依次展开：Appearance & Behavior -> System Settings -> HTTP Proxy。

勾选 Auto-detect proxy settings（自动检测代理设置）。

勾选下方的 Automatic proxy configuration URL，并在输入框中填入国内大厂的镜像源地址（任选其一，推荐第一个）：

`[http://mirrors.neusoft.edu.cn:80](http://mirrors.neusoft.edu.cn:80)（东软镜像源，国内非常稳定）`

镜像备用：手动配置代理，Host填 mirrors.aliyun.com，Port填 80

点击最下方的 Apply，然后点击 OK 保存退出。

# 构建工具包太慢替换腾讯镜像

在左侧项目文件树里，找到并展开 gradle -> wrapper 文件夹。

双击打开里面的 gradle-wrapper.properties 文件。

找到最后一行以 distributionUrl= 开头的那行代码，它现在应该长这样：

```
Properties
distributionUrl=https\://services.gradle.org/distributions/gradle-9.4-bin.zip
```


```
将其替换为**腾讯云的国内镜像地址**（把域名改掉即可）：
   properties
   distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-9.4-bin.zip
```

# 实在不行离线塞包

第一步：用浏览器下载 Gradle 压缩包
复制下面下载链接，直接粘贴到你的电脑浏览器（或者手机浏览器传回电脑）里下载：


注意： 下载完成后，会得到一个 gradle-9.4.1-bin.zip 的压缩包。千万不要解压它！ 就保持 .zip 格式。

第二步：找到并打开它的“秘密缓存口袋”
我们要把这个压缩包放到 Android Studio 专门存放 Gradle 的本地缓存路径里。

在电脑上打开任意文件夹，在最上面的路径栏里输入以下路径并回车（这是 Windows 系统的默认存放地）：


`C:\Users\xxx\.gradle\wrapper\dists`

进去之后，你会看到一个叫 gradle-9.4.1-bin 的文件夹，点进去。

进去后，你会看到一串由乱码字母和数字组成的奇葩文件夹（比如 8wwvxxxxx... 每个人生成的乱码不一样），再点进去。

第三步：人工偷天换日

进到那个乱码文件夹后，你会发现里面有一个类似 gradle-9.4.1-bin.zip.part（没下载完的临时文件）。

干掉它： 把里面的临时文件全部删掉，让这个文件夹变成空的。

塞进去： 把你刚刚用浏览器下载好的 gradle-9.4.1-bin.zip 压缩包，直接复制粘贴到这个乱码文件夹里。

最终你的文件夹里应该只有孤零零的一个 gradle-9.4.1-bin.zip 压缩包，长这样：

C:\Users\xxxx\.gradle\wrapper\dists\gradle-9.4.1-bin\【一串乱码文件夹】\gradle-9.4.1-bin.zip

第四步：重启 Android Studio 见证奇迹

重新打开你的 Android Studio 和你的 AndroidVue 项目。

点击右上角或者弹窗提示里的 Try Again 或者点击顶部菜单栏的 File -> Sync Project with Gradle Files。

这时候，Android Studio 会震惊地发现：“诶？我需要的包怎么已经在兜里了？！” 它会直接跳过联网下载阶段，直接在本地解压并完成配置！

# 热重载