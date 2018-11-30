#Android自定义View详解  
##1. 认识ViewRoot和DecorView  
ViewRoot对应于ViewRootImpl类，**是链接WindowManager和DecorView的纽带，View的三大流程均是通过ViewRoot来完成的**。在ActivityThread中，当Activity对象被创建完毕后，会将DecorView添加到Window中，同时会创建ViewRootImpl对象，并且将ViewRootImpl对象和DecorView建立关联。值得注意的是，ViewRoot并不是一个View。**View的绘制流程就是从ViewRootImpl的performTraversals()方法开始的**  

* ViewRoot，Window，DecorView三者的关系  
![](http://pic002.cnblogs.com/images/2012/328668/2012041014441235.jpg)  
* View的绘制流程  
![](http://ojgs96t5i.bkt.clouddn.com/IMG_20170111_212444_EDIT_1.jpg)  
![](http://img.blog.csdn.net/20150706171750186)  
在performMeasure方法中会调用ViewGroup的measure方法，在measure方法中又会调用onMeasure方法，在onMeasure方法中会对所有的子元素进行measure过程，这样测量过程就从父容器传到了子控件。而后子View就开始调用自己的measure方法，然后是onMeasure方法，所以，我们自定义View的时候重写的就是onMeasure方法。  
##2. 理解MeasureSpec  
MeasureSpec代表了View的测量规则，由父容器和子View的LayoutParams共同决定；  
MeasureSpec 是一个32 位的int值，高2位代表 SpecModel,低30位代表 SpecSize。 SpecModel 指测量模式，SpecSize指在某种测量模式下的规格大小。这种将来两个个值打包成一个int值，可以避免过多的对象内存分配。对于 SpecModel 主要有如下三种模式：  

* **UNSPECIFIED**
  该模式下，父容器不对View 有任何限制，要多大给多大，一般用于系统内部。
* **EXACTLY**
  该模式下，父容器已经检测出 View 所需的精确大小，此时 View 的最终大小就是 SpecSize，它对应 LayoutParams 中的 match_parent 和 具体数值。  
* **AT_MOST**
  View 的大小不能超过父容器指定的可用大小 (SpecSize) ,它对应 LayoutParams 中的 wrap_parent。
  
我们来看看子view得到MeasureSpec的一个途径ViewGroup的measureChildWithMargins（View child，int parentWidthMeasureSpec，int widthUsed，int parentHeightMeasureSpec，int heightUsed）方法：  
对于参数中的widthUsed和heightUsed进行以下说明：这两个参数代表的是宽度或者高度上已经被占用的空间，比如被其他的View所占用了。  

	   protected void measureChildWithMargins(View child, 
        int parentWidthMeasureSpec, int widthUsed, 
        int parentHeightMeasureSpec, int heightUsed) { 
    final MarginLayoutParams lp = (MarginLayoutParams) child.getLayoutParams(); 
 
    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec, 
            mPaddingLeft + mPaddingRight + lp.leftMargin + lp.rightMargin 
                    + widthUsed, lp.width); 
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec, 
            mPaddingTop + mPaddingBottom + lp.topMargin + lp.bottomMargin 
                    + heightUsed, lp.height); 
 
    child.measure(childWidthMeasureSpec, childHeightMeasureSpec); 
	}   
这个方法很简单，首先获得子View的LayoutParams，**然后根据子View的LayoutParams布局参数和父容器的MeasureSpec来确定子View的MeasureSpec**，这就是之前说的MeasureSpec是由LayoutParams和父容器共同决定的；接下来看看getChildMeasureSpec这个方法是怎么创建子View的MeasureSpec的：  

	public static int getChildMeasureSpec(int spec, int padding, int childDimension) { 
        int specMode = MeasureSpec.getMode(spec); 
        int specSize = MeasureSpec.getSize(spec); 
 
        int size = Math.max(0, specSize - padding); 
 
        int resultSize = 0; 
        int resultMode = 0; 
 
        switch (specMode) { 
        // Parent has imposed an exact size on us 
        case MeasureSpec.EXACTLY: 
            if (childDimension >= 0) { 
                resultSize = childDimension; 
                resultMode = MeasureSpec.EXACTLY; 
            } else if (childDimension == LayoutParams.MATCH_PARENT) { 
                // Child wants to be our size. So be it. 
                resultSize = size; 
                resultMode = MeasureSpec.EXACTLY; 
            } else if (childDimension == LayoutParams.WRAP_CONTENT) { 
                // Child wants to determine its own size. It can't be 
                // bigger than us. 
                resultSize = size; 
                resultMode = MeasureSpec.AT_MOST; 
            } 
            break; 
 
        // Parent has imposed a maximum size on us 
        case MeasureSpec.AT_MOST: 
            if (childDimension >= 0) { 
                // Child wants a specific size... so be it 
                resultSize = childDimension; 
                resultMode = MeasureSpec.EXACTLY; 
            } else if (childDimension == LayoutParams.MATCH_PARENT) { 
                // Child wants to be our size, but our size is not fixed. 
                // Constrain child to not be bigger than us. 
                resultSize = size; 
                resultMode = MeasureSpec.AT_MOST; 
            } else if (childDimension == LayoutParams.WRAP_CONTENT) { 
                // Child wants to determine its own size. It can't be 
                // bigger than us. 
                resultSize = size; 
                resultMode = MeasureSpec.AT_MOST; 
            } 
            break; 
 
        // Parent asked to see how big we want to be 
        case MeasureSpec.UNSPECIFIED: 
            if (childDimension >= 0) { 
                // Child wants a specific size... let him have it 
                resultSize = childDimension; 
                resultMode = MeasureSpec.EXACTLY; 
            } else if (childDimension == LayoutParams.MATCH_PARENT) { 
                // Child wants to be our size... find out how big it should 
                // be 
                resultSize = 0; 
                resultMode = MeasureSpec.UNSPECIFIED; 
            } else if (childDimension == LayoutParams.WRAP_CONTENT) { 
                // Child wants to determine its own size.... find out how 
                // big it should be 
                resultSize = 0; 
                resultMode = MeasureSpec.UNSPECIFIED; 
            } 
            break;  www.2cto.com
        } 
        return MeasureSpec.makeMeasureSpec(resultSize, resultMode); 
    }   
首先看看这个方法的参数都是怎么传过来的：  
由于这里展示的方法是从ViewGroup的measureChildWithMargins方法中调用的，所以这里的第一个参数代表的是**父容器的MeasureSpec**，第二个参数代表的是当前view的水平方向上**被占用的空间（包括了内外边距以及其他东西占用的空间）**，第三个参数是**LayoutParams中的layout_width属性**  
首先看看这行代码：int size = Math.max(0, specSize - padding);这里的size就是子View在水平方向上能够使用的最大空间，当子view的layout_width为wrap_content的时候，那么此时的最大宽度就是这个size。确定子View的MeasureSpec非常简单，**如果子View的layout_width是一个正的确切的值，那么不管父容器的specMode是什么，子View的specMode都是MeasureSpec.EXACTILT;如果子View的layout_width属性是wrap_content，那么不管父容器的specMode是什么，子View的specMode都是MeasureSpec.AT_MOST;当父容器的specMode是MeasureSpec.EXACTILT的时候，如果子View的specMode是match_parent，那么子View的spe就是MeasureSpec.EXACTILT,当父容器的specMode是MeasureSpec.AT_MOST的时候，如果子View的specMode是match_parent，那么子View的spe就是MeasureSpec.AT_MOST,但是子View的尺寸都是这个可用的最大值size。**在这里有一点需要注意，我们在自定义view的时候，需要重写onMeasure方法，在**处理wrap_content这种属性的时候，如果想要实现真正意义上的wrap_content，不能直接在setMeasureDimension（int measuredWidth,int measuredHeight）方法中直接将specSize作为参数传递过去**，因为根据上面的源码分析，wrap_content以及match_parent时的specSize都是当前可用的最大尺寸size，这样一来的话直接将这个尺寸作为参数传递过去，wrap_content就和match_parent没有区别了。而TextView在处理wrap_content的时候就进行了特殊的处理。  
## 3. 细节  
如果在自定义View的过程中，想要padding属性有效果，那么需要手动完成；但是，margin属性的处理就不用去理会，因为margin属性的处理是由ViewGroup完成的，不用我们去处理。注意：处理padding是在onDraw方法中处理的，不是在onMeasure方法中，因为padding空白也是view的尺寸。

