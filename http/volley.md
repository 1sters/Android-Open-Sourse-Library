# Volley基本使用介绍

## Volley是什么？ 

Volley是Google在2013 Google I/O 大会发布的Android平台网络通讯库，旨在帮助开发者实现更快速，简单，健壮的网络通讯。同时Volley也支持网络图片的缓存加载功能。 

![volley](images/volley.png)


在发布时所配的是这样一张图片，我们可以看到多人发射弓箭的场景。   
谷歌在告诉我们，volley适用于数据量不大，但是通讯频率较高的场景。   

源码托管地址：
https://android.googlesource.com/platform/frameworks/volley

官方所提供的教程：
http://developer.android.com/training/volley/index.html

## Volley基本使用

### 下载Volley源码

我们有几种方式来下载Volley源码：   

* 1. git命令克隆：git clone https://android.googlesource.com/platform/frameworks/volley   
* 2. 直接访问 https://android.googlesource.com/platform/frameworks/volley   
* 3. 从极客学院课程资料当中

### 实现一个基本HTTP请求-StringRequest  

接下来，我们来使用volley实现一个最简单的http请求。  
 
所需权限：   

```xml
	<uses-permission  android:name="android.permission.INTERNET"/>
```

我们只需要三部曲就可以实现：     

* 1.创建RequestQueue--请求队列   
* 2.创建Request--请求(可从多种继承子类中选择适用，返回不同数据类型)
* 3.将Request添加到RequestQueue中

基本代码实现：   

```java    
RequestQueue mQueue=Volley.newRequestQueue(this);
StringRequest mRequest=new StringRequest( "http://www.jikexueyuan.com", new Listener<String>() {

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
		mQueue.add(mRequest);
```

只需要简单几句代码，我们就可以实现一个基本的http请求。不再需要顾虑线程等复杂问题。

### 实现Post请求方式并传递参数

有些时候，出于安全等方面的考虑，我们会选择使用post方式来实现http并且传递相关参数，那么，在Volley当中，我们需要怎么来实现，从哪里设置参数呢？   
很简单，`Request`默认提供2中构造参数，第二种构造参数可以设置我们请求的类型，同时，我们可以重写`getParams`方法来设置我们所要传递的参数，代码如下：   

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

### 请求队列的相关操作：取消、tag设置

当我们发起一些请求之后，我们通常会在请求结束时来做一些页面反馈，包括弹出框提示，数据绑定展示等操作，但是，很多时候，用户往往在等待的时候退出这个页面。这个时候，我们在这个页面发起的请求就没有再继续的必要，如果不停止，还有可能因为在回调中使用activity中的一些对象而导致程序异常。   
因此，Volley提供了一个很好的机制，帮助我们来停止请求队列中的某个或者全部请求。   
我们可以使用Request的`cancel`事件或者是RequestQueue的`cancelAll("tag")`事件来取消，代码如下：

```java   

		mLoginRequest.cancel();//取消单个请求
		
		//为Request设置tag
		mLoginRequest.setTag("login");
		mNewsRequest.setTag("news");
		mMsgListRequest.setTag("msg");
		mMsgRequest.setTag("msg");
		
		//添加到队列中
		mQueue.add(mLoginRequest);
		mQueue.add(mNewsRequest);
		mQueue.add(mMsgListRequest);
		mQueue.add(mMsgRequest);
		
		//取消队列中包含msg标签的请求
		mQueue.cancelAll("msg");
```

### 使用Volley加载网络图片的3种方式     

我们在开发软件的过程中，经常都需要加载许多的网络图片，对应这些图片的下载，缓存，读取设置等操作往往令我们十分的头疼，Volley给我们提供了三个十分简单的方法来加载网络图片。

* ImageRequest
* ImageLoader
* NetworkImageView

#### 使用ImageRequest下载图片   

`ImageRequest`是继承了Request的请求，它与其他Request的使用方式一样，通过传递`URL参数`，就可以返回`Bitmap`类型的图片数据：

```java   
ImageRequest mImageRequest=new ImageRequest("http://s1.jikexueyuan.com/current/static/images/logo.png", new Listener<Bitmap>() {

			@Override
			public void onResponse(Bitmap response) {
				// TODO Auto-generated method stub
				mImage.setImageBitmap(response);//显示下载的网络图片
			}
		}, 1080, 1920, Config.RGB_565, new ErrorListener() {

			@Override
			public void onErrorResponse(VolleyError error) {
				// TODO Auto-generated method stub
				mImage.setImageResource(R.drawable.default_image); //没有读取到图片，显示默认图片
			}
		});
		mQueue.add(mImageRequest);
```

#### 使用ImageLoader将图片直接缓存加载到ImageView控件    

Volley提供了`ImageLoader`类，让我们更方便的实现网络图片的加载，它的用法主要有四步：   
   
* 1.创建RequestQueue请求队列   
* 2.创建ImageLoader对象   
* 3.获取一个ImageListener对象   
* 4.调用ImageLoader的get方法来进行加载   

主要代码如下：   

```java
ImageLoader imageLoader = new ImageLoader(mQueue, new ImageCache() {  
    @Override  
    public void putBitmap(String url, Bitmap bitmap) {  
    }  
  
    @Override  
    public Bitmap getBitmap(String url) {  
        return null;  
    }  
});  
ImageListener listener = ImageLoader.getImageListener(imageView,  
        R.drawable.default_image, R.drawable.failed_image); 
imageLoader.get("http://s1.jikexueyuan.com/current/static/images/logo.png", listener); 
```

#### 使用NetworkImageView控件直接加载图片  

`NetworkImageView`是直接继承ImageView来实现的，能够在满足ImageView所有功能之外，额外的提供了直接在控件上加载网络图片的功能，使用它需要五个步骤：   

* 1.创建RequestQueue请求队列
* 2.创建Imageloader对象
* 3.在xml布局中使用NetworkImageView进行布局
* 4.获取NetworkImageView控件实例
* 5.使用控件实例的setImageUrl方法加载网络图片

前面两步都与上面一样，我就不再介绍，直接从第三步开始，我们在xml中添加一个控件：

```xml
<com.android.volley.toolbox.NetworkImageView   
        android:id="@+id/network_image_view"  
        android:layout_width="200dp"  
        android:layout_height="200dp"  
        android:layout_gravity="center_horizontal"  
        />  
```

然后在代码中，获取该控件的实例，并且设置默认图片，加载异常图片，以及我们所要加载的网络图片的URL，如下：

```java
NetworkImageView networkImageView = (NetworkImageView) findViewById(R.id.network_image_view); 
networkImageView.setDefaultImageResId(R.drawable.default_image);  
networkImageView.setErrorImageResId(R.drawable.failed_image);  
networkImageView.setImageUrl("http://s1.jikexueyuan.com/current/static/images/logo.png",  
                imageLoader);  
```

## 总结

以上就是Volley的一些基本使用方法，熟悉了这些用法，可以大大减少我们的代码量，同时也让我们不用再烦恼线程，缓存这些头疼的问题，大大提高了开发的效率。相信在了解完之后，你就会深深的爱上它。







