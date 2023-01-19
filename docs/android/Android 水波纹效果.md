## 前言

水波纹效果从Android5.0就已经出来了，基本的使用相信大家都知道了，这里多谈一些相对深层次的使用：

- 1、基本使用
- 2、水波纹效果与布局绘制之间的问题
- 3、长按水波纹扩散效果
- 4、Button点击的水波纹效果

## 基本使用

### 系统自带水波纹实现方式

#### 有界水波纹

```java
android:background="?android:attr/selectableItemBackground"
```

#### 无界水波纹

> 以控件宽高中最大的数值作为水波纹效果所在正方形的边界进行绘制

```java
android:background="?android:attr/selectableItemBackgroundBorderless"
```

### 自定义水波纹实现方式

#### 无界水波纹

```java
<?xml version="1.0" encoding="utf-8"?>  
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
android:color="@color/colorAccent"> 
</ripple> 
```

#### 有界水波纹

```java
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="@color/itemBackground">
    <item >
        <color android:color="@android:color/white" />
    </item>
</ripple>
```

圆角背景：

```java
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="@color/colorTheme">

    <!--背景透明色 android:id="@android:id/mask"-->
    <item>
        <shape android:shape="rectangle">
            <padding
                android:bottom="5dp"
                android:left="15dp"
                android:right="15dp"
                android:top="5dp" />
            <solid android:color="@color/color_page_bg" />
            <corners android:radius="100dp" />
        </shape>
    </item>

</ripple>
```

## 水波纹效果与布局绘制之间的问题

![img](https:////upload-images.jianshu.io/upload_images/1354448-37ff47faeefa1837.png?imageMogr2/auto-orient/strip|imageView2/2/w/632/format/webp)

在使用了以上的**自定义有界水波纹点击效果**后，使用[开发者选项 - 调试GPU过渡绘制]得到下面的视图

![img](https:////upload-images.jianshu.io/upload_images/1354448-d64a1d0d128a14f4.png?imageMogr2/auto-orient/strip|imageView2/2/w/756/format/webp)

自定义有界水波纹

对比后，发现绿色的文字部分经过了二重绘制，因为布局的白色背景和文字自身颜色的原因。如果布局背景能去掉还能实现水波纹的效果就好了，这样就只有文字一层的颜色。

### 有两种方案可以达到想要的这种效果：

1、使用系统自带有界水波纹实现方式，因为系统本身的默认背景是透明色的。

```java
android:background="?android:attr/selectableItemBackground"
```

> 系统的默认水波纹颜色是灰色，如果需要使用对应的高亮色来作为ripple的背景色，我们可以在`styles-v21`系统主题里加入这个：`<item name = "android:colorControlHighlight">@color/colorAccent</item>`

2、使用自定义有界水波纹效果，使其默认背景色为透明色。
写法：

```java
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="@color/itemBackground">
    <item android:id="@android:id/mask">
        <color android:color="@android:color/white" />
    </item>
</ripple>
```

> 添item时，如果***指定id为@android:id/mask\***，那么不点击时不会显示出该item指定的color。
可以设置指定子层item的android:id="@android:id/mask"来设定当前Ripple的Mask。
Mask的内容并不会被绘制到屏幕上，它的作用是限定Ripple效果的绘制区域。

最后可以得到我们想要的效果：

![img](https:////upload-images.jianshu.io/upload_images/1354448-9200fb6b1432db62.png?imageMogr2/auto-orient/strip|imageView2/2/w/648/format/webp)

image.png

## 长按水波纹扩散效果

![img](https:////upload-images.jianshu.io/upload_images/1354448-8f11f2c691d43da9.gif?imageMogr2/auto-orient/strip|imageView2/2/w/373/format/webp)

长按水波纹扩展效果

在使用小红书时，我们可以看到关于“笔记”的item长按会展示扩散的效果。

```java
android:foreground="?attr/selectableItemBackgroundBorderless"
```

又或者，无边界的水波纹也可以达到长按扩散的效果，只是它会超出边界，那我们就在对应的父布局加一层有边界的水波纹背景即可。就像这样：

```java
<RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/selectableItemBackground">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:onClick="@{()->adapter.openDetail(bean)}"
            android:padding="8dp">
        </RelativeLayout>
</RelativeLayout>
```

> 两者的区别是：长按扩散时，前者的水波纹会在图片之上，而后者在图片之下。

## Button点击的水波纹效果

![img](https:////upload-images.jianshu.io/upload_images/1354448-5d195c450d541d19.gif?imageMogr2/auto-orient/strip|imageView2/2/w/350/format/webp)

button阴影与点击效果

```java
<Button
    style="@style/Widget.AppCompat.Button.Colored"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_marginLeft="20dp"
    android:layout_weight="1"
    android:onClick="login"
    android:text="登陆"
    android:textColor="@android:color/white"
    android:textStyle="bold" />
```

> The Widget.AppCompat.Button.Colored 继承了 Widget.AppCompat.Button style并且根据你选择的主题应用最接近的颜色。
