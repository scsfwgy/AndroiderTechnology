##Android View相关
*  自定义view的过程【重点，待研究】

	1. 自定义View的属性
	2. 在View的构造方法中获得我们自定义的属性
	3. [重写onMesure ]
	4. layout过程：layout过程其实就是对子View的位置进行排列，onLayout方法给我一个机会，来按照我们想要的规则自定义子View排列。
	5.重写onDraw

@see:[http://www.jianshu.com/p/138b98095778](http://www.jianshu.com/p/138b98095778)

#### View和ViewGroup的事件分发和处理的总流程
* 很厉害的总结：@see:[http://www.jianshu.com/p/e99b5e8bd67b](http://www.jianshu.com/p/e99b5e8bd67b)
* ![](http://upload-images.jianshu.io/upload_images/966283-b9cb65aceea9219b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 某个控件的dispatchTouchEvent 返回true消费终结事件，那么收到ACTION_DOWN 的函数也能收到 ACTION_MOVE和ACTION_UP。
* onTouchEvent消费事件的情况：在哪个View的onTouchEvent 返回true，那么ACTION_MOVE和ACTION_UP的事件从上往下传到这个View后就不再往下传递了，而直接传给自己的onTouchEvent 并结束本次事件传递过程。
* 对于ACTION_MOVE、ACTION_UP总结（下面几句话不太明白，不都是只要消费了，就在该处停止吗？求解答）：
	* ACTION_DOWN事件在哪个控件消费了（return true）， 那么ACTION_MOVE和ACTION_UP就会从上往下（通过dispatchTouchEvent）做事件分发往下传，就只会传到这个控件，不会继续往下传。
	* 如果ACTION_DOWN事件是在dispatchTouchEvent消费，那么事件到此为止停止传递。
	* 如果ACTION_DOWN事件是在onTouchEvent消费的，那么会把ACTION_MOVE或ACTION_UP事件传给该控件的onTouchEvent处理并结束传递。


#### View绘制原理，性能问题（view绘制间隔16ms，60fps）,measure，layout，draw三大过程
* @see:[http://www.tuicool.com/articles/63yiMbY](http://www.tuicool.com/articles/63yiMbY)
	
* View 绘制流程
我们都知道，View 的绘制分为三个阶段：测量、布局和绘制，这三个阶段各自的作用如下：
	* measure: 为整个 View 树计算实际的大小，即设置实际的高（对应属性：mMeasureHeight）和宽（对应属性：mMeasureWidth），每个 View 的控件的实际宽高都是由父视图和本身视图所决定的。
	* layout：为将整个根据子视图的大小以及布局参数将 View 树放到合适的位置上。
	* draw：利用前两部得到的参数，将视图显示在屏幕上。
			

* 优化
		
* 从内优化
减少 View 层级：这样会加快 View 的循环遍历过程。去除不必要的背景。由于 在 draw 的步骤中，会单独绘制背景。因此去除不必要的背景会加快 View 的绘制。
尽可能少的使用 margin、padding：在测量和布局的过程中，对有 margin 和 padding 的 View 会进行单独的处理步骤，这样会花费时间。我们可以在父 View 中设置 margin 和 padding，从而避免在子 View 中每个单独设置和配置。
去除不必要的 scrollbar：这样能减少 draw 的流程。
慎用渐变：能减少 draw 的流程。
			
从外优化
布局嵌套过于复杂。这会直接 View 的层级变多。View 的过渡绘制。
View 的频繁重新渲染。UI 线程中进行耗时操作。在 Android 4.0 之后，不允许在 UI 线程做网络操作。冗余资源及错误逻辑导致加载和执行缓慢。简单的说，就是代码写的烂。频繁触发 GC，导致渲染受阻。当系统在短时间内有大量对象销毁，会造成内存抖动，频繁触发 GC 线程，而 GC 线程的优先级高于 UI 线程，因而会造成渲染受阻。

#### 如何避免overdraw

* @see:[http://blog.csdn.net/xiaoluoli88/article/details/50534779](http://blog.csdn.net/xiaoluoli88/article/details/50534779)

* Overdraw就是过度绘制，是指在一帧的时间内（16.67ms）像素被绘制了多次，理论上一个像素每次只绘制一次是最优的，但是由于重叠的布局导致一些像素会被多次绘制，而每次绘制都会对应到CPU的一组绘图命令和GPU的一些操作，当这个操作耗时超过16.67ms时，就会出现掉帧现象，也就是我们所说的卡顿，所以对重叠不可见元素的重复绘制会产生额外的开销，需要尽量减少Overdraw的发生。
* Android提供了测量Overdraw的选项，在开发者选项－调试GPU过度绘制（Show GPU Overdraw），打开选项就可以看到当前页面Overdraw的状态，就可以观察屏幕的绘制状态。
	
* 如何避免
第一招：合理选择控件容器。
第二招：去掉window的默认背景。
第三招：去掉其他不必要的背景（容器的布局和子控件的布局背景使用一个即可）。
第七招：善用draw9patch。
第八招：慎用Alpha
第九招：避免“OverDesign”。（产品设计层面）
第五招：ViewStub、Merge

#### 自定义View相关知识，下拉刷新的实现

* 自定义下拉、上拉思路
![](http://img.blog.csdn.net/20131012144804468?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVlaG9uZzIwMDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
	
* 关于上图，需要说明几点：
	1. 这个布局扩展于LinearLayout，垂直排列
	2. 从上到下的顺序是：Header, Content, Footer
	3. Content填充满父控件，通过设置top, bottom的padding来使Header和Footer不可见，也就是让它超出屏幕外
	4. 下拉时，调用scrollTo方法来将整个布局向下滑动，从而把Header显示出来，上拉正好与下拉相反。
	5. 派生类需要实现的是：将Content View填充到父容器中，比如，如果你要使用的话，那么你需要把ListView, ScrollView, WebView等添加到容器中。
	6. 上图中的红色区域就是屏的大小（严格来说，这里说屏幕大小并不准确，应该说成内容区域更加准确）


#### Android动画分类和各自特点

* Frame Animation(帧动画)主要用于播放一帧帧准备好的图片，类似GIF图片，优点是使用简单方便、缺点是需要事先准备好每一帧图片；
* Tween Animation(补间动画)仅需定义开始与结束的关键帧，而变化的中间帧由系统补上，优点是不用准备每一帧，缺点是只改变了对象绘制，而没有改变View本身属性。因此如果改变了按钮的位置，还是需要点击原来按钮所在位置才有效。包括四种子类：AlphaAnimation(透明度动画)、ScaleAnimation(缩放动画)、TranslateAnimation(位移动画)、RotateAnimation(透明度动画)
* Property Animation(属性动画)是3.0后推出的动画，优点是使用简单、降低实现的复杂度、直接更改对象的属性、几乎可适用于任何对象而仅非View类，缺点是需要3.0以上的API支持，限制较大！但是目前国外有个开源库，可以提供低版本支持！

#### View和SurfaceView的区别
* SurfaceView和View最本质的区别在于，surfaceView是在一个新起的单独线程中可以重新绘制画面而View必须在UI的主线程中更新画面。 
* 那么在UI的主线程中更新画面 可能会引发问题，比如你更新画面的时间过长，那么你的主UI线程会被你正在画的函数阻塞。那么将无法响应按键，触屏等消息。 
* 当使用surfaceView 由于是在新的线程中更新画面所以不会阻塞你的UI主线程。但这也带来了另外一个问题，就是事件同步。比如你触屏了一下，你需要surfaceView中thread处理，一般就需要有一个event queue的设计来保存touch event，这会稍稍复杂一点，因为涉及到线程同步。 
