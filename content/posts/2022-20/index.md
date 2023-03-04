---
title: "Android刷机笔记"
date: 2022-12-01T10:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Android]
featuredImagePreview: ""
summary: 这是一篇许久以前的刷机笔记。
---

{{< admonition type=note title="注意" open=true >}}
本文操作机型为 Xiaomi Redmi Note 5(whyred), 不支持 A/B 分区. 
{{< /admonition >}}

## 官方解锁工具

https://www.miui.com/unlock/download.html

## 官方线刷工具

http://bigota.d.miui.com/tools/MiFlash2018-5-28-0.zip

## TWRP

https://twrp.me/xiaomi/xiaomiredminote5pro.html

## ROM

### 类原生

- Android 类原生系统收录: https://mi.fiime.cn/Android
- 社区分享包: https://mi.fiime.cn/OriginROM
- LineageOS: ~~https://download.lineageos.org/whyred~~, 已撤包
- Pixel Experience: https://download.pixelexperience.org/whyred, 已停止维护
    > 安装了基于 Android 10(不包括10) 以上的 Pixel Experience 后, 无法进入 TWRP, 需要使用作者推荐的 [Aosp Recovery for Whyred](https://www.pling.com/p/1687869/) 
- Arrow: https://arrowos.net/download, 已停止维护
- crDroid: https://crdroid.net/whyred/7, 已停止维护
- DerpFest: https://sourceforge.net/projects/derpfest/files/whyred/
- DotOS: https://www.droidontime.com/devices/whyred, 已停止维护
- Evolution-X: https://evolution-x.org/device/whyred, 已停止维护
- Havoc-OS: https://havoc-os.com/device#whyred, 已停止维护
- Nusantara: https://nusantararom.org/device/whyred/
- SparkOS: https://spark-os.live/download/whyred

### 官方

- xiaomirom.com: https://xiaomirom.com/rom/redmi-note-5-note-5-pro-whyred-global-fastboot-recovery-rom/
- xiaomi.eu: https://sourceforge.net/projects/xiaomi-eu-multilang-miui-roms/files/xiaomi.eu/MIUI-STABLE-RELEASES/

## Magisk

Magisk 官方文档: https://topjohnwu.github.io/Magisk/install.html

Magisk 中文网: https://magiskcn.com/

### 随机包名隐藏 Magisk

使用随机包名重新安装 Magisk APP, 从而隐藏 Magisk, 由于上游文件位于 GitHub, 所以使用此功能最好启用网络代理. 

#### 对于 Magisk(version≤23)

Magisk(version＞23) 之后使用了新特性: Zygisk, 由于差别过大, 新版 Magisk(version＞23) 不能直接覆盖安装旧版 Magisk(version≤23), 所以 Magisk 使用随机包名隐藏 Magisk 的功能不再生效, 需要设置更新通道为自定义通道: 

```
https://cdn.jsdelivr.net/gh/zhyoch/magisk-v23.0@master/magisk-v23.0.json
```

#### 对于 Magisk(version＞23)

不用更改通道.

## Magisk 模块

### Zygisk-Shamiko

Magisk(version＞23) 之后移除了 Magisk Hide, 可以使用 Shamiko 代替:

下载: https://github.com/LSPosed/LSPosed.github.io/releases/latest

文档: https://www.daxiaamu.com/6465/

### LSPosed

#### 对于 Magisk(version≤23)

1. 从 [GitHub](https://github.com/RikkaApps/Riru/releases/latest) 下载 `Riru.zip` 后, 在 Magisk 中刷入 Riru. 
2. 从 [GitHub](https://github.com/LSPosed/LSPosed/releases/latest) 下载 `LSPosed.zip` 后, 在 Magisk 中刷入 LSPosed. 
3. 如果丢失了 LSPosed 的桌面快捷方式, 可通过拨号键输入 `*#*#5776733#*#*` 进入 LSPosed.

#### 对于 Magisk(version＞23)

对于 Zygisk, 可以直接安装最新版 LSPosed 框架: https://github.com/LSPosed/LSPosed/releases/latest

## LSPosed 模块

- [哔哩哔哩 不要竖屏](https://github.com/WankkoRee/Portrait2Landscape/releases/latest) , 将哔哩哔哩中全屏播放的"竖屏视频"强制以"横屏播放器"播放
- [FuckBilibiliVote](https://github.com/Xposed-Modules-Repo/github.zerorooot.fuckbilibilivote/releases/latest) , 移除哔哩哔哩中的 `投票`、`一键三连`、`预告`、`up主弹幕`、`打分` 弹幕弹窗
- [Fuck Coolapk R](https://github.com/Xposed-Modules-Repo/org.hello.coolapk/releases/latest) , 精简酷安
- [隐藏应用列表](https://github.com/Dr-TSNG/Hide-My-Applist/releases/latest) , 使一些应用对另一些应用隐藏
- [知了](https://github.com/shatyuka/Zhiliao/releases/latest) , 精简知乎

## SELinux

折腾一通后, 通过

```shell
adb shell
getenforce
```

来查看 SELinux 的状态, 若状态是 `Permissive` , 则通过

```shell
su root
setenforce 1
```

使状态变成 `Enforcing` . 

## Safetynet

参考 [这篇文章](https://blog.csdn.net/a1240193326/article/details/113866931) , 一般先使用 [Universal SafetyNet Fix](https://github.com/kdrag0n/safetynet-fix/releases/latest) , 不行的话再上 [MagiskHidePropsConf](https://github.com/Magisk-Modules-Repo/MagiskHidePropsConf/releases/latest) , 再不行就用 [XPrivacyLua](http://repo.xposed.info/module/eu.faircode.xlua) . 

Play 商店设备未通过认证, 可 [手动注册](https://www.google.com/android/uncertified/) , 参考少数派的这篇 [文章](https://sspai.com/post/55536) , 偶尔有用.

检查系统指纹命令:

```shell
adb shell getprop ro.build.fingerprint
# MIUI_12.0.3: xiaomi/whyred/whyred:9/PKQ1.180904.001/V12.0.3.0.PEICNXM:user/release-keys
```

## MIUI

### 卸载预装软件

已经 root 了就直接用软件卸载, [MyAndroidTools](https://www.coolapk.com/apk/cn.wq.myandroidtools/), [AppManager](https://github.com/MuntashirAkon/AppManager/releases/latest) 等等可以使用 root 权限的软件管理应用都可以.

如果卸载不了就 `adb shell pm uninstall --user 0 package_name`.

如果还是卸载不了某些应用, 那就先清除数据, 再找到安装包所在路径, 比如 `/system/priv-app/*` 之类的, 直接删除相应文件夹, 再重启就没有了.

### Google Apps

MIUI 中比较简单, 从 ApkMirror 下载安装 [Google Services Framework](https://www.apkmirror.com/apk/google-inc/google-services-framework/), [Google Play Services](https://www.apkmirror.com/apk/google-inc/google-play-services/), [Google Play Store](https://www.apkmirror.com/apk/google-inc/google-play-store/) 即可. 

> 似乎不可以刷入 [Mind The Gapps](https://androidfilehost.com/?w=files&flid=322935) 或 [The Open GApps Project](https://opengapps.org/#downloadsection) . 

### Bluetooth AAC

MIUI 的蓝牙 AAC 黑白名单文件路径为 `/system/etc/bluetooth/interop_database.conf` . 比如将 OPPO Enco Air 添加至白名单: 

```
[INTEROP_ENABLE_AAC_CODEC]
OPPO Enco Air = Name_Based
# Address_Based除对应蓝牙耳机MAC地址前六位
48:D8:45 = Address_Based
```

## LineageOS

### Google Apps

LineageOS 中, 本机型直接安装 `Google Services Framework`, `Google Play Services`, `Google Play Store` 三件套不能运行, 需要刷入.

参考官方 [WIKI](https://wiki.lineageos.org/gapps) 下载 [Mind The Gapps](https://androidfilehost.com/?w=files&flid=322935) , 或者从 [The Open GApps Project](https://opengapps.org/#downloadsection) 下载 `pico` 版本(极其精简), 从 TWRP 中刷入. 

{{< admonition type=warning title="注意" open=true >}}
在 TWRP 刷入 ROM 成功后, 先不要开机, 在这时刷入 Google 套件.

若是不小心开了机, 只能恢复出厂设置后再刷入 Google 套件.
{{< /admonition >}}

## Firmware

https://xiaomifirmwareupdater.com/firmware/whyred/

## Baseband

查看基带信息: 

```shell
adb shell getprop gsm.version.baseband
```

- Official-CN: `MPSS.AT.3.1-00777-SDM660_GEN_PACK-1.336736.1.337834.1`
- Official-EU, ArrowOS, LingageOS, PixelExperience: `660_GEN_PACK-1.336736.1.337834.1,660_GEN_PACK-1.336736.1.337834.1`

## ADB 常用命令

```shell
# 进入 shell
adb shell

# 列出设备中已经安装的所有应用
pm list packages
# 列出包名中包含关键字的应用的完整包名
pm list packages "keyword"
# 列出所有已禁用的应用
pm list packages -d
# 列出所有已启用的应用
pm list packages -e
# 列出所有系统应用
pm list packages -s
# 列出所有第三方应用
pm list packages -3

# 列出系统上所有的用户
pm list users

# 列出该应用的所有数据位置
pm path package_name

# 清除指定应用的所有数据
pm clear package_name

# 卸载应用
# -k: 卸载应用且保留数据与缓存(不加-k则全部删除)
# --user 0: 为当前用户卸载
pm uninstall [-k] [--user 0] package_name

# 禁用应用
pm disable package_name
# 启用应用
pm enable package_name
```