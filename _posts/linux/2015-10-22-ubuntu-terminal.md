---
layout: post
title:  Ubuntu终端配置攻略
categories: [linux]
---

背景：自己的Acer本现在彻底用作服务器了，用作代码的存放和一些环境的搭建，顺便深入学习一下Linux。

同样的，先看一下配置好的图。注意，该图是从Mac ssh远程登录到Ubuntu截的。(事实上和Mac上的配置方式雷同，只是Ubuntu需要多安装一些基础软件)

![展示图片][ubuntu_terminal]


## 安装zsh ##

```shell
sudo apt-get install zsh
```


## 安装Git ##

```shell
sudo apt-get install git-core
```

## 安装oh-my-zsh ##

这里再次感激oh-my-zsh,正因为你，zsh才变得如此简单好用。当然，没你之前zsh只存在于高手之间，有了你期待更多的人变成高手。

```shell
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

## 切换shel & 重启计算机 ##

```shell
# 切换
chsh -s `which zsh`
# 接着退出终端重新登录
```

## 安装pyhton ##

```shell
sudo apt-get install python-pip
```

## 下载并安装powerline ##

```
# 下载
git clone https://github.com/milkbikis/powerline-shell
# 安装
cd powerline-shell
python install.py
```

## 配置powerline ##

编辑.zshrc文件，然后在文件的最后加入如下语句，注意powerline-shell.py的路径修改成你存放的路径即可。最后注意让配置文件生效，记得执行，```source .zshrc```

```shell

function powerline_precmd() {
  # 注意这里是powerline-shell.py的路径！！！
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

## 安装powerline-font字体 ##

其实，如果我只是使用我的Mac终端访问我的ubuntu server的话我可以不用安装字体了，因为在Mac上曾经已经安装并且配置过了。但是，为了ubuntu server本身能够显示正常，所以安装并且配置字体。

```
# 下载字体
https://github.com/Lokaltog/powerline-fonts
# 安装字体
cd powerline-font
./install.sh
```

然后，如果是桌面版记得将terminal的默认字体是设置为power-line的一种。

{% if site.model == 'pub' %}
[ubuntu_terminal]:   {{ site.pub.image }}ubuntu_terminal.png "展示图片"
{% else %}
[ubuntu_terminal]:   {{ site.dev.image }}ubuntu_terminal.png "展示图片"
{% endif %}




