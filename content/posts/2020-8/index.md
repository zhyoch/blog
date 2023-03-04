---
title: "Debian中zsh的安装与美化"
date: 2020-07-12T09:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Bash,Debian,Linux,ZSH]
featuredImagePreview: ""
summary: zsh的安装与美化。
---

{{< admonition type=warning title="注意" open=true >}}
`zsh`和`oh my zsh`与[自定义bash提示符颜色](https://zhyoch.netlify.app/2020-11/)不兼容, 不建议同时使用。
{{< /admonition >}}

## 安装zsh

{{< admonition type=warning title="注意" open=true >}}
`zsh`和`oh my zsh`, 以及`oh my zsh`的插件都需要在各自用户下安装一遍。以防在root用户下安装后, 切换到普通用户发现不生效的情况。

普通用户下安装后需要注销重新登录才生效。
{{< /admonition >}}

```bash
sudo apt install zsh
```

## oh my zsh

{{< admonition type=tip title="技巧" open=true >}}
如果安装速度慢或者SSL错误, 可以先给此终端挂上代理, 参考[终端代理设置](https://zhyoch.netlify.app/2020-1/)。
{{< /admonition >}}

### 通过curl安装

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 通过wget安装

```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## zsh-themes

### powerlevel10k

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

中国大陆用户可以使用Gitee上的官方镜像加速下载: 

```bash
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

编辑**zsh**配置文件: 

```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

## zsh-plugins

### zsh-autosuggestions

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### zsh-syntax-highlighting

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

编辑**zsh**配置文件: 

```
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```

{{< admonition type=quote title="参考链接" open=true >}}
1. [超级终端的字体背景和颜色显示等](https://blog.csdn.net/u014470361/article/details/81512330)
2. [Linux环境变量加载原理解析](https://bbs.huaweicloud.com/forum/thread-89706-1-1.html)
{{< /admonition >}}
