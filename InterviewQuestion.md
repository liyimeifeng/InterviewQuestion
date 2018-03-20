
### 1、app进程保活 ###
- 双进程守护互相拉起
- 启用前台服务startForeground()，设置为前台进程
- 在service的onstart方法里返回 STATR_STICK 
- Manifest文件添加属性值为android:persistent=“true” （需要系统签名）
- 监听系统静态广播 ，如开机、屏幕解锁、屏幕亮灭，监听到就拉起进程
- AlarmManager唤醒
- 开启1像素activity保活
- 应用见互相拉起，腾讯系，阿里系，百度系

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
### 6、threadlocal ###
### 7、性能优化、布局优化 ###
	性能优化主要有布局优化、绘制优化、内存泄漏优化、响应速度优化、线程优化、bitmap优化等
	
	布局优化：尽量使用Relativelayout，减少层级绘制，多使用<include>、<merge>(结合include使用，可去除include之中重复的linearlayout
	)、<viewstub>(继承自view，宽高都是0，轻量级)
	绘制优化：避免在onDraw当中执行大量操作，耗时任务和循环操作。
	内存泄漏主要有：图片加载过大过量、静态变量过多或者静态变量持有activity的引用、单例模式注册监听持有activity的引用、属性动画在
	onDestroy中没有停止（也会持有activity的引用，即使activity关闭，属性动画也一直存在）
	线程优化：采用线程池，控制最大并发数，避免经常new Thread带来系统开销，消耗资源
	建议：
	避免过多的创建对象
	常量使用static final来修饰
	尽量用静态内部类
	采用软引用和弱引用
	采用内存缓存和磁盘缓存
	***app进程启动的时候，类被ClassLoader加载，静态变量被分配内存，ClassLoader生命周期和进程一致，CL不存在则类被卸载，则静态变量被销毁。
	   当Activity消毁时，其中的静态变量是存在的，因为静态变量是属全局变量、类变量（不是new出来的那种对象）静态变量是整个应用程序的公共变量。
	   只有整个应用程序的进程被销毁，静态变量才会被清除，在onDestory里添加android.os.Process.killProcess(android.os.Process.myPid())干掉进程
### 8、点击两次返回退出app、timertask ###
### 9、屏幕适配（18:9）、常见布局  ###
### 10、service生命周期 ###
### 11、view缓存 ###
12、socket通信
13、@DrawableRes图片注解
14、mvp架构 mvvm架构
15、webview使用解析及安全漏洞
16、二分算法，快速排序、冒泡排序、递归算
17、自定义view
18、recyclerview优化、item缓存
### 19、图片缓存、OOM问题、Lrucache算法 ###
	前有软引用softRefenrence、弱引用，后有Lrucache算法，即最近最少使用算法，标记最近最少改动的文件。先计算应用总内存大小，一般再设置缓存大小为总内存的几分之一，
	缓存超出就删除就缓存，也有DiskLruCache，硬盘缓存算法，需要设置SD卡缓存路径，一般为/sdcard/Android/data/package_name/cache。
	现在有glide、fresco等图片加载框架，可动态设置缓存机制。
### 20、断点续传 ###
	先使用数据库实时存储文件下载到哪个位置
	然后GET请求中的setRequestProperty()方法可以告诉服务器，如setRequestProperty("Range","bytes=" + start + "-" + info.getLength());  
	最后在本地的文件下载写入时，调用RandomAccessFile的seek()方法在文件中的任意位置进行写入操作
### 21、数据结构collection、arraylist ###
### 22、网络请求超时时间 ###
### 23、activity启动模式 ###
### 24、圆形图片剪裁 ###
### 25、git代码冲突解决 ###
### 26、linux内核 ###
### 27、职业规划，工作因素自定义排序 ###
### 28、spannstringbuilder ###
### 29、jvm、davilk、RPT ###
### 30、组播、广播 ###
### 31、 线程的优先级 ###
### 32、获取手机内联系人、电话号码 ###
