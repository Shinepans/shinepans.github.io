---
layout: post
title:  Mac终端配置攻略
categories: [mac]
---

先看下图。如果你喜欢请继续看这篇文章，如果你不喜欢，建议早点关闭此网页，珍惜时间，人生苦短，去干其他更有意义的事情。

![展示图片][terminal-wel]

## 安装iterm(x) ##

“一切软件都去官网下载。”这是我奉行的观念。所以不多说直接访问[iTerm]。版本自行选择，下载之后移动到应用程序之中即可。

## 安装oh-my-zsh ##

使用cat /etc/shells先看一下系统之中存在的shell。

```shell
cat /etc/shell
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

但值得注意的是Mac OS 默认的shell是bash。但是，Mac OS默认比如Linux多安装了zsh。千万别小瞧了这个zsh。zsh被众多的人称之为“终极shell”。为什么说是终极除了功能的强大之后，因为它的配置也是比较复杂的。还有，这个世界上牛人实在是太多了。有外国的牛人开发了一个叫做[oh-my-zsh]的开源项目。这才让zsh在大众程序员之中流行开来。

首先：把shell切换到zsh。

```shell
chsh -s /bin/zsh
```

然后：安装oh-my-zsh

```shell
// 自动安装
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
// 手动安装
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
最后：配置oh-my-zsh

编辑.zshrc。比如里面有个plugin参数，看看范例，把自己需要的plugin加入进去。再看看.oh-my-zsh目录下面的plugins，有哪些扩展你需要的，加进去尝试尝试。比如：我现在用的最爽的git, autojump等等。

## 安装powerline ##

powerline是个好东西，因为加上之后命令行的目录显示更有层次感。

首先：下载powerline。

```shell
# 下载
git clone https://github.com/milkbikis/powerline-shell
# 安装
cd powerline--shell
python install.py
```

然后：修改.zshrc文件，把powerline引入进来

```
function powerline_precmd() {
  export PS1="$(/path/to/powerline-shell.py $? --shell zsh 2> /dev/null)"
}

function install_powerline_precmd() {
  for s in "${precmd_functions[@]}"; do
    if [ "$s" = "powerline_precmd" ]; then
      return
    fi
  done
  precmd_functions+=(powerline_precmd)
}

install_powerline_precmd
```

最后：使配置文件生效

```
source .zshrc
```

这个时候是不是看到“漂亮”的显示条了？为什么漂亮两个字用引号引起来了，因为有乱码，万恶的乱码。下面解决乱码问题。

```
# 下载字体
git clone https://github.com/Lokaltog/powerline-fonts
# 安装字体
cd powerline-font
./install.sh
```

然后，将iTerm的默认字体是设置为power-line的一种。然后，是不是格外的漂亮。


## 感谢 ##

[MacTalk]

[oh-my-zsh]

[iTerm]



{% if site.model == 'pub' %}
[terminal-wel]:   {{ site.pub.image }}mac-iterm-zsh.png "展示图片"
{% else %}
[terminal-wel]:   {{ site.dev.image }}mac-iterm-zsh.png "展示图片"
{% endif %}


[iTerm]:https://www.iterm2.com/downloads.html
[oh-my-zsh]:https://github.com/robbyrussell/oh-my-zsh
[MacTalk]:http://macshuo.com/?p=676





