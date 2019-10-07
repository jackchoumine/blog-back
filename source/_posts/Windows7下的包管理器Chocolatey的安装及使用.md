---
title: Windows7下的包管理器Chocolatey的安装及使用
tags:
  - 数码产品
categories:
  - - 开发工具
date: 2019-10-07 21:16:39
---


以前安装过 chocolatey，win7 下报错，试了几次都没成功，今天学习 electron，又安装了一次，居然成功了。记录一下安装和使用。
<!-- more -->
***

@[TOC]

## 安装

win7 终端下，或者cmder下，输入下面的字符串，然后回车:
```bash
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
没有错就显示安装信息了，安装成功后可能会报警告，别管它，然后检查是否安装成功：
在 git bash 或者 cmder 输入：
```bash
choco
```
显示版本号等信息，就表示安装成功了。

## 使用

`choco -h` 可查看常用命令，这里记录常用的命令：

```bash
choco <command> -h # 查看命令帮助 choco list -h  查看 list 用法
choco list <filter> # 搜索本地或者远程的安装包  clist 简写 search / find 和 list 功能相同
choco list -li # 查看本地安装的软件
choco list -lo # 列出已安装
choco list --local # 查看本地通过 choco 安装的软件
choco info <pkg> # 查看安装包信息

choco install <pkg1> <pkg2> # 安装
choco install --yes <pkg1> <pkg2> # 安装时无需确认
choco install <pkg> -y # 同上
cinst <pkg> -y --dir DIR # 指定 DIR 为安装路径
cinst <pkg> --version n #指定版本号安装
choco uninstall <pkg> # 卸载软件
cuninst <pkg> # 卸载

choco outdated # 查看可更新的软件

# 更新
choco upgrade <pkg|all> [<pkg>] # 更新所有或多个软件
cup <pkg|all> [<pkg>] # 更新所有或多个软件
cup <pkg> --version 1.0.0 # 更新某个版本
```
## 使用 gui

使用命令不习惯，还可以使用 gui。
```bash
cinst chocolateygui -y # 安装
```
在`所有程序`里搜索`choco` ，点击启动gui了。

还可在 [chocolatey商店](https://chocolatey.org/packages) 查找软件。

## 其他用法

修改安装的默认路径：
- 花钱使用高级版本；
- 修改注册表，谨慎操作，修改了，可能会导致某些软件运行报错，比如firefox。


## 参考
- [Windows7下的包管理器Chocolatey的安装](https://blog.csdn.net/u013253924/article/details/83149821)
- [Window上更方便的软件安装方式 — Chocolatey](https://zhuanlan.zhihu.com/p/57663273)
- [Chocolatey软体管理神器](https://blog.tdccc.com.tw/319/)
- [设置软件默认安装路径（一劳永逸）](https://blog.csdn.net/vrmogui/article/details/88593365)

