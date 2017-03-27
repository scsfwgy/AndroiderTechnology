##Android基础
###目录
* 五种布局的特点和效率对比
* Activity的生命周期和缓存
* 切换activity执行顺序
* Fragment的生命周期
* Service的两种启动方式，如何保证一个Service不被杀死
* 广播注册的两种方式的区别
* 如何安全的退出一个已经开启多个activity的APP
* Android的进程优先级
* 进程的保活
* Asset目录与res目录的区别
* ANR问题，force close问题，activity，service，broadcast各自超过多少秒报ANR
* Activity启动模式，intent匹配规则
* Android反编译与混淆
* JNI和native方法
* Android系统的有哪些安全机制
* Dalvik和ART区别
* Android屏幕适配有哪些方法

###详解
####五种布局的特点和效率对比
* LinearLayout：常用，线性布局，简单布局可以使用，可以使用权重进行百分比布局。特别注意，在复杂布局中使用可能会出现“过度绘制”问题。
* RelativeLayout：常用，相对布局，适用于复杂的布局。
* FrameLayout：较常用，帧布局，所有东西依次都放在左上角，会重叠。在开发中常做容器使用，用于动态添加布局对象。
* TableLayout：较常用，表布局，类似于表格。
* AbsoluteLayout：不常用，绝对布局，**屏幕适配麻烦**，容易出问题，在实际开发中不常用。

####Activity的生命周期和缓存
![](http://hi.csdn.net/attachment/201109/1/0_1314838777He6C.gif)
	
* 有a、b两个activity，当a进入b之后的一段时间，可能系统就把a回收了，这时候按back键，执行的不是a的onStart()方法，而是onCreate()方法，a被重新创建了一次，这时a的临时数据和状态就很有可能就丢失了。而Activity中的onSaveInstanceState()回调方法会保存临时的数据和状态，这个方法一定会再activity回收之前调用。方法中有一个Bundle参数，putString()、putInt()等方法需要传入两个参数，一个键一个值。数据保存之后会在 onCreate中会恢复，onCreate也有一个Bundle类型的参数。onRestoreInstanceState(Bundle outState)不一定会执行，建议在onCreate(Bundle outState)中做恢复处理。
* 关键字：onSaveInstanceState(Bundle outState)、onCreate(Bundle savedInstanceState)、onRestoreInstanceState(Bundle outState)。
* @see:[http://blog.csdn.net/lccly/article/details/6089667](http://blog.csdn.net/lccly/article/details/6089667)

####切换activity执行顺序
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
* Fragment与对应activity的生命周期的对比
![](http://img.my.csdn.net/uploads/201211/29/1354170682_3824.png)
* Fragment与对应activity的执行流程对比
![](http://img.blog.csdn.net/20150310094648441)

#### Service的两种启动方式，如何保证一个Service不被杀死
* 两种方式：Context.startService()、Context.bindService(intent, conn, BIND_AUTO_CREATE)。
* 如何保证一个Service不被杀死：
	* onStartCommand方法，返回START_STICKY，手动返回START_STICKY，亲测当service因内存不足被kill，当内存又有的时候，service又被重新创建，比较不错，但是不能保证任何情况下都被重建，比如进程被干掉了....
	* 提升service优先级，android:priority = "1000"（可能无效）
	* 提升service进程优先级， startForeground(0x111, notification)。用户体验不好，通知栏会看到。如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart
	* onDestroy方法里重启service，当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证~.~
	* Application加上Persistent属性，据说这个属性不能乱设置，不过设置后，的确发现优先级提高不少，或许是相当于系统级的进程，但是还是无法保证存活
	* @see:[http://blog.csdn.net/mad1989/article/details/22492519](http://blog.csdn.net/mad1989/article/details/22492519)


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
	
在AndroidManifest中进行注册后，不管改应用程序是否处于活动状态，都会进行监听，比如某个程序时监听 内存的使用情况的，当在手机上安装好后，不管改应用程序是处于什么状态，都会执行改监听方法中的内容。
在代码中进行注册后，当应用程序关闭后，就不再进行监听。我们都知道，应用程序是否省电，决定了该应用程序的受欢迎程度，所以，对于那些没必要在程序关闭后仍然进行监听的Receiver，在代码中进行注册，无疑是一个明智的选择。

#### 如何安全的退出一个已经开启多个activity的APP
#### Android的进程优先级
#### 进程的保活
#### Asset目录与res目录的区别
#### ANR问题，force close问题，activity，service，broadcast各自超过多少秒报ANR
#### Activity启动模式，intent匹配规则
#### Android反编译与混淆
#### JNI和native方法
#### Android系统的有哪些安全机制
#### Dalvik和ART区别
#### Android屏幕适配有哪些方法




