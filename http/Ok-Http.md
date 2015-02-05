# Ok-Http基本使用介绍

## Ok-Http简介 

为Android构建的基于异步回调的Http客户端，是基于Apache HttpClient库的。所有的请求都是独立于应用程序主线程 UI 之外,但任何回调逻辑跟在线程上通过Android的handler创建消息传递执行回调是一样的。
以官网的说法，Twitter的app所使用的就是Android-Async-Http

###特点   
相对于其他库，Android-Async-Http比较特有的地方就是可以通过RequestParams直接传递文件参数上传文件。这个功能，我个人还是挺喜欢的，在平时做图片和文件上传十分的方便。同时它对文件下载的封装也做得很好。
  

源码托管地址：   
https://github.com/loopj/android-async-http   

官方网站：   
http://loopj.com/android-async-http/   

## Ok-Http基本使用

### 下载Ok-Http源码

我们有几种方式来下载Ok-Http源码：   

* 1. 从官网下载jar包：http://loopj.com/android-async-http/   
* 2. 通过github使用git命令或者直接下载源码压缩包： https://github.com/loopj/android-async-http   
* 3. 从极客学院课程资料当中

### 实现一个基本HTTP请求  

接下来，我们来使用Ok-Http实现一个最简单的http请求。  
 
所需权限：   

```xml    
	<uses-permission  android:name="android.permission.INTERNET"/>
```
使用Ok-Http十分的简单，只要创建一个AsyncHttpClient对象通过这个对象就可以做我们想做的事情   
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

只需要简单几句代码，我们就可以实现一个基本的http请求。这样的代码量是不是写起来非常的痛快？

### 实现Post请求方式并传递参数

使用Ok-Http来实现Post请求一样很简单，只需要调用post方法，并且用RequestParams来设定参数就可以：   

```java    
RequestParams params = new RequestParams();
params.put("key", "value");
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

### 实现文件上传，下载



## 总结

以上就是Ok-Http的一些基本使用方法







