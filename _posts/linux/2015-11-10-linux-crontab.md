---
layout: post
title:  Linux crontab 格式详解
categories: [linux]
---

## 写在前面 ##

通过crontab定时任务我们可以在固定间隔时间内之行各种脚本（shell,PHP,Python...）。其中，crontab的时间间隔单位可以为：分钟、小时、日、月、周，当然也可以是这五者的组合。这篇文章主要介绍crontab的基本格式和我自己使用到的以及网络上搜集到的crontab常用的组合。

讲个糗事：上次改一个定时任务，我想让他每一个小时执行一次，然后把分钟的位置习惯性的改成了60。［衰］

## 基本命令 ##

```crontab -l``` 列出crontab文件

```crontab -e``` 编辑crontab文件

```crontab -r``` 删除用户的crontab文件

## 基本格式 ##

```* * * * * command```

* 第1列分钟1～59
* 第2列小时1～23（0表示子夜）
* 第3列日1～31
* 第4列月1～12
* 第5列星期0～6（0表示星期天）
* 第6列要运行的命令

## 常用实例 ##

### 每分钟之行一次命令 ###

``` * * * * * command ``` 每一分钟之行一次command

### 每小时的第N分钟之行 ###

``` 3 * * * * command ``` 每小时的第3分钟之行一次command

``` 3,15 * * * * command ``` 每小时的第3分钟和第15分钟之行一次command

### 每隔xx执行一次 ###

``` */10 * * * * command ``` 每隔十分钟执行一次。一分钟外生效。

``` * */2 * * * command ``` 每隔2小时

``` 0,30 18-23 * * * command ``` 18到23点每隔30分执行一次 

### 其它  ###

``` 0 23 * * 6  commnd``` 每周六的23:00执行。

``` 0 0-4 * * *  commnd``` 每天的0点到四点执行


## 参考资料 ##

[crontab定时任务]

(全文完)

[crontab定时任务]:http://linuxtools-rst.readthedocs.org/zh_CN/latest/tool/crontab.html












