---
layout: post
title:  PHP多版本共存
categories: [php]
---

随着PHP7.0的到来，很多PHPer都会按捺不住自己内心的激动。想去试试PHP7.0，去感受一下直接跨过6来到7的PHP。这个时候你又不能抛弃之前的PHP5.x，所以你需要一个方案来实现多版本PHP共存，并且切换自如。这篇文章就介绍如何在Mac上实现多版本PHP共存。

## 安装多版本PHP ##

使用强大的brew安装54,55,56,70版本的PHP。

```shell
# 安装php54
brew install php54

# 安装php55
brew unlink php54
brew install php55

# 安装php56
brew unlink php55
brew install php56

# 安装php7.0
brew unlink php56
brew install php70

```

到/usr/local/Cellar/下查看PHP的安装情况。发现PHP已经安装成功。

## 安装php-version ##

安装[php-version]进行PHP版本的管理。安装的方法如下:

```shell
brew tap homebrew/dupes
brew tap josegonzalez/homebrew-php
brew install php-version

# 注意：untap josegonzalez/homebrew-php
brew untap josegonzalez/homebrew-php
```

编辑 ~/.zshrc 文件，在最后一行加入如下内容。然后soure ~/.zshrc

```shell
source $(brew --prefix php-version)/php-version.sh && php-version 5
```

测试php-version

```shell
php-version
# 使用PHP7.0
php-version 7.0.0beta2
# 显示当前版本
php -v
```

![php-version显示][php-version]


## 配置Apache ##

虽然，在你的控制台php -v显示的是你用php-version 选定的PHP版本。但是，如果你不只是想让后台脚本采用你选择的PHP版本，还想让Web服务器也支持。这个时候你需要配置Web服务器的配置文件。Mac上默认服务器Apache修改配置文件 vim /etc/apache2/httpd.conf


```apache
# php 5.4
# LoadModule php5_module /usr/local/Cellar/php54/5.4.43_2/libexec/apache2/libphp5.so
# php 5.5
  LoadModule php5_module /usr/local/Cellar/php55/5.5.27_2/libexec/apache2/libphp5.so
# php 5.6
# LoadModule php5_module /usr/local/Cellar/php56/5.6.11_2/libexec/apache2/libphp5.so
# php7.0
# LoadModule php7_module /usr/local/Cellar/php70/7.0.0-beta.2/libexec/apache2/libphp7.so
```

注意：在Web服务器配置文件配置之前，需要你先用php-version选中你要配置的php。配置完之后重启Apache即可。


## 参考链接 ##

[php-version-github]

（全文完）

[php-version-github]:https://github.com/wilmoore/php-version

{% if site.model == 'pub' %}
[php-version]:   {{ site.pub.image }}php-version.png 
{% else %}
[php-version]:   {{ site.dev.image }}php-version.png 
{% endif %}





