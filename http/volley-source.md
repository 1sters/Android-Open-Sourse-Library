# Volley源码剖析

## 网络数据请求详解
在了解了Volley的基本使用之后我们都知道，不论是实现http请求，还是对网络图片做缓存加载，除了使用ImageLoader方式，都会经历几个主要步骤：  
1.使用Volley创建一个RequestQueue队列     
2.新建一个相应的Request    
3.将新建的Request添加到队列中   
一：Volley.java类   
那么我们从Volley入手，来看看整个库是怎么工作的。   
我们打开Volley类。可以看到，Volley当中只有两个重载的方法newRequestQueue。用于创建一个请求队列。
一般我们默认使用newRequestQueue(Context context) 只传递一个context参数的方法。这个方法则是调用了上面两个参数的方法，后面的参数传递为null。   

在这个方法，主要做了几个事情：   
   1.设置默认的缓存文件夹
   ```java   
   private static final String DEFAULT_CACHE_DIR = "volley";
   
   File cacheDir = new File(context.getCacheDir(), DEFAULT_CACHE_DIR);
   ```
   
   2.获取APP的包名，版本号，添加userAgent到请求头中，如果获取失败则默认使用Volley/0：
   ```java    
   String userAgent = "volley/0";
        try {
            String packageName = context.getPackageName();
            PackageInfo info = context.getPackageManager().getPackageInfo(packageName, 0);
            userAgent = packageName + "/" + info.versionCode;
        } catch (NameNotFoundException e) {
        }


   3.对stack进行相关操作，得到NetWork对象。从这个代码段我们可以看出。如果stack为空得话volley会根据系统版本，选择对应的方式来创建。在2.3以前得版本，使用HttpClient来实现请求，在2.3及之后的版本则都是使用HttpUrlConnection来实现，具体实现思路大家可以回顾一下极客学院关于HttpClient和HttpUrlConnection的视频，查看HurlStack和HttpClientStack的源码来了解一下，这里就不再详细讲解。如果我们传递了自己自定义的stack 那么就直接使用。因此，如果你想要个性化自己来实现stack，就可以直接调用这个方法,自己新建一个Stack对象传递进来就可以。   
   
   ```java    
if (stack == null) {
            if (Build.VERSION.SDK_INT >= 9) {
                stack = new HurlStack();
            } else {
                // Prior to Gingerbread, HttpUrlConnection was unreliable.
                // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
                stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
            }
        }

        Network network = new BasicNetwork(stack);
```

  4.最后，根据设定的缓存文件夹新建一个缓存对象，创建一个请求队列，参数为上面创建的缓存对象及NetWork对象，然后启动请求队列，同时将队列对象作为返回值返回，我们调用这个方法就会将返回值赋值给我们创建的全局队列对象。   
  
  ```java    
  RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
  queue.start();
```

二：RequestQueue.java   
接着，我们来看看RequestQueue这个类   
首先，看看它的构造函数：   

```java     
public RequestQueue(Cache cache, Network network) {
        this(cache, network, DEFAULT_NETWORK_THREAD_POOL_SIZE);
    }
```


```java     
public RequestQueue(Cache cache, Network network, int threadPoolSize) {
        this(cache, network, threadPoolSize,
                new ExecutorDelivery(new Handler(Looper.getMainLooper())));
    }
```

```java     
public RequestQueue(Cache cache, Network network, int threadPoolSize,
            ResponseDelivery delivery) {
        mCache = cache;
        mNetwork = network;
        mDispatchers = new NetworkDispatcher[threadPoolSize];
        mDelivery = delivery;
    }
```
它有三个重载的构造函数，从传递缓存对象，网络对象，衍生到设置并发请求数量，再到设置回调。   
这里，并发请求数量默认为4，我们也可以自己根据需要定义更多的并发数量。   
在构造函数中主要是设置一些初始值   
接着我们看start方法：   

```java     
public void start() {
        stop();  // Make sure any currently running dispatchers are stopped.
        // Create the cache dispatcher and start it.
        mCacheDispatcher = new CacheDispatcher(mCacheQueue, mNetworkQueue, mCache, mDelivery);
        mCacheDispatcher.start();

        // Create network dispatchers (and corresponding threads) up to the pool size.
        for (int i = 0; i < mDispatchers.length; i++) {
            NetworkDispatcher networkDispatcher = new NetworkDispatcher(mNetworkQueue, mNetwork,
                    mCache, mDelivery);
            mDispatchers[i] = networkDispatcher;
            networkDispatcher.start();
        }
    }
    ```
    
    首先，他会将现有队列停止掉，然后分别创建一个缓存线程以及我们所设定并发数量的网络线程。并且将他们启动。
    


1.从RequestQueue入手，new、start、add都做了些什么？   
2.看看StringRequest是怎么实现的？   
3.参考StringRequest定制自己的请求类  
4.进阶优化，进行封装组合  

## 网络图片缓存加载详解

1.ImageRequest的实现方式解析
2.ImageLoader的操作流程
3.进阶定制，增加缓存到内存的方式，提高加载效率
4.NetworkImageView解析