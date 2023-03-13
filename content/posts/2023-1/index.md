---
title: "Aria2配置和浏览器扩展"
date: 2023-03-12T09:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Browser,Download]
featuredImagePreview: ""
summary: 相当好用的 Aria2 配置, 以及 Chromium 系浏览器扩展。
---

## 安装

浏览器扩展: [Aria2 for Chrome](https://chrome.google.com/webstore/detail/aria2-for-chrome/mpkodccbngfoacfalldjimigbofkhgjn) , [Aria2 for Edge](https://microsoftedge.microsoft.com/addons/detail/aria2-for-edge/jjfgljkjddpcpfapejfkelkbjbehagbh)

- 这两个扩展是相同的。
- 可以拦截浏览器的下载请求, 转为使用 aria2 下载。
- 集成了 [Aria2NG](http://ariang.mayswind.net/zh_Hans/) , 所以只需要再安装 [Aria2](https://github.com/aria2/aria2/releases/latest) 本体即可。

## 配置

1. 在 `aria2c.exe` 同目录下创建 `aria2.conf` , 内容参考 https://github.com/P3TERX/aria2.conf/blob/master/aria2.conf , 其注释非常详细。
2. 按照配置文件，创建需要的空文件，比如 `dht.dat` 、`dht6.dat` 、`aria2.session` 等。

## aria2c.exe的启动方式

### 手动启动(不推荐)

配置了系统环境变量中的 `PATH` 后, 随处在终端中运行 `aria2c --conf=aria2.conf` 就可以启动 `aria2c.exe` 了, 但是一关闭终端窗口, `aria2c.exe` 就会停止。

更进一步的做法是, 创建 `vbs` 文件, 写入以下内容, 双击运行, 使 `aria2c.exe` 在后台运行: 

```
CreateObject("WScript.Shell").Run "aria2c.exe --conf-path=aria2.conf",0
```

但还是不如开机自启动方便。

### 开机自启动(推荐)

打开任务计划程序, 创建任务:

1. 常规

   1. “更改用户或组”, 使用 **SYSTEM** 账户。
   2. 选择 **只在用户登录时运行** 。

      > 如果要使用 `falloc` 的文件预分配方式, 又出现报错 `[WARN] Gaining privilege SeManageVolumePrivilege failed` , 则需要勾选 **使用最高权限运行** 。

2. 触发器

    新建, “开始任务”选择 **登录时** , 按需选择“延迟任务时间”, 勾选 **已启用** 。

3. 操作

    新建, “操作”选择 **启动程序** , “程序或脚本”填写 `aria2c.exe` , “添加参数”填写 `--conf=aria2.conf` , “起始于”填写 `aria2c.exe` 所在目录的路径。

4. 条件

    全部取消勾选, 包括灰显的。

5. 设置

   1. 仅勾选 **允许按需运行任务** 。
   2. “如果此任务已经运行, 以下规则适用”, 选择 **请勿启动新实例** 。