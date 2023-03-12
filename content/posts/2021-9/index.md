---
title: "设置CMD和PowerShell中的Alias"
date: 2021-03-21T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [CMD,PowerShell,Windows]
featuredImagePreview: ""
summary: 使用`DOSKEY`设置CMD中的Alias, 使用`New-Alias`设置PowerShell中的Alias, 将设置Alias的命令保存在预加载文件中来达到别名持久化。
---

## CMD

### DOSKEY

使用`DOSKEY`命令, 比如: 

```shell
DOSKEY ls=dir /p $*
```

{{< admonition type=warning title="注意" open=true >}}
1. `$*`是需要的, 用来传递参数。
2. 在当前窗口设置的别名只在当前窗口有效。
{{< /admonition >}}

### 持久化

参考[这里](https://zhyoch.netlify.app/2020-1/#永久代理), 将自定义的`DOSKEY`命令写入`cmd_init.cmd`文件中, 比如: 

```shell
@echo off

:: Commands Aliases

DOSKEY aria2c=aria2c -c -s16 -x16 -k1M  $*
DOSKEY cd=cd /d $*
DOSKEY clear=cls
DOSKEY cp=xcopy /e /h /k $*
DOSKEY ls=dir /p $*
DOSKEY mv=move $*
```

## PowerShell

### Set-Alias

参考微软官方文档[Set-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias), 使用`Set-Alias`命令设置新的别名或修改已有别名: 

```shell
Set-Alias -Name 别名 -Value 原命令
```

比如: 

```shell
Set-Alias -Name aria2c -Value "aria2c -c -s16 -x16 -k1M"
```

> 也可以直接使用`Set-Alias`的别名`sal`。

### Get-Alias

使用`Get-Alias`命令查看所有别名, 也可以直接使用`Get-Alias`的别名`gal`。

### New-Alias

参考微软官方文档[about_Aliases](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_aliases#creating-an-alias), 使用`New-Alias`命令设置新的别名, 如果别名已存在会提示重复: 

```shell
New-Alias -Name 别名 -Value 原命令
```

> 也可以直接使用`Newt-Alias`的别名`nal`。

{{< admonition type=warning title="注意" open=true >}}
在当前窗口设置的别名只在当前窗口有效。
{{< /admonition >}}

### 持久化

不推荐使用[Export-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/export-alias)和[Import-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/import-alias), 太不方便了, 直接参考[这里](https://zhyoch.netlify.app/2020-1/#永久代理-1), ~~将 Set-Alias -Name aria2c -Value "aria2c -c -s16 -x16 -k1M" 命令写入 PROFILE 文件中~~, 不能将这种长命令当作别名, 不然终端会报错。应该在`PROFILE`文件中自定义`function`:

```shell
function aria2c { & 'aria2c.exe' -c -s16 -x16 -k1M @args }
```
