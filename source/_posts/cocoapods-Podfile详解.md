---
title: cocoapods+Podfile介绍
date: 2018-02-22 16:56:44
tags: [cocoapods, Podfile]
---
在使用cocoapods开发中必使用到Podfile文件，Podfile文件详细描述了一个或多个工程中targets的依赖关系。

<!-- more -->

以以下代码为例，简要介绍下Podfile用法：
```
target 'MyApp' do
use_frameworks!
pod 'Alamofire'
end
```
### 各参数意思：
永远使用最新版本：
```
pod 'Alamofire'
```
使用固定版本3.0：
```
pod 'Alamofire', '3.0'
```
使用大于3.0小于4.0(不含4.0)的版本：
```
pod 'Alamofire', '~> 3.0'：
```
其他：
1. \> version：要求版本大于version,当有新版本时，会更新至最新版本
2. \>= version： 要求版本大于或者等于version，当有新版本时，会更新至最新版本
3. < version： 要求版本小于version，当超过version版本后，不会再更新
4. <= version：要求版本小于或者等于version，当超过version版本后，不会再更新
5. ~> version：比如version=0.1.2时，范围在[0.1.2, 0.2.0)。注意0.2.0是开区间，也就是不包括0.2.0。如果version = 0.1,那么范围就是[0.1 1.0).如果version = 3.0,那么范围就是[3.0 4.0)

### 带参数用法
使用本地路径：
```
pod 'AFNetworking', :path => '~/Documents/AFNetworking'
```
使用主分支的版本,也就是默认写法：
```
pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git'
```
不使用master，而是使用指定的分支：
```
pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :branch => 'dev'
```
使用指定的提交版本：
```
pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :commit => '082f8319af'
```
使用指定的tag（标签，发布库的版本时，通常版本号与tag号是一致的）：
```
pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :tag => '0.7.0'
```
一般spec文件与Podfile文件在同一个目录,并且spec文件的名字与依赖库的名字一致.如果不指定spec文件,则表示默认用根目录的spec文件，可以这样指定spec文件：
1. spec文件在网络上
```
pod 'JSONKit', :podspec => 'https://example.com/JSONKit.podspec'
```
2. spec文件在本地
```
podspec :path => '/Documents/PrettyKit/PrettyKit.podspec'
```














