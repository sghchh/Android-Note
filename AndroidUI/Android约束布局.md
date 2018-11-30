#Android布局---ConstraintLayout(约束布局)  
作为一个强大的布局，现在Google已经将ConstraintLayout作为了默认的布局，所以想要避开这个布局是不可能的。  
##1. 特色  
根据名字就可以看出，这个布局的设计理念是**根据别的View来约束当前View的大小和位置**，下面就来见识一下ConstraintLayout的一些特色的属性：  
####1.1 常用属性  
要想使用这些属性，就要像使用自定义view的属性一样，增加一个xml的命名空间，这里就把他命名为app。  
属性是这样描述一个约束的：  
**layout_constraint[SourceAnchor]_[TargetAnchor]**="[TargetID]"  
意思就是**新增加的控件的一个参考方位（SourceAnchor），位于目标View（TargetID代表的view）的某一方位（TargetAnchor）**；举个例子：  
有两个Button，1和2；在button2中加入如下属性：  
app:layout_constraintLeft_toRightOf="@id/button1"  
这个属性的结果就是，button2的左侧位于button1的右侧，效果就是button2横向排列在button1的右侧，这句代码就和在RelativeLayout中的layout_toRightOf属性是一个效果。  
**注意：**1. 如果View2使用View1来作为约束，并且设置了app:layout_constraintLeft_toLeftOf="@id/view1"和app:layout_constraintRight_toRightOf="@id/view1"，很明显，想要实现的效果是两个View同宽，但是如果View2的需求宽（控件中显示完整的内容所需的宽）或者设置了比View1要宽的layout_width值，那么这两个属性是起不到效果的。如果想要实现效果，忽略显示内容过多的情况，可以设置view2的layout_width="0"  
2. layout_constraintBaseline_toBaselineOf是控件中文字的底部和参照物控件的文字的底部做约束。亲身测试，这个属性会覆盖app:layout_constraintTop_toBottomOf属性，即效果回事两个控件水平横向排列，不会出现第二个在第一个下面  
#### 1.2 特色属性  
**layout_constraintDimensionRatio属性来设置宽高的比值。**  
比如layout_constraintDimensionRatio=“1:1”的意思就是width:height=1:1   **注意：1. 这是width：height 2. 使用这个属性的时候，layout_width和layout_height中要有一个为0dp**  
  