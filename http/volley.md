# Volley

## Volley基本使用介绍

### Volley是什么？ 
Volley是Google在2013 Google I/O 大会发布的Android平台网络通讯库，旨在帮助开发者实现更快速，简单，健壮的网络通讯。同时Volley也支持网络图片的缓存加载功能。
<img src='https://raw.githubusercontent.com/daguye918/Images/master/volley.png' width='500'/>   
在发布时所配的是这样一张图片，我们可以看到多人发射弓箭的场景。   
谷歌在高速我们，volley适用于数据量不大，但是通讯频率较高的场景。   

源码托管地址：
https://android.googlesource.com/platform/frameworks/volley

官方所提供的教程：
http://developer.android.com/training/volley/index.html

### Volley基本使用

##### 下载Volley源码
我们有几种方式来下载Volley源码：   

    1. git命令克隆：git clone https://android.googlesource.com/platform/frameworks/volley   
    2. 直接访问 https://android.googlesource.com/platform/frameworks/volley   
    3. 从极客学院课程资料当中
#####实现一个基本HTTP请求-StringRequest   
接下来，我们来使用volley实现一个最简单的http请求。  
 
所需权限：   

	<uses-permission  android:name="android.permission.INTERNET"/>

我们只需要三部曲就可以实现：     

    1.创建RequestQueue--请求队列   
    2.创建Request--请求(可从多种继承子类中选择适用，返回不同数据类型)
    3.将Request添加到RequestQueue中
基本代码实现：   
```java    
StringRequest mPostRequest=new StringRequest( "http://www.jikexueyuan.com", new Listener<String>() {

			@Override
			public void onResponse(String response) {
				// TODO Auto-generated method stub
				Log.e("jikexueyuan", "请求结果："+response);
			}
		}, new ErrorListener() {

			@Override
			public void onErrorResponse(VolleyError error) {
				// TODO Auto-generated method stub
				Log.e("jikexueyuan", "出错啦："+error.getMessage());
			}
		});
		mQueue.add(mPostRequest);
```
只需要简单几句代码，我们就可以实现一个基本的http请求。不再需要顾虑线程等复杂问题。

#####实现Post请求方式并传递参数
有些时候，出于安全等方面的考虑，我们会选择使用post方式来实现http并且传递相关参数，那么，在Volley当中，我们需要怎么来实现，从哪里设置参数呢？   
很简单，Request默认提供2中构造参数，第二种构造参数可以设置我们请求的类型，同时，我们可以重写getParams方法来设置我们所要传递的参数，代码如下：   

```java    
StringRequest mPostRequest=new StringRequest(Method.POST, "http://www.jikexueyuan.com", new Listener<String>() {

			@Override
			public void onResponse(String response) {
				// TODO Auto-generated method stub
				Log.e("jikexueyuan", "请求结果："+response);
			}
		}, new ErrorListener() {

			@Override
			public void onErrorResponse(VolleyError error) {
				// TODO Auto-generated method stub
				Log.e("jikexueyuan", "出错啦："+error.getMessage());
			}
		}){
			@Override
			protected Map<String, String> getParams() throws AuthFailureError {
				// TODO Auto-generated method stub
				Map<String, String> map=new HashMap<String, String>();
				map.put("参数名1", "参数值1");
				map.put("参数名2", "参数值2");
				return map;
			}
		};
		mQueue.add(mPostRequest);
```
#####请求队列的相关操作：取消、tag设置
#####使用Volley加载网络图片的3种方式：

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







