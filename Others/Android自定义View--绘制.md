# Android自定义View绘制的基础知识学习  
> 本笔记内容学习参考：http://hencoder.com/ui-1-2/  

## 总领
自定义View是安卓中的一个比较有趣但是有一定难度的事情，今天就来学习一下自定义View时如何将View绘制出来：  
>自定义View的绘制：  
>1. 重写onDraw(Canvas canvas)方法(负责主题内容的绘制)进行绘制，绘制过程中Paint负责设置颜色和风格，范围裁切(clipXXX)和几何变换(Matrix)作为辅助来实现不同效果  
>2. 对于绘制顺序，覆盖关系有要求的了解其他的绘制方法  
>**理解：Paint的存在是设置共有信息的，比如颜色风格等，不管绘制什么内容都会涉及到这些参数的配置；所以都把这些参数放到了Paint这个辅助类中；而其他的独有参数，如圆的半径圆心坐标等，都是canvas.drawXXX方法的直接参数了。**  

#### 关于颜色  
Canvas对于颜色的控制有三个阶段：  
![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6dcywn2j20j909yabu.jpg)  
##### 基本颜色  
![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6gxcusnj20iw04xmzr.jpg)  
其中Paint对于颜色的控制又有两种：一种是直接用 Paint.setColor/ARGB() 来设置颜色，另一种是使用 Shader 来指定着色方案。

## 1. Canvas.drawXXX()系列   
大部分情况下我们都是通过Canvas的drawXXX方法来绘制图形的，接下来就认识一下这些方法：  

#### 1.1 绘制颜色  
* **drawColor（int color)**:这个方法简单直接，就是把一个颜色填充到View的区域，**注意：这个方法不需要Paint参数**  
* **drawRGB(int r,int g,int b)**:这个方法也是绘制颜色，只不过红绿蓝三种颜色要手动分别设置。  
* **drawARGB(int alph,int r,int g,int b)**:方法同上，只不过加了个透明度的参数。  

反正纵观三个绘制颜色的方法，个人比较习惯第一个方法，简单易用。同时，我们自定义View的时候绘制颜色这个方法很少用，一般是用作加一个半透明的蒙版之类的效果。  
#### 1.2 绘制圆形  
绘制圆形的方法很简单，就一个**drawCircle(float cx,float cy,float radius,Paint paint)**方法的前三个参数分别是圆心的横纵坐标，半径；文章的作者的一句话：  
> 圆心坐标和半径，这些都是圆的基本信息，也是它的独有信息。什么叫独有信息？就是只有它有，别人没有的信息。你画圆有圆心坐标和半径，画方有吗？画椭圆有吗？这就叫独有信息。独有信息都是直接作为参数写进 drawXXX() 方法里的（比如 drawCircle(centerX, centerY, radius, paint) 的前三个参数）。

> 而除此之外，其他的都是公有信息。比如图形的颜色、空心实心这些，你不管是画圆还是画方都有可能用到的，这些信息则是统一放在 paint 参数里的。  

这几句话可以帮我们更好的理解Paint和Canvas之间的关系。  
#### 1.3 绘制矩形  
绘制矩形也是很套路：  

* **drawRect(float left,float top,float right,float bottom,Paint paint)**:前四个参数分别代表了矩形的四个边对相应的坐标，矩形就是这个四个坐标围成的区域  

除此之外，绘制矩形还有两个重载的方法drawRect(RectF rect, Paint paint) 和 drawRect(Rect rect, Paint paint) ，让你可以直接填写 RectF 或 Rect 对象来绘制矩形。  
> 提问：Rect和RectF有啥区别？  

#### 1.4 绘制点  
安卓绘制点通过**drawPoint(float cx,float cy,Paint paint)**  
其中Paint中可以通过方法setStrokeCap（）方法来设置点的样式，这个方法的参数有三个值：  

* Paint.Cap.BUTT  
* Paint.Cap.SQUARE:点是方形  
* Paint.Cap.ROUND:点是圆点  

实际上，这个方法一般不用来绘制点的时候，而是绘制Path的时候，当Path不是一个闭合的路径的时候，当绘制完最后一笔的时候，就涉及到最后是什么样式了，就类似于写字的收笔的时候那一个顿笔一样。  
![](https://ws4.sinaimg.cn/large/006tNc79ly1fig74qv8rij30ct05rglp.jpg);  
**drawPoints(float[] pts, int offset, int count, Paint paint) / drawPoints(float[] pts, Paint paint) 画点（批量）**  
同样是画点，它和 drawPoint() 的区别是可以画多个点。pts 这个数组是点的坐标，每两个成一对；offset 表示跳过数组的前几个数再开始记坐标；count 表示一共要绘制几个点。说这么多你可能越读越晕，你还是自己试试吧，这是个看着复杂用着简单的方法。

#### 1.5 绘制椭圆  
绘制椭圆和绘制矩形的参数一样：  
**drawOval(float left, float top, float right, float bottom, Paint paint)** 画椭圆，另外，和绘制矩形一样，它也有一个重载方法另外， drawOval(RectF rect, Paint paint)，让你可以直接填写 RectF 来绘制椭圆。    

#### 1.6 绘制直线  
**drawLine(float startX, float startY, float stopX, float stopY, Paint paint) 画线：**前四个参数分别是起始端点和结束端点的坐标。  
#### 1.7 绘制圆角矩形  
**drawRoundRect(float left, float top, float right, float bottom, float rx, float ry, Paint paint) 画圆角矩形**：其中第五第六个参数是表示圆角的横向半径和纵向半径。  
#### 1.8 绘制弧形  
**drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint) 绘制弧形或扇形**，这个方法的参数需要说明一下，第五个参数是起始的角度，0度角代表的是x轴的方向，角度为正值说明顺时针取，负值说明是逆时针取；而且前四个参数代表的是弧形所在的椭圆区域；useCenter 表示是否连接到圆心，如果不连接到圆心，就是弧形，如果连接到圆心，就是扇形。  
#### 1.9 绘制位图  
**drawBitmap(Bitmap bitmap, float left, float top, Paint paint) 画 Bitmap** ：绘制 Bitmap 对象，也就是把这个 Bitmap 中的像素内容贴过来。其中 left 和 top 是要把 bitmap 绘制到的位置坐标。  
#### 1.10 绘制文字  
**drawText(String text, float x, float y, Paint paint) 绘制文字** ：drawText() 这个方法就是用来绘制文字的。参数  text 是用来绘制的字符串，x 和 y 是绘制的起点坐标。  
#### 1.11 绘制路径  
**drawPath(Path path, Paint paint) 画自定义图形**：绘制路径也可以理解为自定义图形的绘制，较为复杂，但是效果肯定比那些简单的绘制一个集合图形要炫酷的多。  
## 2. Path类  
由于Path类在绘制中的方法有点多，而且一些东西还比较复杂，所以就单独摘出来作为一个版块儿进行讲解。Path顾名思义，是一个路径的描述，绘制的时候就按照Path描述的路径进行绘制：  
### 2.1 addXXX（）--添加子图形  
Path类有一些addXXX方法可以直接添加一些现有的将几何图形，从而简化了我们的工作量：  
比如：**addCircle(float x, float y, float radius, Direction dir) 添加圆**。看这个方法的前三个参数，发现和Canvas.drawCircle方法的参数一样，实际上就是一样，但是第四个参数Direction是什么动西呢？这个有两个选择“顺时针”“逆时针”，在图形的填充的时候会有不同的效果。  
就像Canvas.drawXXX系列一样，Path都有对应的addXXX方法，而且最后一个参数都是Direction。  
如果Path只是调用了这一个addXXX方法，接着就调用Canvas.drawPath（），那么得到的效果就和Canvas.drawXXX的效果是一样的。  
### 2.2 xxxTo方法--画线  
#### 2.2.1 lineTo/rLineTo--画直线  
**lineTo(float x, float y) / rLineTo(float x, float y) 画直线**：从当前位置向目标位置画一条直线， x 和 y 是目标位置的坐标。这两个方法的区别是，lineTo(x, y) 的参数是绝对坐标，而 rLineTo(x, y) 的参数是相对当前位置的相对坐标 （前缀 r 指的就是 relatively 「相对地」)  
#### 2.2.2 画贝塞尔曲线  
**quadTo(float x1, float y1, float x2, float y2) / rQuadTo(float dx1, float dy1, float dx2, float dy2) 画二次贝塞尔曲线**  
**cubicTo(float x1, float y1, float x2, float y2, float x3, float y3) / rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3) 画三次贝塞尔曲线**  
贝塞尔曲线的画法，暂时就不写了，用到了再说。  
#### 2.2.3 moveTo--控制始末  
**moveTo(float x, float y) / rMoveTo(float x, float y) 移动到目标位置**：不论是直线还是贝塞尔曲线，都是以当前位置作为起点，而不能指定起点。但你可以通过 moveTo(x, y) 或  rMoveTo() 来改变当前位置，从而间接地设置这些方法的起点。  
#### 2.2.4 arcTo--绘制圆弧  
**arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(RectF oval, float startAngle, float sweepAngle) 画弧形**：它们也是用来画线的，但并不使用当前位置作为弧线的起点。  
这个方法和 Canvas.drawArc() 比起来，少了一个参数 useCenter，而多了一个参数 forceMoveTo 。
少了 useCenter ，是因为 arcTo() 只用来画弧形而不画扇形，所以不再需要 useCenter 参数；而多出来的这个 forceMoveTo 参数的意思是，绘制是要「抬一下笔移动过去」，还是「直接拖着笔过去」，区别在于是否留下移动的痕迹
  
## 3. Paint类  
Paint作为一个辅助类，主要是用来进行色彩等的配置：  
1. 当Paint设置风格为STOKE是，并且设置了Stoken_width为a，如果执行Canvas的drawCircle方法，最终的圆的半径是radius参数加上a/2.  

### 3.1 Paint对于颜色的控制  
#### 3.1.1 直接设置颜色  
1. setColor(int color)  
2. setARGB(int alph,int red,int green,int blue)  

#### 3.1.2 设置Shader  
**setShader(Shader shader)方法会覆盖之前的setColor()方法**  
Shader有五个实现类：  
1. 线性渐变LinearGradient：构造器参数为**两个点的横纵坐标，两个点的颜色，渲染类型TileMode**  
2. 辐射渐变RadialGradient：构造器参数为**圆心横纵坐标，半径，起始颜色，渲染类型**  
3. 扫描渐变SweepGradient：构造器参数为**圆心横纵坐标，起始颜色，渲染类型**  
4. 图形填充BitmapShader：用图形或文字作为区域的填充，构造器参数为**Bitmap，X方向的渲染类型，Y方向的渲染类型**  
5. 混合着色器ComposeShader：将上面的多个Shader合成，构造参数为**两个Shader，绘制策略PorterDuff.Mode**    

>TileMode有三种：CLAMP,MIRROR,REPEAT  
>PorterDuff.Mode有17中，可分为两类：  
>1. alpha合成  
>2. 混合  
>详情还是参考文章：[http://hencoder.com/ui-1-2/](http://hencoder.com/ui-1-2/)  

### 3.2 Paint对于颜色的处理  
Paint类对于颜色的处理通常通过ColorFilter类来进行颜色的过滤，这个各类有三个子类：LightingColorFilter，PorterDuffColorFilter和ColorMatrixColorFilter。具体使用还看文章：[http://hencoder.com/ui-1-2/](http://hencoder.com/ui-1-2/)  
### 3.3 Paint对绘制效果的控制  
Paint可以通过setXfermode(Xfermode)方法对绘制的效果进行控制。"Xfermode" 其实就是 "Transfer mode"，用 "X" 来代替 "Trans" 是一些美国人喜欢用的简写方式。严谨地讲， **Xfermode 指的是你要绘制的内容和 Canvas 的目标位置的内容应该怎样结合计算出最终的颜色。但通俗地说，其实就是要你以绘制的内容作为源图像，以 View 中已有的内容作为目标图像，选取一个  PorterDuff.Mode 作为绘制内容的颜色处理方案。**   
### 3.4 其他零碎的风格  
1. setAntiAlias(boolean):设置抗锯齿  
2. setStyle(Paint.Style):设置风格线条还是填充  
3. setStrokeXXX:设置线条的一些细节  
	* setStrokeWidth(float):设置线条的宽度，单位是像素  
	* setStrokeCap(Paint.Cap):设置线头的形状：BUTT 平头、ROUND 圆头、SQUARE 方头。默认为 BUTT。  
	* setStrokeJoin(Paint.Join):设置拐角的形状。有三个值可以选择：MITER 尖角、 BEVEL 平角和 ROUND 圆角。默认为 MITER。  
	* setStrokeMiter(float):这个方法是对于 setStrokeJoin() 的一个补充，它用于设置 MITER 型拐角的延长线的最大值。所谓「延长线的最大值」

### 3.5 色彩优化  
Paint 的色彩优化有两个方法： setDither(boolean dither) 和 setFilterBitmap(boolean filter) 。它们的作用都是让画面颜色变得更加「顺眼」，但原理和使用场景是不同的。  
1. setDither(boolean):增加抖动，可以认为是在颜色分明的临界位置附近加入噪点，使得颜色的变化不那么突兀，而更加顺畅。  
2. setFilterBitmap(boolean):双线性过滤，使得放大的图片不会出现马赛克式的颜色分明的现象，而是一种模糊状态。  

### 3.6 线条轮廓控制  
使用Paint的setPathEffect(PathEffect effect)来给Canvas绘制出来的图形的轮廓设置不同的效果，有虚线，化角为弧，无规则，以形画线等，具体还是参考上面的链接。  
### 3.7 
其他的风格控制，如设置阴影，模糊等也是参考链接。  

## 4. 绘制辅助  
### 4.1 范围裁切  
范围裁切有两个方法： clipRect() 和 clipPath()。裁切方法之后的绘制代码，都会被限制在裁切范围内。  
根据名字就能知道这两个方法怎么用了，就不多介绍了。  
>如果一个绘制过程中要绘制两个Bitmap，其中某一个需要范围裁切，而另一个不需要，这时候如果在其中一个drawBitmap之前调用了clipXXX方法，那么如果是在第一次的drawBitmap之前调用了clipXXX方法，那么第二次的drawBitmap也会有范围的裁切；如果只想要第一次有范围裁切怎么办呢？**在clipXXX方法调用之前调用canvas.save();当第一次的drawBitmap方法被调用后，调用canvas.restore()方法。**  

### 4.2 几何变换   
几何变换的使用大概分为三类：

1. 使用 Canvas 来做常见的二维变换；  
2. 使用 Matrix 来做常见和不常见的二维变换；  
3. 使用 Camera 来做三维变换。
#### 4.2.1 Canvas自身方法进行简单的几何变换  
Canvas自身能够做到只有最简单的几种集合变换：  

* Canvas.translate(float x,float y):根据x和y参数进行横向和纵向的平移。  
* Canvas.rotate(float degrees,float x,float y):根据x，y轴心的位置旋转degrees度。  
* Canvas.scale(float sx,float sy,float px,float py):根据轴心px,py进行横向放缩sx倍纵向放缩sy倍
* Canvas.skew(float sx,float sy):进行x方向和y方向的错切。  

>**注意：在用Canvas进行连续多次的几何变换的时候，Canvas的顺序是反的，比如：我想先进行平移，再进行旋转；在写代码的时候旋转要写在平移的前面。**    

#### 4.2.2 Matrix进行变换  
**Matrix进行常见变换的方式：**  
1. 创建Matrix对象  
2. 调用 Matrix 的 pre/postTranslate/Rotate/Scale/Skew() 方法来设置几何变换；  
3. 使用 Canvas.setMatrix(matrix) 或 Canvas.concat(matrix) 来把几何变换应用到 Canvas。  

>**可以看到Matrix的每一种变换的方法都是成对儿存在的，即pre/postXXX方法，这两种方法的意思是向前和向后变换pre系列的方法表示向前插入变换，即后调用的先执行，是一种反序，和Canvas本身的变换是一个效果；而post表示向后插入，代码顺序就是执行顺序。**  

把 Matrix 应用到 Canvas 有两个方法： Canvas.setMatrix(matrix) 和 Canvas.concat(matrix)。

1. Canvas.setMatrix(matrix)：用 Matrix 直接替换 Canvas 当前的变换矩阵，即抛弃 Canvas 当前的变换，改用 Matrix 的变换（注：不同的系统中 setMatrix(matrix) 的行为可能不一致，所以还是尽量用 concat(matrix) 吧）；
2. Canvas.concat(matrix)：用 Canvas 当前的变换矩阵和 Matrix 相乘，即基于 Canvas 当前的变换，叠加上 Matrix 中的变换。  

**Matrix自定义变换**  
Matrix 的自定义变换使用的是 setPolyToPoly() 方法。
**Matrix.setPolyToPoly(float[] src, int srcIndex, float[] dst, int dstIndex, int pointCount) 用点对点映射的方式设置变换**  
参数里，src 和 dst 是源点集合目标点集；srcIndex 和 dstIndex 是第一个点的偏移；pointCount 是采集的点的个数（个数不能大于 4，因为大于 4 个点就无法计算变换了）。  
poly 就是「多」的意思。setPolyToPoly() 的作用是通过多点的映射的方式来直接设置变换。「多点映射」的意思就是把指定的点移动到给出的位置，从而发生形变。例如：(0, 0) -> (100, 100) 表示把 (0, 0) 位置的像素移动到 (100, 100) 的位置，这个是单点的映射，单点映射可以实现平移。而多点的映射，就可以让绘制内容任意地扭曲  
### Camera变换  
>请参考视频：[https://www.bilibili.com/video/av12937987/](https://www.bilibili.com/video/av12937987/)  



