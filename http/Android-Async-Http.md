# Android-Async-Http基本使用介绍

## Android-Async-Http简介 

为Android构建的基于异步回调的Http客户端，是基于Apache HttpClient库的。所有的请求都是独立于应用程序主线程 UI 之外,但任何回调逻辑跟在线程上通过Android的handler创建消息传递执行回调是一样的。
以官网的说法，Twitter的app所使用的就是Android-Async-Http

###特点   
相对于其他库，Android-Async-Http比较特有的地方就是可以通过RequestParams直接传递文件参数上传文件。这个功能，我个人还是挺喜欢的，在平时做图片和文件上传十分的方便。同时它对文件下载的封装也做得很好。
  

源码托管地址：   
https://github.com/loopj/android-async-http   

官方网站：   
http://loopj.com/android-async-http/   

## Android-Async-Http基本使用

### 下载Android-Async-Http源码

我们有几种方式来下载Android-Async-Http源码：   

* 1. 从官网下载jar包：http://loopj.com/android-async-http/   
* 2. 通过github使用git命令或者直接下载源码压缩包： https://github.com/loopj/android-async-http   
* 3. 从极客学院课程资料当中

### 实现一个基本HTTP请求  

接下来，我们来使用Android-Async-Http实现一个最简单的http请求。  
 
所需权限：   

```xml    
	<uses-permission  android:name="android.permission.INTERNET"/>
```
使用Android-Async-Http十分的简单，只要创建一个AsyncHttpClient对象通过这个对象就可以做我们想做的事情   
基本代码实现：   

```java    
AsyncHttpClient client = new AsyncHttpClient();
client.get("http://www.jikexueyuan.com", new AsyncHttpResponseHandler() {

    @Override
    public void onStart() {
        //请求开始回调
    }

    @Override
    public void onSuccess(int statusCode, Header[] headers, byte[] response) {
        //请求成功回调
    }

    @Override
    public void onFailure(int statusCode, Header[] headers, byte[] errorResponse, Throwable e) {
        // 请求失败回调
    }

    
});
```
在这里。Android-Async-Http提供了许多不同返回类型的回调提供给我们使用
只需要简单几句代码，我们就可以实现一个基本的http请求。这样的代码量是不是写起来非常的痛快？   
除了以上三个基本的回调。Android-Async-Http还提供了许多相关回调方便我们在请求的各个过程做各做处理操作。
包括有：

```java    
@Override
			public void onFinish() {
				// TODO Auto-generated method stub
				super.onFinish();
			}
			
			@Override
			public void onCancel() {
				// TODO Auto-generated method stub
				super.onCancel();
			}
			
			@Override
			public void onProgress(int bytesWritten, int totalSize) {
				// TODO Auto-generated method stub
				super.onProgress(bytesWritten, totalSize);
			}
			
			@Override
			public void onRetry(int retryNo) {
				// TODO Auto-generated method stub
				super.onRetry(retryNo);
			}
```



### 实现Post请求方式并传递参数

使用Android-Async-Http来实现Post请求一样很简单，只需要调用post方法，并且用RequestParams来设定参数就可以：   

```java    
AsyncHttpClient mClient=new AsyncHttpClient();
RequestParams mParams = new RequestParams();
mParams.put("username", "admin");
mParams.put("password", "123456");
mClient.get("http://www.jikexueyuan.com",mParams,new AsyncHttpResponseHandler() {
			
			@Override
			public void onSuccess(int arg0, Header[] arg1, byte[] arg2) {
				// TODO Auto-generated method stub
				
			}
			
			@Override
			public void onFailure(int arg0, Header[] arg1, byte[] arg2, Throwable arg3) {
				// TODO Auto-generated method stub
				
			}
			@Override
			public void onStart() {
				// TODO Auto-generated method stub
				super.onStart();
			}
			});
	}
```

### 实现文件上传，下载
很多时候，我们经常会需要上传图片或者文件到服务器，或者实现版本更新的时候从服务器下载文件。这时候Android-Async-Http可以很好得帮助我们来实现这些操作。

上传文件我们只需要在RequestParams的参数中传入File类型或者InputStream类型的数据。带入到请求中。下载的话只需要使用获取byte[]类型数据的回调就可以。




## 总结

以上就是Android-Async-Http的一些基本使用方法，使用它，我们可以很方便的在项目开发当中快速的实现各种各样的网络请求。甚至是上传文件，下载文件都只需要简单的几句代码就可以做到。是不是十分方便呢。







