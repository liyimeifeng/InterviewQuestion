
### 1、app进程保活 ###
- 双进程守护互相拉起
- 启用前台服务startForeground()，设置为前台进程
- 在service的onstart方法里返回 STATR_STICK 
- Manifest文件添加属性值为android:persistent=“true” （需要系统签名）
- 监听系统静态广播 ，如开机、屏幕解锁、屏幕亮灭，监听到就拉起进程
- AlarmManager唤醒
- 开启1像素activity保活
- 应用间互相拉起，腾讯系，阿里系，百度系

### 2、线程同步 ###
-  synchronized关键字，同步代码块同步方法
-  使用volatile关键字，告诉虚拟机该域会被其它线程更新，每次使用该域都要重新计算
-   ReenreantLock类（会降低程序运行效率，不推荐）
-   使用ThreadLocal管理变量达到局部变量效果，你访问你的，我访问我的
-   使用阻塞队列LinkedBlockingQueue<E>
-   使用原子变量

### 3、fragment被绑定生命周期，service生命周期 ###
![fragment生命周期](https://i.imgur.com/OvrLiYe.jpg)
![关系图](https://i.imgur.com/RrHXzoJ.jpg)

**同一服务，多次启动，服务实际执行的过程
第一次 启动服务时，运行 `onCreate` -->`onStartCommand`
后面在启动服务时，服务只执行`onStartCommand`
在实际使用过程中，通过Intent 传递数据，在`onStartCommand`中执行。**
![service生命周期](https://i.imgur.com/MNbIorq.png)
### 4、触摸事件分发机制 ###
事件传递的顺序：Activity -> ViewGroup -> View
![事件传递机制](https://i.imgur.com/T6xPrpz.png)
![事件分发U型](https://i.imgur.com/cViWJAF.png)
### 5、hashmap原理 ###
	HashMap是基于hashing的原理，我们使用put(key, value)存储对象到HashMap中，使用get(key)从HashMap中获取对象。
	当我们给put()方法传递键和值时，我们先对键调用hashCode()方法，返回的hashCode用于找到bucket位置来储存Entry对象。
### 6、threadlocal ###
### 7、性能优化、布局优化 ###
- App启动速度优化
- 布局优化、绘制优化
- 内存优化
- apk瘦身
- 电量优化
- 线程优化
- 内存泄漏优化
	
	布局优化：尽量使用Relativelayout，减少层级绘制，多使用<include>、<merge>(结合include使用，可去除include之中重复的linearlayout
	)、<viewstub>(继承自view，宽高都是0，轻量级)
	绘制优化：避免在onDraw当中执行大量操作，耗时任务和循环操作。
	内存泄漏主要有：图片加载过大过量、静态变量过多或者静态变量持有activity的引用、单例模式注册监听持有activity的引用、属性动画在
	onDestroy中没有停止（也会持有activity的引用，即使activity关闭，属性动画也一直存在）
	线程优化：采用线程池，控制最大并发数，避免经常new Thread带来系统开销，消耗资源
	建议：
- 避免过多的创建对象
- 常量使用static final来修饰
- 尽量用静态内部类（静态内部类：不依赖于外部，相当于重新建立的一个新类，所以不能引用外部非静态成员。如果使用非静态内部类。当非静态内部类没有销毁的时候会一直占有其外部类的实例，当外部类实例退出比如Activity退出，service销毁，内部类占有实例。导致GC不销毁其在内存中的占有。就造成内存泄漏问题。典型例子如AsyncTask）
- 采用软引用和弱引用
- 采用内存缓存和磁盘缓存

	***app进程启动的时候，类被ClassLoader加载，静态变量被分配内存，ClassLoader生命周期和进程一致，CL不存在则类被卸载，则静态变量被销毁。
	   当Activity消毁时，其中的静态变量是存在的，因为静态变量是属全局变量、类变量（不是new出来的那种对象）静态变量是整个应用程序的公共变量。
	   只有整个应用程序的进程被销毁，静态变量才会被清除，在onDestory里添加android.os.Process.killProcess(android.os.Process.myPid())干掉进程
### 8、点击两次返回退出app、timertask ###
    // 是否退出程序 
    private static Boolean isExit = false; 
    // 定时触发器 
    private static Timer tExit = null;
    public boolean onKeyUp(int keyCode, KeyEvent event) { 
     
     if (keyCode == KeyEvent.KEYCODE_BACK) { 
      if (isExit == false) { 
       isExit = true; 
       if (tExit != null) { 
    tExit.cancel(); // 将原任务从队列中移除 
       } 
       // 重新实例一个定时器 
       tExit = new Timer(); 
       TimerTask task = new TimerTask() { 
    @Override 
    public void run() { 
     isExit = false; 
    } 
       }; 
       Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_SHORT).show(); 
       // 延时两秒触发task任务 
       tExit.schedule(task, 2000); 
      } else { 
       finish(); 
       System.exit(0); 
      } 
      return true; 
     } 
     return super.onKeyUp(keyCode, event); 
    }

第二种方式

    // 定义一个变量，来标识是否退出
    private static boolean isExit = false;
    
    Handler mHandler = new Handler() {
    
    @Override
    public void handleMessage(Message msg) {
    super.handleMessage(msg);
    isExit = false;
    }
    };
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_BACK) {
    exit();
    return false;
    }
    return super.onKeyDown(keyCode, event);
    }
    
    private void exit() {
    if (!isExit) {
    isExit = true;
    Toast.makeText(getApplicationContext(), "再按一次退出程序",
    Toast.LENGTH_SHORT).show();
    // 利用handler延迟发送更改状态信息
    mHandler.sendEmptyMessageDelayed(0, 2000);
    } else {
    finish();
    System.exit(0);//经测试有没有finish，都一样；
    
    }
    }

### 9、屏幕适配（18:9）、常见布局  ###

- LinearLayout
- RelativeLayout
- FrameLayout
- TabLayout
- AbsoluteLayout

	（1）、对于不同尺寸的设备
	1 使用 "wrap_content" 和 "match_parent"
	2 使用相对布局
	3 使用尺寸限定符
	4 使用宽度限定符 (sw600dp 或者 w600dp)
	5 使用方向限定符
	6 使用Nine-patch 图片
	7 使用PercentFrameLayout

	（2）、对于不同密度的设备
	1 使用使用设备独立像素（dp 或sp）
	2 提供可选择性的位图
	3 、使用流式布局


### 10、http请求头，与https的区别 ###
https由http + SSL协议来进行加密，SSL依靠证书来验证服务器的身份。

- https协议需要申请CA证书，会产生一定的费用
- http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议
- 两者用的完全不同的连接方式，http用的端口是80，https端口是443
- http连接是无状态的，https是加密传输、身份认证的网络协议，更安全

HTTP请求报文由3部分组成（**请求行+请求头+请求体**）

![请求报文](https://i.imgur.com/VThSUc0.jpg)
![请求详细内容](https://i.imgur.com/VwO6jO4.jpg)
**常见的HTTP请求报文头属性**

- Accept 告诉服务端 客户端接受什么类型的响应 `Accept:text/html,application/xhtml+xml`
- Cookie 客户端的Cookie就是通过这个报文头属性传给服务端
`Cookie: $Version=1; Skin=new;jsessionid=5F4771183629C9834F8382E23BE13C4C`
- Referer表示这个请求是从哪个URL过来的
`Referer:https://www.baidu.com`
- Cache-Control 对缓存进行控制，要还是不要，要多久`Cache-Control: no-cache `
- Accept-Language 接受的语言 `Accept-Language:zh-CN,zh;q=0.9`
- User-Agent 代理服务器`User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64)`
### 11、view缓存 ###
### 12、socket通信 ###
### 13、@DrawableRes图片注解、@LayoutRes注解 ###
   参数前加上@DrawableRes注解即表示参数只接受图片类型
### 14、mvp架构 mvvm架构 ###
### 15、webview使用简述、安全漏洞及android代码和js交互 ###
**基本用法：**

加载在线URL(必须在主线程中执行,网址必须完整即以http://开始)
>void loadUrl(String url)

想实现在webview中打开网址而不是打开系统浏览器则需要设置
>mWebView.setWebViewClient(new WebViewClient());

加载本地的html代码：
>url = "file:///android_asset/web.html"; //Assets目录下
>
>url = "file://" + File_Path + "web.html"; //手机目录下
>
>或者 url = "file:///storage/emulated/0/offline/web.html"

还有一些基本设置
>//开启javascript支持  
>webSettings.setJavaScriptEnabled(true);   
>// 设置可以支持缩放  
>webSettings.setSupportZoom(true);  
>// 设置出现缩放工具  
>webSettings.setBuiltInZoomControls(true); 

**JS调用Java代码**

主要是用到WebView下面的一个函数
>public void addJavascriptInterface(Object obj, String interfaceName)

这个函数有两个参数：

- Object obj：interfaceName所绑定的对象
- String interfaceName：所绑定的对象所对应的名称

举一个例子，点击JS中的按钮调用Android的Toast显示，下面是Android中代码

    public class MyActivity extends Activity {
        private WebView mWebView;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
            mWebView = (WebView) findViewById(R.id.webview);

            WebSettings webSettings = mWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            mWebView.addJavascriptInterface(new JSBridge(), "android");
            mWebView.loadUrl("file:///android_asset/web.html");
        }

        public class JSBridge {
            //在android:targetSdkVersion数值为17（Android4.2）及以上的APP中，JS只能访问带有 @JavascriptInterface注解的Java函数，所以如果你的android:targetSdkVersion是17+，与JS交互的Native函数中，必须添加JavascriptInterface注解，不然无效
            @JavascriptInterface
            public void toastMessage(String message) {
                Toast.makeText(getApplicationContext(), "JS--->Natvie:" + message, Toast.LENGTH_LONG).show();
            }
        }

    }	

html代码如下：

    <!DOCTYPE html>
    <html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<title>Title</title>
    	<h1>WebView加载本地HTML</h1>
    	<input type="button" value="js调native" onclick="ok()">
    </head>
    <body>
    <script type="text/javascript">
    	function ok() {
       		android.toastMessage("我是来自JS的消息！");
    	}
    </script>
    </body>
    </html> 

**Java调用Js代码**

    public class MyActivity extends Activity {
        private WebView mWebView;
        private Button mBtn;

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);

            mWebView = (WebView) findViewById(R.id.webview);
            mBtn = (Button) findViewById(R.id.btn);

            WebSettings webSettings = mWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            mWebView.loadUrl("file:///android_asset/web.html");

            mBtn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    mWebView.loadUrl("javascript:sum(3,8)");
                }
            });
        }
    }

看看在JAVA中调用JS函数的方法：

    String url = "javascript:methodName(params……);"  
    webView.loadUrl(url);
javascript:伪协议让我们可以通过一个链接来调用JavaScript函数 ，中间methodName是JavaScript中实现的函数 ，jsonParams是传入的参数列表 。

html代码：


    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
    	<title>Title</title>
    	<h1>WebView加载本地HTML</h1>
    	<input type="button" value="js调native" onclick="ok()">
	</head>
	<body>
	<script type="text/javascript">
    	function ok() {
       		android.toastMessage("我是来自JS的消息！");
    	}
    	function sum(i,m){
       		alert("Native--->JS  sum=" + (i + m));
    	}
	</script>
	</body>
	</html> 

接着用java去取得Js中的返回值

	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Title</title>
	    <h1>WebView加载本地HTML</h1>
	    <input type="button" value="js调native" onclick="ok()">
	</head>
	<body>
	<script type="text/javascript">
	    function ok() {
	       android.toastMessage("我是来自JS的消息！");
	    }
	    function sum(i,m)
	    {   
	       var result = i+m;
	       alert("Native--->JS  sum=" + result);
	       android.onSumResult(result);
	    }
	</script>
	</body>
	</html>

java代码同上就要这样改：

    public class JSBridge {
        //在android:targetSdkVersion数值为17（Android4.2）及以上的APP中，JS只能访问带有 @JavascriptInterface注解的Java函数，所以如果你的android:targetSdkVersion是17+，与JS交互的Native函数中，必须添加JavascriptInterface注解，不然无效
        @JavascriptInterface
        public void toastMessage(String message) {
            Toast.makeText(getApplicationContext(), "JS--->Natvie:" + message, Toast.LENGTH_LONG).show();
        }

        @JavascriptInterface
        public void onSumResult(int result) {
            Toast.makeText(this,"received result:"+result,Toast.LENGTH_SHORT).show();
        }
    }

**Android 4.4之后有了改动：**

    function sum(i,m)
    {
       var result = i+m;
       return result;
    }

其次java代码时用`evaluateJavascript`方法调用：

    mWebView.evaluateJavascript("sum(3,8)", new ValueCallback<String>() {
        @Override
        public void onReceiveValue(String value) {
            Toast.makeText(getApplicationContext(),"Android 4.4 received result:"+value,Toast.LENGTH_SHORT).show();
        }
    });

注意：


1. evaluateJavascript须在html加载完毕后执行，否则返回的value为null。 
2. 上面限定了结果返回结果为String，对于简单的类型会尝试转换成字符串返回，对于复杂的数据类型，建议以字符串形式的json返回。
3.  evaluateJavascript方法必须在UI线程（主线程）调用，因此onReceiveValue也执行在主线程。 


### 16、二分算法，快速排序、冒泡排序、递归算法 ###




### 17、自定义view,自定义viewgroup，刷新几次 ###
- onMeasure
- onLayout
- onDraw
### 18、recyclerview优化、item缓存 ###

### 19、图片缓存、算法 ###
前有软引用softRefenrence、弱引用，后有Lrucache算法，即最近最少使用算法，标记最近最少改动的文件。先计算应用总内存大小，一般再设置缓存大小为总内存的几分之一，
缓存超出就删除就缓存，也有DiskLruCache，硬盘缓存算法，需要设置SD卡缓存路径，一般为/sdcard/Android/data/package_name/cache。
	现在有glide、fresco等图片加载框架，可动态设置缓存机制。
	例如使用Glide会默认加载内存缓存和硬盘缓存，可以在代码中配置`skipMemoryCache(true)`跳过内存缓存，磁盘缓存默认缓存的处理图(RESULT)，通常内存缓存最大空间(maxSize)=每个进程可用的最大内存 * 0.4，磁盘缓存大小为250MB
### 20、断点续传 ###
	先使用数据库实时存储文件下载到哪个位置
	然后GET请求中的setRequestProperty()方法可以告诉服务器，如setRequestProperty("Range","bytes=" + start + "-" + info.getLength());  
	最后在本地的文件下载写入时，调用RandomAccessFile的seek()方法在文件中的任意位置进行写入操作
### 21、数据结构collection、arraylist ###
### 22、网络请求超时时间 ###
   包括connectTimeout，readTimeout、writeTimeout
### 23、activity启动模式 ###
  standard、singleTop、singleInstance、singleTask
### 24、圆形图片剪裁 ###
### 25、git代码冲突解决 ###
 	解决本地与远程库冲突

      git add filename
      git commit 
      git pull --rebase 
     (若有冲突，分两种，一是在不同的地方冲突，git可自己合并。二是在同一个地方冲突，自己手动更改，然后)
      git rebase --continue
     (重复第三、四步直到所有冲突都解决)
      git push

如果是在相同文件的不同区域冲突，可以

	git fetch
	git merge orign/master
	git push

### 26、linux内核 ###
### 27、职业规划，工作权重自定义排序 ###
### 28、spannstringbuilder ###
   比StringBuilder强大的字符拼接接口，可以更改字符串背景色，某段到某段的字符style，甚至可以将某段字符替换成图片等
### 29、jvm、davilk、ART ###
  JVM是一种堆栈机器，而Dalvik虚拟机则是寄存器机，Dalvik允许在有限的内存中同时运行多个虚拟机的实例，并且每一个Dalvik应用作为一个独立的Linux进程执行，独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。
  Dalvik Android 4.4 及其以下平台使用的虚拟机； 
  ART Android4.4以上平台使用的虚拟机技术；
  Dalvik虚拟机在程序运行过程中不断的进行将字节码编译成机器码的工作。
  ART是在应用程序安装的过程中，就已经将所有的字节码重新编译成了机器码。应用程序运行过程中无需进行实时的编译工作，只需要进行直接调用，提高了应用程序的运行效率，减少了手机的电量消耗，提高了移动设备的续航能力，在垃圾回收等机制上也有了较大的提升。
  相对于Dalvik虚拟机模式，ART模式下Android应用程序的安装需要消耗更多的时间，同时也会占用更大的储存空间（指内部储存，用于储存编译后的代码）,但节省了很多Dalvik虚拟机用于实时编译的时间
### 30、组播、广播 ###
  组播MulticastSocket使用UDP对一定范围内的地址发送相同的一组packet，即一次可以向多个接受者发出信息，其与单播的主要区别是地址的形式，IP协议分配了一定范围内的地址空间给多播，多播只能使用这个范围内的IP，IPv4中的组播地址范围为224.0.0.0到239.255.255.255，部分手机需要在代码中获取组播锁打开组播功能。
  即组播是一对一组，接受方要先加入组才能接收到，广播发出去后，一对所有，有人注册action就能接受到
### 31、 线程的优先级 ###
- Android线程API `android.os.Process.setThreadPriority (int priority)`

    priority：【-20, 19】，高优先级 -> 低优先级
- java线程API  `java.lang.Thread.setPriority (int priority)`
    priority：【1, 10】，低优先级 -> 高优先级
### 32、获取手机内联系人、电话号码 ###
getContentResolver
### 33、OOM问题 ###
- 图片处理使用三级缓存
- 少用静态变量
- 少用枚举
- 对I/O、Cursor使用完之后要关闭
- 尽量使用静态内部类防止Activity泄露，例如AsyncTask会隐士的持有Activity的引用
### 34、Rxjava操作符 ###
### 35、崩溃、bug、ANR收集 ###

- 自己封装Logutil
- 腾讯Bugly、友盟
- 表脸的做法android:largeHeap="true"使用最大内存值
- 三方测试，如testin、优测
- 继承UncaughtExceptionHandler，当程序发生Uncaught异常的时候,有该类来接管程序,并记录错误日志
- 自己搭建bug收集平台
- ANR会自动创建文件记录，使用adb pull /data/anr/traces.txt拉出文件 


### 36、IM实时通讯 ###
XMPP协议一句话总结就是一个可以用于IM功能的协议，传输的是xml数据

自建的有**openfire+spark+smack**实现即时通讯

- openfire是一个即时通讯服务器，也称之为即时通讯平台，基于XMPP协议，用于信息的转发
- spark从本质上来说就是一个运行在PC上的java程序，可以看成是官方为我们实现好的运行在PC上的客户端，我们只需要下载使用即可
- smack是一套封装好了的用于实现XMPP协议传输的API，是一个非常简单并且功能强大的类库，给用户发送消息只需要三行代码

**第三方IM、推送服务**

- 极光推送、极光IM、极光分享
- 环信SDK（推荐）
- 融云
- 网易云信
- 腾讯信鸽（主要做推送）

**MQTT协议**

>MQTT是Android端实现消息推送方案之一，是一个轻量级的消息发布/订阅协议，它是实现基于手机客户端的消息推送服务器的理想解决方案



