---
title: cocoapods基础安装命令
date: 2018-02-22 16:21:01
tags: [cocoapods]
---
本篇文章介绍cocoapods安装相关的一些命令。

<!-- more -->

### 安装最新版cocoapods
```
sudo gem install cocoapods
sudo gem update cocoapods
pod setup
```
### 替换仓库地址
```
pod repo remove master
pod repo add master https://gitcafe.com/akuandev/Specs.git
```
最新版cocoapods貌似还是会自动更新官方地址，无奈做了个投机取巧的办法，把远程仓库的地址改成官方的：
```
pod repo remove master
pod repo add master https://gitcafe.com/akuandev/Specs.git
cd ~/.cocoapods/repos/master
git remote rm origin
git remote add origin https://github.com/CocoaPods/SpecsCocoapods
```
### 安装指定cocoapods版本
```
sudo gem install cocoapods -v 0.39.0
```
### 查看cocoapods的版本
```
pod --version
```
### 升级 gem
```
sudo gem update --system
```
### 修改镜像源
一般安装完成之后我们都修改镜像源,修改成国内的,访问速度比较快。
先删除，再添加淘宝,注意是https：
```
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
```
### 列出源
```
gem sources -l
```
### 卸载cocoapods
```
sudo gem uninstall cocoapods
sudo gem uninstall cocoapods -v 0.38.0
```
### 降级cocoapods
先卸载新版本，再安装旧版本:
```
sudo gem uninstall cocoapods -v 1.0.0
sudo gem install cocoapods -v 0.38.0
```
### 查找某个库
```
pod search AFNetworking
```
### 安装依赖
```
pod install
```
不更新本地索引：
```
pod install --no-repo-update
```
单独更新某一个依赖：
```
pod update AFNetworking
```
更新所有依赖,如果某个依赖已经是最新,则不再更新：
```
pod update
```
指定源地址,例如,指定为本地目录,这种一般用于将依赖库提前放到了本地,这样速度会快过很多：
```
pod 'AFNetworking', path: '/Users/apple/Desktop/CodeStore/AFNetworking'
```
