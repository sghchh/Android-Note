# Android Bitmap 学习  
安卓中的图像处理经常会遇到，而图像处理则离不开Bitmap的操作，今日特地来系统的学习一下Bitmap相关的知识。  

查阅Android官方文档，发现Bitmap相关的类有很多：  

* Bitmap
* [Bitmap.CompressFormat](http://www.zhdoc.net/android/reference/android/graphics/Bitmap.CompressFormat.html)  
* [Bitmap.Config](http://www.zhdoc.net/android/reference/android/graphics/Bitmap.Config.html)
* Bitmap.Compat
* BitmapDrawable
* BitmapFactory
* [BitmapFactory.Options](http://www.zhdoc.net/android/reference/android/graphics/BitmapFactory.Options.html)
* BitmapRegionDecoder
* BitmapShader

## 1. Bitmap.CompressFormat  
这个类很简单，是一个**枚举类**，官方文档对它的描述是:  
> Specifies the known formats a bitmap can be compressed into  

说白了，就是封装了当前的Bitmap的压缩格式。  
这个类的成员有：  

* JPEG
* PNG
* WEBP  

创建一个该类的实例的方法是一个静态方法：  

	static Bitmap.CompressFormat valueOf(String name)  
	例如：
	type=Bitmap.CompressFormat.valueOf("png")  

## 2. Bitmap.Config  
这个类同样很简单，也是一个**枚举类**，官方文档对其的描述为：  

>Possible bitmap configurations. A bitmap configuration describes how pixels are stored. This affects the quality (color depth) as well as the ability to display transparent/translucent colors.  

根据官方文档的描述，我们可以知道，这个类是Bitmap的配置辅助类；描述了像素点是如何储存的，不同的储存方式反映了图片的质量(这里的质量不是物理上的重量)。  

该类的成员有：  

* ALPHA_8
* ARGB_4444
* ARGB_8888
* RGB_565  
* RGBA_F16  

创建一个该类的实例的方法同上面一样，也是通过一个静态方法:  

	static Bitmap.Config valueOf(String name)  

## 3. BitmapFactory.Options  

这个类在进行Bitmap的处理(比如质量或者体积的压缩)的时候很有用，但是官方文档中并没有什么描述。 
下面是一些该类中的成员来进行相关的设置：  

* int inDensity:该Bitmap的像素密度  
* boolean inJustDecodeBounds:如果设置为true，那么decoder只会返回该位图对应的矩形(outXXX成员，具有长和宽)
* boolean inMutable:决定decoder返回的是否是一个可变的Bitmap
* int inSampleSize:缩放比例，如果大于1，说明是压缩
* int inTargetDensity:表示要被画出来时的目标像素密度  
* int inScreenDensity:表示实际设备的像素密度
* int outWidth/outHeight:表示这个Bitmap的宽和高，一般和inJustDecodeBounds一起使用来获得Bitmap的宽高，但是不加载到内存。  

### 3.1 最中的缩放比例  
在进行缩放的时候最终的缩放比例**不只是inSampleSize**决定的：  
![](https://img-blog.csdn.net/20150801142117637?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  

**输出图片的宽高= 原图片的宽高 / inSampleSize * (inTargetDensity / inDensity)**