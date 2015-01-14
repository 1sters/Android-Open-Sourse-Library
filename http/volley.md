# Volley

## Volley基本使用介绍

### Volley是什么？

2013 Google I/O 大会发布的Android平台网络通讯库，旨在帮助开发者实现更快速，简单，健壮的网络通讯。支持网络图片的缓存加载功能。
适用场景：数据量不大，但是通讯频率较高的场景。

官网介绍：
https://android.googlesource.com/platform/frameworks/volley

官方教程：
http://developer.android.com/training/volley/index.html

### Volley使用过程

* 1.下载Volley源码->导入->引用库或打包成jar引用
<img width="10" height="10">![Alt text](https://raw.githubusercontent.com/daguye918/Images/master/git-clone.png)
</img>
* 2.实现一个基本HTTP请求-StringRequest
* 3.实现Post请求方式并传递参数
* 4.请求队列的相关操作：取消、tag设置
* 5.使用Volley加载网络图片的3种方式：
	* ImageRequest
	* ImageLoader
	* NetworkImageView

## Volley源码剖析

### 网络数据请求详解

1.从RequestQueue入手，new、start、add都做了些什么？
2.看看StringRequest是怎么实现的？
3.参考StringRequest定制自己的请求类
4.进阶优化，进行封装组合

### 网络图片缓存加载详解

1.ImageRequest的实现方式解析
2.ImageLoader的操作流程
3.进阶定制，增加缓存到内存的方式，提高加载效率
4.NetworkImageView解析







