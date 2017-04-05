##安卓基础
#### 五种布局的特点和效率对比

#### Activity的生命周期和缓存
![](http://hi.csdn.net/attachment/201109/1/0_1314838777He6C.gif)
	
* 有a、b两个activity，当a进入b之后的一段时间，可能系统就把a回收了，这时候按back键，执行的不是a的onStart()方法，而是onCreate()方法，a被重新创建了一次，这时a的临时数据和状态就很有可能就丢失了。而Activity中的onSaveInstanceState()回调方法会保存临时的数据和状态，这个方法一定会再activity回收之前调用。方法中有一个Bundle参数，putString()、putInt()等方法需要传入两个参数，一个键一个值。数据保存之后会在 onCreate中会恢复，onCreate也有一个Bundle类型的参数。
* 关键字：onSaveInstanceState(Bundle outState),onCreate(Bundle savedInstanceState),onRestoreInstanceState(Bundle outState)。
* @see:[http://blog.csdn.net/lccly/article/details/6089667](http://blog.csdn.net/lccly/article/details/6089667)


#### 切换activity执行顺序
	* 从一个Activity通过Intent切换到另一个Activity:

		08-25 05:02:47.783: INFO/System.out(339): MainActivity------->onPause()  
		08-25 05:02:47.843: INFO/System.out(339): Another------->onCreate()  
		08-25 05:02:47.853: INFO/System.out(339): Another------->onStart()  
		08-25 05:02:47.853: INFO/System.out(339): Another------->onResume()  
		08-25 05:02:48.253: INFO/System.out(339): MainActivity------->onStop()  

	* 按Back键返回：

		08-25 05:03:48.133: INFO/System.out(339): Another------->onPause()  
		08-25 05:03:48.174: INFO/System.out(339): MainActivity------->onRestart()  
		08-25 05:03:48.174: INFO/System.out(339): MainActivity------->onStart()  
		08-25 05:03:48.174: INFO/System.out(339): MainActivity------->onResume()  
		08-25 05:03:48.483: INFO/System.out(339): Another------->onStop()  
		08-25 05:03:48.483: INFO/System.out(339): Another------->onDestroy()
		
	* 第二个Activity使用了finish（）方法，返回：

		08-25 05:10:48.053: INFO/System.out(366): Another------->onPause()  
		08-25 05:10:48.103: INFO/System.out(366): MainActivity------->onRestart()  
		08-25 05:10:48.103: INFO/System.out(366): MainActivity------->onStart()  
		08-25 05:10:48.103: INFO/System.out(366): MainActivity------->onResume()  
		08-25 05:10:48.423: INFO/System.out(366): Another------->onStop()  
		08-25 05:10:48.453: INFO/System.out(366): Another------->onDestroy()  

#### Fragment的生命周期

![](http://img.my.csdn.net/uploads/201211/29/1354170682_3824.png)
![](http://img.blog.csdn.net/20150310094648441)

#### Service的两种启动方式，如何保证一个Service不被杀死
* Context.startService() 
* Context.bindService(intent, conn, BIND_AUTO_CREATE);
* 如何保证一个Service不被杀死:
* onStartCommand方法，返回START_STICKY，手动返回START_STICKY，亲测当service因内存不足被kill，当内存又有的时候，service又被重新创建，比较不错，但是不能保证任何情况下都被重建，比如进程被干掉了....
* 提升service优先级，android:priority = "1000"（可能无效）
* 提升service进程优先级， startForeground(0x111, notification)。用户体验不好，通知栏会看到。如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart
* onDestroy方法里重启service，当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证~.~
* Application加上Persistent属性，据说这个属性不能乱设置，不过设置后，的确发现优先级提高不少，或许是相当于系统级的进程，但是还是无法保证存活
* @[http://blog.csdn.net/mad1989/article/details/22492519](http://blog.csdn.net/mad1989/article/details/22492519)

#### 广播注册的两种方式的区别
		
		//第一种方式
		IntentFilter filter = new IntentFilter("android.provider.Telephony.SMS_RECEIVED");  
		IncomingSMSReceiver receiver = new IncomingSMSReceiver();  
		registerReceiver(receiver, filter); 
	
		//第二种
		<receiver android:name=".IncomingSMSReceiver">  
		    <intent-filter>  
		         <action android:name="android.provider.Telephony.SMS_RECEIVED"/>  
		    </intent-filter>  
		</receiver>  

区别：
* 在AndroidManifest中进行注册后，不管改应用程序是否处于活动状态，都会进行监听，比如某个程序时监听 内存的使用情况的，当在手机上安装好后，不管改应用程序是处于什么状态，都会执行改监听方法中的内容。
* 在代码中进行注册后，当应用程序关闭后，就不再进行监听。我们都知道，应用程序是否省电，决定了该应用程序的受欢迎程度，所以，对于那些没必要在程序关闭后仍然进行监听的Receiver，在代码中进行注册，无疑是一个明智的选择。


#### 如何安全的退出一个已经开启多个activity的APP
* 就是定义一个List<Activity>，在Activity创建的时候加入自定义栈，销毁的时候移除栈。特别注意，因为是重复性动作，所以可以考虑在基类中处理。

#### Android的进程优先级
Android将进程分为5个等级,它们按优先级顺序由高到低依次是:
1. 前台进程(Foreground process)
2. 可视进程(Visible process)
3. 服务进程(Service process)
4. 后台进程 (Background process)
5. 空进程(Empty process)

#### 进程的保活
* 保活手段
	* 黑色保活：不同的app进程，用广播相互唤醒（包括利用系统提供的广播进行唤醒），例如：当你打开支付宝后，一会就会收到淘宝APP发来的广告（前提你装了淘宝）。。。；像微信、qq、搜狗输入法都是两个进程两个服务，应该都是相互守护的，当其中一个挂掉之后，另外一个立马把它启动起来。
	
	* 白色保活：启动前台Service，白色保活手段非常简单，就是调用系统api启动一个前台的Service进程，这样会在系统的通知栏生成一个Notification，用来让用户知道有这样一个app在运行着，哪怕当前的app退到了后台。
	
	* 灰色保活：利用系统的漏洞启动前台Service（迟早要被google封掉这一块，但是主流APP都是这样搞的。。。，使用灰色保活并不代表着你的Service就永生不死了，只能说是提高了进程的优先级。如果你的app进程占用了大量的内存，按照回收进程的策略，同样会干掉你的app。）
* 灰色保活，这种保活手段是应用范围最广泛。它是利用系统的漏洞来启动一个前台的Service进程，与普通的启动方式区别在于，它不会在系统通知栏处出现一个Notification，看起来就如同运行着一个后台Service进程一样。这样做带来的好处就是，用户无法察觉到你运行着一个前台进程（因为看不到Notification）,但你的进程优先级又是高于普通后台进程的。那么如何利用系统的漏洞呢，大致的实现思路和代码如下：

	思路一：API < 18，启动前台Service时直接传入new Notification()；
	思路二：API >= 18，同时启动两个id相同的前台Service，然后再将后启动的Service做stop处理；
	
			public class GrayService extends Service {
		
		    private final static int GRAY_SERVICE_ID = 1001;
		
		    @Override
		    public int onStartCommand(Intent intent, int flags, int startId) {
		        if (Build.VERSION.SDK_INT < 18) {
		            startForeground(GRAY_SERVICE_ID, new Notification());//API < 18 ，此方法能有效隐藏Notification上的图标
		        } else {
		            Intent innerIntent = new Intent(this, GrayInnerService.class);
		            startService(innerIntent);
		            startForeground(GRAY_SERVICE_ID, new Notification());
		        }
		
		        return super.onStartCommand(intent, flags, startId);
		    }
		
		    ...
		    ...
		
		    /**
		     * 给 API >= 18 的平台上用的灰色保活手段
		     */
		    public static class GrayInnerService extends Service {
		
		        @Override
		        public int onStartCommand(Intent intent, int flags, int startId) {
		            startForeground(GRAY_SERVICE_ID, new Notification());
		            stopForeground(true);
		            stopSelf();
		            return super.onStartCommand(intent, flags, startId);
		        }
		
		    }
		}

* 进程保活好文章 @see:[http://www.cnblogs.com/dongweiq/p/5404331.html](http://www.cnblogs.com/dongweiq/p/5404331.html)

#### Asset目录与res目录的区别
*  assert:称为原生文件，无法通过R资源清单类访问，这类文件在被打包成apk文件时是不会进行压缩的；android中有一个专门的类来处理应用对asset文件的访问，这个类就是AssetManager。其内有一个open()方法可以根据用户提供的文件名，返回一个InputStream对象供用户使用。
*  res:，这类文件在打包成apk文件时，可通过R资源清单类访问，会进行小内存优化的哦(不用的是不会被打包进app的,res/drawable下文件会被压缩处理)。

#### ANR问题，force close问题，activity，service，broadcast各自超过多少秒报ANR

* ANR: Application Not Responding 
* 产生原因： 
	1. 主线程（UI线程）响应用户操作事件时间超过5秒 
	2. BroadcastReceiver超过10秒钟任未执行完毕。
	3. Service----->20秒
* 避免方法： 
Android应用程序完全运行在一个独立的线程中。任何在主线程中运行的，需要消耗大量时间的操作都会引发ANR。因此，需要消耗大量时间的操作如访问网络和数据库，都要放到子线程中或者使用异步方式来完成。 
	
* Force Close. 
* 产生原因： 
程序出现异常，一般像空指针、数组越界、类型转换异常等。 
避免方法： 
编写程序时要思维缜密，异常出现时可以通过logcat查看抛出异常代码出现的位置，然后到程序中进行修改。

#### Activity启动模式，intent匹配规则
* standard：标准模式。
* singleTop：栈顶复用模式。【应用】：singleTop适合接收通知启动的内容显示页面。例如，某个新闻客户端的新闻内容页面，如果收到10个新闻推送，每次都打开一个新闻内容页面是很烦人的。
* singleTask：栈内复用模式。【应用】：适合作为程序入口点。例如浏览器的主界面。不管从多少个应用启动浏览器，只会启动主界面一次，其余情况都会走onNewIntent，并且会清空主界面上面的其他页面。
* singleInstance：单实例模式。这是一种加强的SingleTask模式，他除了具有栈内复用模式的特性外，还加强了一点，在此种模式下只能单独的位于一个任务栈中。简单的来说，一个Activity以singleInstance的模式启动，这个任务栈就只有这一个Activity。【应用】：适合需要与程序分离开的页面。例如闹铃提醒，将闹铃提醒与闹铃设置分离。

* intent匹配规则 @see:[http://blog.csdn.net/mynameishuangshuai/article/details/51673273](http://blog.csdn.net/mynameishuangshuai/article/details/51673273)
* 一个经常被问到的问题：我想要给用户发送一个优惠信息，用户点击后可以直接打开我的APP,如何实现？@see：[http://blog.csdn.net/books1958/article/details/49948555](http://blog.csdn.net/books1958/article/details/49948555)


#### Android反编译与混淆
*  反编译 [http://blog.csdn.net/vipzjyno1/article/details/21039349/](http://blog.csdn.net/vipzjyno1/article/details/21039349/)，一个名叫androidkiller的工具进行反编译特别方便
*  混淆 [http://blog.csdn.net/lovexjyong/article/details/24652085](http://blog.csdn.net/lovexjyong/article/details/24652085)，配置一个叫proguard-project.txt的文件即可，注意有些文件不能进行混淆，需要单独列出来。


#### JNI和native方法
* native是与C++联合开发的时候用的！使用native关键字说明这个方法是原生函数，也就是这个方法是用C/C++语言实现的，并且被编译成了DLL，由java去调用。
* native方法是通过java中的JNI实现的。JNI是Java Native Interface的 缩写。从Java 1.1开始，Java Native Interface (JNI)标准成为java平台的一部分，它允许Java代码和其他语言写的代码进行交互。JNI一开始是为了本地已编译语言，尤其是C和C++而设计 的，但是它并不妨碍你使用其他语言，只要调用约定受支持就可以了。使用java与本地已编译的代码交互，通常会丧失平台可移植性。但是，有些情况下这样做是可以接受的，甚至是必须的，比如，使用一些旧的库，与硬件、操作系统进行交互，或者为了提高程序的性能。JNI标准至少保证本地代码能工作在任何Java 虚拟机实现下。
* 目前java与dll交互的技术主要有3种：jni，jawin和jacob。Jni（Java Native Interface）是sun提供的java与系统中的原生方法交互的技术（在windows\Linux系统中，实现java与native method互调）。目前只能由c/c++实现。后两个都是sourceforge上的开源项目，同时也都是基于jni技术的windows系统上的一个应用库。Jacob（Java-Com Bridge）提供了java程序调用microsoft的com对象中的方法的能力。而除了com对象外，jawin（Java/Win32 integration project）还可以win32-dll动态链接库中的方法。就功能而言：jni >> jawin>jacob	
* jni操作步骤@see：[http://www.cnblogs.com/rocomp/p/4892866.html](http://www.cnblogs.com/rocomp/p/4892866.html)
	1. 新建一个Android项目，在根目录下创建 jni文件夹，用于存放 C。
	2. 在java代码中，创建一个本地方法 getStringFromC 本地方法没有方法体。`private native String getStringFromC();`
	3. 在jni中创建一个C文件，定义一个函数实现本地方法，函数名必须用使用 本地方法的全类名，点改为下划线。`jstring Java_com_mwp_jnihelloworld_MainActivity_getStringFromC(JNIEnv* env,jobject obj){}`，内两参数固定。
	4. 在jni中创建 Android.mk文件，用于配置 本地方法。
	5. 在jni目录下执行 ndk-build.cmd指令，编译c文件。
	6. 在java代码中加载编译后生成的so类库，调用本地方法。

			static{
	        //加载打包完毕的 so类库
	        System.loadLibrary("hello");
	    	}
	

#### Android系统的有哪些安全机制
*  代码混淆
*  权限申请和动态权限申请
*  沙盒机制：进程沙箱隔离机制，使得Android应用程序在安装时被赋予独特的用户标识（UID），并永久保持。应用程序及其运行的Dalvik虚拟机运行在独立的Linux进程空间，与其它应用程序完全隔离。


#### Android新特性有哪些（5.0 6.0 7.0）
* Android5.0新特性:
	1. 代号为Lollipop(棒棒糖),采用了Google最新推行的**Material Design设计**语言,用户切换activity时,有无缝的过度位移动画.
	2. 运行机制改为**更新的ART**。相比Dalvik，ART的处理机制完全不同，它会在应用程序安装时就把程序代码转换成机器语言，让程序成为真正的本地应用。这样做的好处是程序的启动时间被极大的提高，运行速度也会更快。电量消耗的更少，系统运行也更加的流畅。
	3. Notifications通知详情可以由用户自己设计.用户在浏览某个app时,可以在当前app上浮动一个窗口来显示关键的通知信息.在锁屏界面也可以直接查看通知消息了,用户还可以直接在锁屏的界面进行消息回复或查看.
	4. 允许电视应用程序来处理诸如HDMI输入,电视调谐器,视频流,和IPTV接收机在更大的屏幕上.
	5.折叠效果的任务管理器.
	6. 支持Khronos OpenGL ES 3.，因此在游戏和app的性能上行有更好的表现 。
	7. 更强大的音频,一种新的音频捕获设计提供延迟音频输入.
	8. 增强摄像机和视频.
	9. 屏幕获取和屏幕分享.
	10. 新型传感器.
	11. 优化电量.Project Volta 电池续航改进计划,增加了Battery Saver模式,在低电量的时候系统会自动降低屏幕亮度,限制自动更换背景等功能.
	12. 新的API支持,蓝牙4.1,USB Audio,多人分享等其他特性.
	
* Android 6.0 新特性:
	1. **运行时权限**,用户可以在已安装的app上立刻同意或者拒绝权限.
	2.睡眠模式和APP备用模式,这个版本介绍了一种新的节能优化对于闲置的设备和APP.睡眠模式:当屏幕静止时,闲置一段时间,设备就会进入睡眠模式,让系统保持睡眠的状态,
	3. **去除了Apache的http Client**相关方法.Google建议用httpURLConnection来替代httpclient的相关方法,如果执意继续使用httpclient 相关方法,那么你就需要自取导入Apache的jar包.在Android Studio中的Gradle里通过userlibrary'org.Apache.http.legacy'去引用.
	4. 大量漂亮流畅的动画,Android 6.0系统增加了大量漂亮的过度动画,可以从视觉上减少卡顿感.
	5. 相机新增专业模式,一直以来,原生的Android相机都被吐槽太过简单甚至简陋了,在此次专业模式,增加了快门速度调节和曝光度调节等新功能.
	6. 全新的电源键菜单,一般来说 Android的电源键菜单都是关机/重启/飞行,Android 6.0变成了关机/重启/紧急,紧急模式是为了手机快没电的时候设计的,相当于飞行模式的高级版,可以关闭一切好点应用,尽最大可能节省电量.
	7. 可自定义锁界面样式,支持电话,信息,相机等快捷方式在锁屏界面的定制,用户可以根据自己的喜好调整这些图标的位置,或者开启或关闭这些快捷方式.
	8. 支持快速充电的切换.可以开启或关闭快速充电.
	9. 支持文件夹拖拽应用,可在应用从一个文件夹内直接拖到另一个文件夹,简化了此前繁琐的操作方式,拖拽的过程和Windows的拖拽功能有点相似.
	10. 支持raw格式照片.
	
* Android 7.0新特性：
	1. 分屏多任务支持
	2. 画中画
	3. 通知栏快速回复
	4. OpenJDK替换java API
	5. Android7.0采用了一项具有实时代码剖析功能的ARI JIT编译器,它能够在安卓应用程序在运行时不断提高自身的性能
	6. 7.0开发适配@see：[http://blog.csdn.net/Chay_Chan/article/details/57083383](http://blog.csdn.net/Chay_Chan/article/details/57083383)
	
* @see:[http://blog.csdn.net/monazhou/article/details/54092352](http://blog.csdn.net/monazhou/article/details/54092352)

#### Dalvik和ART区别
* 什么是Dalvik？
	* Dalvik是Google公司自己设计用于Android平台的虚拟机。Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一。
	* 它可以支持已转换为 .dex格式的Java应用程序的运行，.dex格式是专为Dalvik设计的一种压缩格式，适合内存和处理器速度有限的系统。
	* Dalvik 经过优化，允许在有限的内存中同时运行多个虚拟机的实例，并且每一个Dalvik 应用作为一个独立的Linux 进程执行。独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。
	* 很长时间以来，Dalvik虚拟机一直被用户指责为拖慢安卓系统运行速度不如IOS的根源。2014年6月25日，Android L 正式亮相于召开的谷歌I/O大会，Android L 改动幅度较大，谷歌将直接删除Dalvik，代替它的是传闻已久的ART。

* Dalvik和JVM有啥关系？
	* Dalvik是基于寄存器的，而JVM是基于栈的。
	* Dalvik运行dex文件，而JVM运行java字节码
	* 自Android 2.2开始，Dalvik支持JIT（just-in-time，即时编译技术）。优化后的Dalvik较其他标准虚拟机存在一些不同特性:　
	1. 占用更少空间　
	1. 为简化翻译，常量池只使用32位索引　　
	1. 标准Java字节码实行8位堆栈指令,Dalvik使用16位指令集直接作用于局部变量。局部变量通常来自4位的“虚拟寄存器”区。这样减少了Dalvik的指令计数，提高了翻译速度。当Android启动时，Dalvik VM 监视所有的程序（APK），并且创建依存关系树，为每个程序优化代码并存储在Dalvik缓存中。Dalvik第一次加载后会生成Cache文件，以提供下次快速加载，所以第一次会很慢。Dalvik解释器采用预先算好的Goto地址，每个指令对内存的访问都在64字节边界上对齐。这样可以节省一个指令后进行查表的时间。为了强化功能, Dalvik还提供了快速翻译器（Fast Interpreter）。
	
* 什么是ART？
即Android Runtime。
ART 的机制与 Dalvik 不同。在Dalvik下，应用每次运行的时候，字节码都需要通过即时编译器（just in time ，JIT）转换为机器码，这会拖慢应用的运行效率，而在ART 环境中，应用在第一次安装的时候，字节码就会预先编译成机器码，使其成为真正的本地应用。这个过程叫做预编译（AOT,Ahead-Of-Time）。这样的话，应用的启动(首次)和执行都会变得更加快速。
	
* ART有什么优缺点呢？
	* 优点：
	1. 系统性能的显著提升。
	2. 应用启动更快、运行更快、体验更流畅、触感反馈更及时。
	3. 更长的电池续航能力。
	4. 支持更低的硬件。
	* 缺点：
	1. 机器码占用的存储空间更大，字节码变为机器码之后，可能会增加10%-20%（不过在应用包中，可执行的代码常常只是一部分。比如最新的 Google+ APK 是 28.3 MB，但是代码只有 6.9 MB。）
	2. 应用的安装时间会变长。
	
* tips：现在智能手机大部分都可以让用户选择使用Dalvik还是ART模式。当然默认还是使用Dalvik模式。用法：设置-辅助功能-开发者选项（开发人员工具）-选择运行环境（不同的手机设置的步骤可能不一样）。

#### Android屏幕适配有哪些方法
1. 【常用】图片适配:根据手机尺寸的不同，加载不同尺寸文件夹中的图片（图标）
2. dimens.xml文件适配，可用于指定控件大小，不同像素密度手机加载不同values文件夹下的dimens.xml文件。
3. 布局文件适配，不同分辨率的手机，加载不同的布局文件以达到适配效果。创建多个layout(如：layout-1280x720、layout-800x480)文件夹用于存放不同像素密度手机所需布局文件。
4. java代码适配，通过android相应api获取当前手机的宽高像素值，按比例分配屏幕中控件的宽高以达到适配效果。
5. 【常用】权重适配，通过android中线性布局中的属性(layout_weight权重) 按比例来分配，已达到适配效果。
6. 【常用】用dp替代px(普通控件尺寸大小);字体采用sp进行尺寸标度。
			//ppi:像素密度、px:像素
			px = dp*ppi/160
			dp = px / (ppi / 160)
			px = sp*ppi/160
			sp = px / (ppi / 160)
			dp = sp? 
7. 【重要】根据google出的百分比支持包进行按比例适配。（see:Android 百分比布局库(percent-support-lib) 解析与扩展,[http://blog.csdn.net/lmj623565791/article/details/46695347](http://blog.csdn.net/lmj623565791/article/details/46695347)）
	

