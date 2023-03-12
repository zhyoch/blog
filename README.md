![Blog-on-Devices](assets/images/Blog-on-Devices.png)

<a href="https://gohugo.io" target="_blank" rel="noopener noreffer"><img src="https://img.shields.io/badge/Frame-Hugo-blue?style=flat&logo=Hugo&color=FF4088"></a> <a href="https://github.com/HEIGE-PCloud/DoIt" target="_blank" rel="noopener noreffer"><img src="https://img.shields.io/badge/Theme-DoIt-blue?style=flat&logo=CSS3&logoColor=0594CB&color=0594CB"></a> <a href="https://github.com/zhyoch/blog/blob/master/LICENSE" target="_blank" rel="noopener noreffer"><img src="https://img.shields.io/github/license/zhyoch/blog?label=License&logo=Git&logoColor=F05032&color=F05032"></a> <a href="https://gitee.com/zhyoch/blog" target="_blank" rel="noopener noreffer"><img src="https://img.shields.io/badge/Mirror-Gitee-blue?style=flat&logo=Gitee&logoColor=C71D23&color=C71D23"></a>

你可以像本项目一样，将 [DoIt](https://github.com/HEIGE-PCloud/DoIt.git) 主题仓库添加为你的网站目录的子模块：

```bash
git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt
```

之后, 你可以通过这条命令来将主题更新至最新版本：

```bash
git submodule update --remote --merge
```

---------------------------------------

克隆本项目（含有子模块的项目）时，使用：

```bash
git clone --recurse-submodules https://github.com/zhyoch/blog.git
```

会自动初始化并更新仓库中的每一个子模块， 包括可能存在的嵌套子模块。

如果你已经克隆了项目但忘记了 `--recurse-submodules` ，那么可以运行：

```bash
git submodule update --init
```

如果还要初始化、抓取并检出任何嵌套的子模块，请使用简明的：

```bash
git submodule update --init --recursive
```