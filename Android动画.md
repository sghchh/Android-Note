#Android动画  
Android基本的动画可以分为三种：View动画，帧动画，属性动画。View动画通过对场景里的对象不断做图象变换（平移，伸缩，旋转，透明度）从而产生动画效果；而帧动画时间上也是View动画的一种，通过顺序播放一系列的图像从而产生动画效果，如果图像过大，容易产生OOM；属性动画通过动态地改变对象的属性从而达到动画的效果。  
##1. View动画（Animation）  
顾名思义**View动画的作用对象时View**，而且，就行它的名字那样，View动画还可以像View一样，通过**在xml文件中定义**来使用它，以及**能够自定义**，这是**属性动画所不具有的。**系统自带的View动画有四种，TranslateAnimation，ScaleAnimation，RotateAnimation，AlphaAnimation。  
###1.1 View动画的静态使用  
通常推荐静态使用View动画，因为这样不仅方便，而且还能增强可读性。  
首先要在res路径下新建一个directory名叫anim，**anim目录用来存放View动画的xml文件**  
新建一个View的xml文件，这个xml文件的根元素是**<set/>,这个根元素对应于AnimationSet类，代表若干个View动画的集合，也就是说，一个view可以加载不止一个动画。**  

	<?xml version="1.0" encoding="utf-8"?>
	<set xmlns:android="http://schemas.android.com/apk/res/android"
    	android:duration="500"
    	android:shareInterpolator="true">
    <rotate
        android:fromDegrees="0"
        android:toDegrees="360"
        android:pivotY="0"
        android:pivotX="0"
        />

    <alpha
        android:fromAlpha="0.2"
        android:toAlpha="0.9"/>

    <scale
        android:fromXScale="0.5"
        android:toXScale="1"
        android:fromYScale="0.5"
        android:toYScale="1"
        android:pivotX="0.5"
        android:pivotY="0.5"/>

    <translate
        android:fromXDelta="0.6"
        android:toXDelta="1"
        android:fromYDelta="0.3"
        android:toYDelta="1"/>

	</set>  
看上面的实例，不同的标签分别对应了四个Animation类，这里就几个属性值来进行讲解：  

* **shareInterpolator：**表示集合的动画是否和集合共享同一个插值器，如果**集合（注意：这里说的是集合，也就是set根元素，所以从这句话以及实践表明，如果set的属性中设置了interpolator属性，那么该值就会覆盖子动画中的interpolator属性；这个现象也适用于duration属性）**不指定插值器，那么子动画就要单独指定所需的插值器或者默认值。
* **android:fillAfter:**设置为true表示动画结束后，该控件保持结束时的状态，false则表示不保持。比如：一个平移动画使一个控件平移了30dp，如果这个fillAfter设置为了true，那么动画结束后，控件将会在平移了30dp的位置。  
* **translate**  
	* **frome/toXDelta:**这个值并不是一个比值，而是相对于屏幕而言的，也就是说，加入设置这个值为0.5，效果是真的从或者到距离屏幕左侧0.5dp的位置，而不是0.5*控件的宽的位置。同样Y方向上的也是。
* **rotate**  
	* **from/toDegree：**直接填度数就行了，并不用换算成弧度制  
* **scale**  
	* **from/toXScale:**这个值是比例，即如果填0.5，那么该方向上的缩放值就是原控件的宽的0.5倍。Y方向上也是。  
到这里，我们的View动画的xml文件已经定义完了，那么如何去使用他呢：  

	Animation animation=AnimationUtil.loadAnimation(Context,R.anim.XXX);
	view.startAnimation(animation);  
好了，至此，属性动画的静态使用已经完了。  
###1.2 View动画的动态使用  
动态使用无非是使用对应的Animation类实现动画。  

	AlphaAnimation alpha=new AlphaAnimation(0,1);  
	alpha.setDuration(3000);
	view.startAnimation(alpha);  
动态使用View是一个略微麻烦的事。  
###1.3 为View动画设置监听器  
可以为View动画设置监听器，方法是Animation.setAnimationListener(AnimationListener);
通过接口可以看见每一个方法的含义：  

	public static interface AnimationListener{
		void onAnimationStart(Animation animation);  
		void onAnimationEnd(Animation animation);  
		void onAnimationRepeat(Animation animation);  
	}  
###1.4 自定义View动画  
自定义view动画只需要继承Animation类，并且重写其中的initialize和applyTransform方法就可以了。  
##2. 帧动画  
帧动画是特殊的View动画，但是，Android提供了一个专门的类来给用户啊使用帧动画：AnimationDrawable，既然是一个drawable，所以帧动画的xml定义也是在drawable下的，并不是在anim下的。  

	<?xml version="1.0" encoding="utf-8"?>
	<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/ic" android:duration="500"/>
    <item android:drawable="@drawable/out" android:duration="1000"/>

	</animation-list>  
需要注意的是：我们创建一个drawable的xml文件是时候，刚开始的时候的根元素是<selector/>所以我们要把这个根元素改成<animation-list/>；其次，item元素有两个子元素，一个是图片，另一个是该图片显示的时间。  
最后我们通过代码使用：  

	AnimationDrawable drawable=(AnimationDrawable)view.getBackground();
        drawable.start();  
##3. View动画的特殊应用场景  
####3.1 LayoutAnimation：为ViewGroup的child添加入场动画  
LayoutAnimation作用于ViewGroup，为ViewGroup指定一个动画，这样他的子元素出场时就会具有这种动画效果，**多用于ListView为item添加进场动画**  

1. 定义一个LayoutAnimation的xml文件，这个文件当然是位于anim路径下，而且**根元素换成LayoutAnimation**  
	* android:delay:设置子元素的进场延迟，**这个值是一个小于1.0的值**，比如，子元素的进场时间的周期是300毫秒，delay的值是0.5，表示第一个元素延迟150毫秒开始播放入场动画，第二个元素延迟2*150毫秒播放，以此类推。
	* android:animationOrder:表示子元素进场的顺序，可选值有：  
		* normal：表示顺序显示
		* reverse：表示逆向显示
		* random：表示随机显示
	* android:animation：指定一个进场动画资源  
可能有同学要想了，如果我要为listview指定一个自定义的Animation呢，通过xml总不能实现了吧。这个问题可以通过LayoutAnimationController来解决：  

			LayoutAnimationController controller=new LayoutAnimationController(animation);
			controller.setDelay(0.5f);
			controller.setOrder(LayoutAnimationController.ORDER_NORMAL);  
			listview.setLayoutAnimation(controller);  
这样一来，我们就可以为ViewGroup指定一个自定义的Animation了。
2. 在ViewGroup的xml属性中添加一个android:layoutAnimation属性指定一个LayoutAnimation资源  
####3.2 Activity切换的动画效果  
想要设置Activity的切换效果，只需要调用overridePendingTransition（int enterAnim，int exitAnim）这个方法就可以了。**该方法必须在startActivity（）或者finish（）方法之后调用才行**  
* int enterAnim：Activity被打开的动画  
* int exitAnim：Activity暂停时的动画  
##4. 强大的属性动画：Animator  
属性动画不同于View动画只能作用于View，能够作用于任何对象，而且**属性动画的类是Animator系列**的ValueAnimator，ObjectAnimator，AnimatorSet等，其中ObjectAnimator继承自ValueAnimator，与View的动画的Animation容易混淆。  
*前面说过，属性动画是通过改变对象的属性来产生的动画的效果的，所以属性动画几乎是无所不能的*。属性动画的使用与View动画的使用大不相同。  

	ObjectAnimator objectanimator=ObjectAnimator.ofFloat(Object,"propertyName",float);  
	objectanimator.start();  
刚一看到这段代码，我们可能会感到有点蒙，细看就可以明白了。首先，属性动画的开始播放动画同View动画一样，都是调用了start（）方法实现的；获取属性动画对象的是通过属性动画的**静态方法XXXAnimator.ofXXX(Object,"propertyName",float)**获得的。这个方法的第一个参数代表了属性动画执行到哪一个对象上，第二个表示了要改变的属性的名称，第三个参数代表了怎么改变。  

	ObjectAnimator.ofFloat(view,"translationY",view.getHeight()/2).start();  
这个动画的效果就是该view向下平移了控件高的一半的距离。  
###4.1 AnimatorSet  
AnimatorSet类也是可以将多个动画效果加到一个对象上，它有一个playTogether（Animator ...)或者playSequentially（Animator...）方法来设置，到底是所有的动画一起执行还是按顺序执行（**只是设置动画的播放形式，真正播放还是得调用start方法**）。  

	AnimatorSet set=new AnimatorSet();
        set.playSequentially(ObjectAnimator.ofFloat(view,"translationY",view.getHeight()/2),ObjectAnimator.ofFloat(view,"translationY",-view.getHeight()/2));
        set.setDuration(2000).start();  
这个动画的最终效果是，向上平移了高度的一半。**可见，即使是AnimatorSet将多个动画加到一个对象上，各个动画的实现都是对原始的对象进行操作的，而不是上一个动画进行过后的对象。从传入的形参也可以看出，每一个ObjectAnimator都是通过ofFloat方法获得的，第二个ObjectAnimator传入也是对原始的view执行的ofFloat方法，而不是对第一个ObjectAnimator的ofFloat方法操作后的view。**

	animator.setStartDelay();//通过该方法来设置开始播放的延迟  
	animator.setRepeatCount();//通过该方法来设置动画播放重复的次数，该方法AnimatorSet没有  
	animator.setRepeatMode();//通过该方法来设置动画重复播放时的模式，常用有NORMAL表示顺序，REVERSE表示逆序播放,该方法AnimatorSet没有   
属性动画虽然也可以通过xml来静态定义和使用，但是不推荐这么做。因为属性动画应用的场景比较灵活，通常对一个属性值的改变不是事先就知道了的，比如设置平移的时候，平移控件的自身宽或者高的一半，光是这一点就没有方法在定义xml时指定。所以属性动画一般都是Java代码使用的。  
**属性动画也可以设置监听器**  
###4.2 属性动画原理  
要想实现属性动画的效果，那个propertyName不能随便传，即使对象有这个属性，他需要满足以下两个条件才会有动画效果：  

* 对象必须提供该属性的set和get方法，其中get方法是当动画没有传递初始值的时候，通过该方法去获取初始值（如果没满足这条条件，直接crash）  
* 对象的set方法对属性值的改变必须能够通过某种方式被反映出来，比如会带来UI的改变之类的。（如果这条条件没有满足，动画没效果但不会crash）。  

下面我们想一个场景，我想要通过属性动画改一个Button的width值来达到放大的效果，虽然Button有width这个属性，并且有setWidth这个方法，但是因为这个方法设置的不是控件的width而是最大width，这该怎么办呢。官方文档告诉我们有以下三个方法：  
* 为对象加上get和set方法，如果有权限的话  
* 用一个类来包装该对象，简介为其提供get和set方法：比如我用A类来包装Button，set方法内部通过button.getLayoutParams().width=newwidth;button.requestLayout();方法来完成width的改变  
* 采用ValueAnimator，监听动画过程，自己实现属性的改变   
