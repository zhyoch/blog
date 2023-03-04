---
title: "自定义Bash提示符并着色"
date: 2020-07-15T09:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Bash,Linux]
series: [Customize Prompt of Shell]
series_weight: 1
featuredImagePreview: ""
summary: Customize and Colorize Bash Prompt.
---

{{< admonition type=quote title="原文地址" open=true >}}
本文**部分**转载自[如何修改PS1命令行提示符的颜色](https://madmalls.com/blog/post/how-to-change-the-output-color-of-echo-in-linux/)
{{< /admonition >}}

## ANSI转义字符

### Control Sequence Introducer

Control Sequence Introducer (CSI) 在ANSI转义序列中用两字符序列`ESC [`表示, 这个序列是由控制字符`ESC` (通常用`^[`或`<ESC>`表示) , 加上左方括号字符`[`组成, 即`^[[`。

在bash中, 控制字符ESC也支持`\e`、`\033`或`\x1b`三种转义字符的写法, 大写字母也行 (`\E`和`\x1B`) 。

### Set Graphics Rendition

{{< admonition type=info title="转义序列代码中没有空格" open=true >}}
转义序列代码在实际使用时中间没有空格, 下面示例中用空格隔开只是方便阅读。
{{< /admonition >}}

要控制显示格式, 须使用Set Graphic Rendition (SGR) 转义序列: `ESC [ parameters m`。

- `parameters`是控制代码, 使用多个代码时中间用分号`;`隔开, 比如`1;31`。
- 如果不指定控制代码, 即`ESC [ m`, 相当于`ESC [ 0 m` (重置所有显示控制属性为默认设置) 。
- `m`表示`SGR`序列。

#### 显示控制代码

显示控制代码有3类: 

- 效果控制代码
- 前景色控制代码 (即字体颜色) 
- 背景色控制代码

##### 效果控制代码

| 代码 | 代码 | 效果                       |
| :--: | :--: | -------------------------- |
|  00   |  0   | 重置所有显示属性为默认设置 |
|  01   |  1   | 字体加粗                   |
|  04   |  4   | 字体加下划线               |
|  05   |  5   | 字体闪烁                   |
|  07   |  7   | 前景色与背景色调转         |

> `00`与`0`效果相同, 其他数字也类似。

##### 前景色与背景色控制代码

| 前景色代码 | 背景色代码 | 颜色 |
| :--------: | :--------: | :--: |
|     30     |     40     | 黑色 |
|     31     |     41     | 红色 |
|     32     |     42     | 绿色 |
|     33     |     43     | 黄色 |
|     34     |     44     | 蓝色 |
|     35     |     45     | 紫色 |
|     36     |     46     | 青色 |
|     37     |     47     | 白色 |

## 修改PS1

{{< admonition type=warning title="注意" open=true >}}
此方案与`zsh`和`oh my zsh`不兼容, 不要同时使用。
{{< /admonition >}}

在`~/.bashrc`文件中添加: 

```bash
export PS1='${debian_chroot:+($debian_chroot)}\[\e[1;37m\]\t \[\e[1;31m\]\u\[\e[1;37m\]@\[\e[1;31m\]\h \[\e[1;36m\]$PWD\[\e[1;31m\]\n\$\[\e[0m\]'
```

如果觉得自己用不到`chroot`, 也可以这样: 

```bash
export PS1='\[\e[1;37m\]\t \[\e[1;31m\]\u\[\e[1;37m\]@\[\e[1;31m\]\h \[\e[1;36m\]$PWD\[\e[1;31m\]\n\$\[\e[0m\]'
```

### 解释

- `\[`和`\]`这两个转义字符通知bash, 被括起来的字符不占用命令行上的任何空间, 这样就使自动换行能够继续正常工作。如果没有这两个转义字符, 当用户键入的命令到达终端的最右端时, 或者查看历史命令时, 就会出现显示错乱的情况。
- `\e[1;37m`即SGR转义序列`ESC [ parameters m`, 定义后续字符的颜色, 这里是加粗的白色。
- `\t`以HH:MM:SS格式显示24小时制时间。
- `\u`显示当前用户名。
- `\h`显示当前host机器名称。
- `$PWD`显示完整路径。
- `\n`表示换行。
- `\$`当用户为`root`时, 显示为`#`。
- `\e[0m`重置后续字符的显示控制属性为默认设置。

手动`source ~/.bashrc`以在当前终端生效, 或者在`~/.profile`中添加: 

```bash
source ~/.bashrc
```

这样每次用户登录时, `~/.bashrc`文件会被自动加载生效。

## 效果

普通用户:

<div style="background-color:black;font-weight:bold;">
    <span style="color:white;">20:34:50</span> <span style="color:red;">bhsr</span><span style="color:white;">@</span><span style="color:red;">Debian</span> <span style="color:cyan;">/home/bhsr</span><div></div><span style="color:red;">$</span>
</div>

root用户:

<div style="background-color:black;font-weight:bold;">
    <span style="color:white;">20:34:50</span> <span style="color:red;">root</span><span style="color:white;">@</span><span style="color:red;">Debian</span> <span style="color:cyan;">/root</span><div></div><span style="color:red;">#</span>
</div>

{{< admonition type=tip title="如果是自己的机器" open=true >}}
自己的机器, 有root权限, 那就把这个效果应用到所有账户吧, 不必像上面那样在每个账户的目录下配置`~/.bashrc`和`~/.profile`, 只需将`PS1`添加到`/etc/profile`即可。
{{< /admonition >}}

## Git Bash on Windows

修改`C:\Program Files\Git\etc\profile.d\git-prompt.sh`, 参考使用以下内容: 

```bash
if test -f /etc/profile.d/git-sdk.sh
then
	TITLEPREFIX=SDK-${MSYSTEM#MINGW}
else
	TITLEPREFIX=$MSYSTEM
fi

if test -f ~/.config/git/git-prompt.sh
then
	. ~/.config/git/git-prompt.sh
else
	PS1='\[\e]0;$TITLEPREFIX:$PWD\007\]' # set window title
	PS1="$PS1"'\n'                 	# new line
	PS1="$PS1"'\[\e[1;37m\]'       	# change to white
	PS1="$PS1"'\t '                 # show time
	PS1="$PS1"'\[\e[1;31m\]'       	# change to red
	PS1="$PS1"'\u'					# show user
	PS1="$PS1"'\[\e[1;37m\]'       	# change to white
	PS1="$PS1"'@'					# show @
	PS1="$PS1"'\[\e[1;31m\]'       	# change to red
	PS1="$PS1"'\h '             	# show host<space>
	PS1="$PS1"'\[\e[1;36m\]'       	# change to cyan
	PS1="$PS1"'$PWD'                # current working directory
	if test -z "$WINELOADERNOEXEC"
	then
		GIT_EXEC_PATH="$(git --exec-path 2>/dev/null)"
		COMPLETION_PATH="${GIT_EXEC_PATH%/libexec/git-core}"
		COMPLETION_PATH="${COMPLETION_PATH%/lib/git-core}"
		COMPLETION_PATH="$COMPLETION_PATH/share/git/completion"
		if test -f "$COMPLETION_PATH/git-prompt.sh"
		then
			. "$COMPLETION_PATH/git-completion.bash"
			. "$COMPLETION_PATH/git-prompt.sh"
			PS1="$PS1"'\[\e[1;36m\]'  # change color to cyan
			PS1="$PS1"'`__git_ps1`'   # bash function
		fi
	fi
	PS1="$PS1"'\n'                 # new line
	PS1="$PS1"'\[\e[1;31m\]'       # change to red
	PS1="$PS1"'$ '                 # prompt: always $
	PS1="$PS1"'\[\e[0m\]'        # change color to unset
fi

MSYS2_PS1="$PS1"               # for detection by MSYS2 SDK's bash.basrc

# Evaluate all user-specific Bash completion scripts (if any)
if test -z "$WINELOADERNOEXEC"
then
	for c in "$HOME"/bash_completion.d/*.bash
	do
		# Handle absence of any scripts (or the folder) gracefully
		test ! -f "$c" ||
		. "$c"
	done
fi
```

颜色和[上面](#解释)一样, 但是粗体效果不明显, 感觉只是颜色变明亮了。

{{< admonition type=quote title="参考链接" open=true >}}
1. [ANSI escape code](https://en.wikipedia.org/wiki/ANSI_escape_code)
2. [Console Codes](http://man7.org/linux/man-pages/man4/console_codes.4.html)
3. [Bash/Prompt customization](https://wiki.archlinux.org/title/Bash/Prompt_customization)
4. [如何修改PS1命令行提示符的颜色](https://madmalls.com/blog/post/how-to-change-the-output-color-of-echo-in-linux/)
{{< /admonition >}}
