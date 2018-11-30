# Android自定义View--自定义属性  
## 1. 不需要<declare-styleable>标签  
由于我都是在**declare-styleable**下添加的自定义属性，所以以为只有这一种方法，今天正好学学另一种方法：  

1. 新建一个xml文件，随意命名  
2. **根元素是resources**  
3. 跳过<declare-styleable>标签，直接在根元素下开始添加自定义属性  
  
### 在自定义View中加载自定义属性  
先来看一看一个例子：  

	<resources>
	    <attr name="customText" format="string" />
	    <attr name="customColor" format="color" />
	</resources>  

我们定义了两个XML属性，customText是一个string类型，表示MyView要显示的文本，customColor是color类型，表示文本的颜色。

对项目进行编译之后会生成R.java文件，R.java文件对应着R类。如果是Android Studio项目，那么R文件的目录是**app\build\generated\source\r\debug\com\ispring\attr\R.java**，在该文件中有内部类public static final class attr，在**R.attr**中会发现有**customText和customColor**

> 新版的Android Studio中R文件的位置是：**app\build\generated\source\not_namespaced_r_class_sources\debug\processDebugResources\r\包名\R.java**

**R.attr.customText和R.attr.customColor**分别是属性customText和customColor的资源ID。  
**加载自定义属性：**  

	private void init(AttributeSet attrs, int defStyle) {
        //首先判断attrs是否为null
        if(attrs != null){
            //获取AttributeSet中所有的XML属性的数量
            int count = attrs.getAttributeCount();
            //遍历AttributeSet中的XML属性
            for(int i = 0; i < count; i++){
                //获取attr的资源ID
                int attrResId = attrs.getAttributeNameResource(i);
                switch (attrResId){
                    case R.attr.customText:
                        //customText属性
                        mCustomText = attrs.getAttributeValue(i);
                        break;
                    case R.attr.customColor:
                        //customColor属性
                        //如果读取不到对应的颜色值，那么就用黑色作为默认颜色
                        mCustomColor = attrs.getAttributeIntValue(i, 0xFF000000);
                        break;
                }
            }
        }

        //初始化画笔
        mTextPaint = new TextPaint();
        mTextPaint.setFlags(Paint.ANTI_ALIAS_FLAG);
        mTextPaint.setTextSize(fontSize);
    }  
在没有<declare-styleable>标签的情况下是这样加载属性的。  

>这种情况下的属性是可以**共用的**，如果有多个自定义的View不走<declare-styleable>标签，这个资源文件中的属性是可以**互用的**  

## 2. 使用<declare-styleable>标签  
我们上面定义的customText和customColor这两个<attr>属性都是直接在<resources>节点下定义的，**这样定义<attr>属性存在一个问题：不能想通过style或theme设置这两个属性的值。**    
要想能够通过style或theme设置XML属性的值，需要在<resources>节点下**添加<declare-styleable>节点**，并在<declare-styleable>节点下定义<attr>，如下所示：  

	<resources>
    <declare-styleable name="MyView">
        <attr name="customText" format="string" />
        <attr name="customColor" format="color" />
    </declare-styleable>
	</resources>  

在<declare-styleable>下面定义的<attr>属性与直接在<resources>定义的<attr>属性其实本质上没有太大区别，无论哪种方式定义<attr>，都会在R.attr类中定义R.attr.customText和R.attr.customColor。不同的是，**<declare-styleable name="MyView">节点会在R.styleable这个内部类中有如下定义： **  

![](http://img.blog.csdn.net/20160222222816704)  

**R.styleable.MyView是一个int数组，其值为0x7f010038和 0x7f010039。0x7f010038就是属性R.attr.customText，0x7f010039就是属性R.attr.customColor。也就是R.styleable.MyView等价于数组[R.attr.customText, R.attr.customColor]。**  

>最近在写自定义View的时候，调用**TypedArray array=context.obtainStyledAttributes(attributeSet,R.styleable.DuiGouView);**的时候，发现第二个参数是一个**int[]数组**，当时有点蒙，现在恍然大悟。  

我们同样可以在R.styleable中发现R.styleable.MyView_customColor和R.styleable.MyView_customText这两个ID。**<declare-styleable name="MyView">中的name加上里面的<attr>属性的name就组成了R.styleable中的MyView_customColor和MyView_customText，中间以下划线连接**。  

* 如果在layout文件中直接为MyView设置了某些XML属性，那么这些XML属性及其属性值就会出现在AttributeSet中，那么Android就会直接使用AttributeSet中该XML属性值作为theme.obtainStyledAttributes()的返回值，比如在上面的例子中，我们通过app:customText="customText in AttributeSet"设置了MyView的XML属性，最终运行的效果显示的也是文本”customText in AttributeSet”。  
* 如果在layout文件中没有为MyView设置某个XML属性，但是给MyView设置了style属性，例如**style="@style/RedStyle"**，并且**在style中指定了相应的XML属性**，那么Android就会用style属性所对应的style资源中的XML属性值作为**theme.obtainStyledAttributes()**的返回值。比如在上面的例子中，我们在layout文件中没有设置app:customColor的值，但是在其style属性所对应的RedStyle资源中将customColor设置成了红色#FFFF0000，最终文本也是以红色显示在界面上的。  

## 3. obtainStyledAttributes方法之defStyleAttr  
我们再看一下方法**obtainStyledAttributes (AttributeSet set, int[] attrs, int defStyleAttr, int defStyleRes)**中的第三个参数defStyleAttr，这个参数表示的是一个**style**中某个属性的ID，**当Android在AttributeSet和style属性所定义的style资源中都没有找到XML属性值时，就会尝试查找当前theme（theme其实就是一个style资源）中属性为defStyleAttr的值，如果其值是一个style资源，那么Android就会去该资源中再去查找XML属性值**。可能听起来比较费劲，我们举个例子。  

我们更改了**values/styles.xml**文件，如下所示：
	<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="myViewStyle">@style/GreenStyle</item>
    </style>

    <style name="GreenStyle">
        <item name="customText">customText in GreenStyle</item>
        <item name="customColor">#FF00FF00</item>
    </style>

    <style name="HelloWorldStyle">
        <item name="customText">Hello World!</item>
    </style>

    <style name="RedStyle">
        <item name="customText">customText in RedStyle</item>
        <item name="customColor">#FFFF0000</item>
    </style>

	</resources>  

我们添加了HelloWorldStyle和**GreenStyle**，其中HelloWorldStyle只定义了customText的属性值，而**GreenStyle**同时定义了**customText和customColor**的值，其中**customColor**定义为绿色。

在**AppTheme**中，我们设置了**myViewStyle**这个属性的值，如下所示：  

	<item name="myViewStyle">@style/GreenStyle</item>  

myViewStyle这个属性是在**values/attrs_my_view.xml**中定义的，如下所示:  

	<resources>
    <attr name="myViewStyle" format="reference" />
    <declare-styleable name="MyView">
        <attr name="customText" format="string" />
        <attr name="customColor" format="color" />
    </declare-styleable>
	</resources>  

**myViewStyle被定义为一个reference格式**，即其值指向一个资源类型，我们在AppTheme中将其赋值为**@style/GreenStyle**，即在AppTheme中，myViewStyle的就是GreenStyle，其指向了一个style资源。

>注意：AppTheme中的资源引用是**style资源下的，style资源县的子元素是item不再是attr了**；而item元素的**name属性须得和一个attr元素的name属性一样。**
>上面说过了**AppTheme实际上就是一个style资源**，**所以这个过程是AppTheme的一个item的name与一个attr的name匹配，后者类型是一个引用，最后调用的时候这个引用指向一个style资源文件，在这个资源文件中为我们自定义View的属性赋了值。**  
>**实际上就是拐了一个弯地实现了通过自定义view的style属性统一设置属性，只不过这个style是AppTheme处设置的**

我们更改MyView代码，如下所示：  

	//通过theme的obtainStyledAttributes方法获取TypedArray对象
            TypedArray typedArray = theme.obtainStyledAttributes(attributeSet, R.styleable.MyView, R.attr.myViewStyle, 0);  

注意，上面obtainStyledAttributes方法最后一个参数还是为0，可以忽略，但是第三个参数的值不再是0，而是**R.attr.myViewStyle**。

然后我们更新layout文件，如下所示：  

	<com.ispring.attr.MyView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        style="@style/HelloWorldStyle"
        />  
我们发现结果是绿色的“Hello World!”,我们解释一下原因:  

* 由于这次我们没有通过layout文件直接设置MyView的XML属性的值，所以AttributeSet本身是没有XML属性值的，我们直接忽略掉AttributeSet  
* 我们通过**style="@style/HelloWorldStyle"**为MyView设置了style为HelloWorldStyle，HelloWorldStyle中定义customText的属性值为”Hello World!”，所以最终customText的值就是”Hello World!”，在界面上显示的也是该值。
* HelloWorldStyle中并没有定义customColor属性值。我们将**theme.obtainStyledAttributes()方法**的第三个参数设置为R.attr.myViewStyle，**此处的theme就是我们上面提到的AppTheme，Android会去AppTheme中查找属性为myViewStyle的值**，我们之前提到了，它的值就是**@style/GreenStyle**，即GreenStyle，由于该值是个style资源，Android就会去该资源中查找customColor的值，GreenStyle定义了customColor的颜色为绿色，所以MyView最终所使用的customColor的值就是绿色。

**综上，我们发现，此处的第三个参数的作用是：当在AttributeSet和style属性中都没有找到属性值时，就去Theme的某个属性（即第三个参数）中查看其值是否是style资源，如果是style资源再去这个style资源中查找XML属性值作为替补值。**