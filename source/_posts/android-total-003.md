---
title: 综合篇 第三章 Android Support Library
date: 2015-7-17 19:05:35
author: Cutler
categories: Android - 中级开发
---

# 第一节 概述 #

　　`Android Support Library`是Google官方提供的一组代码库，提供向后兼容版本的Android框架API，若需要在低版本系统中使用高版本中的功能则只能通过该库的API。 

　　加入它们后，您的应用程序既可以使用支持库所提供的高版本的特点，并且仍然可以在低版本中运行。

<br>**组成**

>Support库目前包含5个库（v4、v7、v8、v13、v17），其中‘v’代表该支持库所支持的最低版本的API等级，如v4即指最低支持Android1.6。每个库支持一个特定范围的Android平台版本和功能集。
>一般来说，我们建议包括v4和v7库到你的应用程序中，因为他们支持广泛的Android版本，并提供API推荐用户界面模式。

<br>**下载**
　　`Android Support Library`是对SDK的补充，可以使用`Android SDK Manager`来下载。具体步骤如下：

    1.  启动SDK Manager，通过SDK安装的根目录下的SDK Manager.exe文件。
    2.  打开SDK Manager然后滚动到最底部，找到“Extras”文件夹并将其展开。
    3.  若你使用Eclipse开发项目则需要选择“Android Support Library”，但如果你使用Android Studio开发则需要下载“Android support Repository”。
    4.  点击“安装”按钮。
 
　　下载完毕后，这些文件被放入到`“<sdk>/extras/android/support/”`目录下面。
<br>　　下面就依次介绍这些支持库的应用场景及特点，你可以依据自己的需求来选择使用哪一个支持库。

## V4 支持库 ##
　　这个支持库是为`Android1.6`(`API Level 4`)或更高版本的平台设计的。相比其他库它包括更大量的`APIs`。包括应用程序组件、用户接口特性、可访问性、数据处理、网络连通性和编程工具。这里有几个关键的类包含在`v4`库：

    -  应用程序组件：
       -  Fragment：增加了支持封装的用户界面和功能的Fragment，使应用程序可以方便的在小和大屏幕设备之间提供布局。
       -  NotificationCompat：可以支持丰富的通知功能。
       -  LocalBroadcastManager：允许应用程序在其内部很容易注册和接收意图，一个私有的广播。
    -  用户接口：
       -  ViewPager：增加一个ViewGroup来管理布局里的子View，用户可以通过手指左右滑动在子View之间来回切换。
       -  PagerTitleStrip：PagerTitleStrip是ViewPager的导航栏（不可以点击）。
       -  PagerTabStrip：PagerTitleStrip是ViewPager的导航栏（可以点击）。
       -  DrawerLayout：可以支持创建导航抽屉，可以从一个窗口的边缘。
       -  SlidingPaneLayout：可以用它实现滑动菜单效果，具体参见 http://my.oschina.net/summerpxy/blog/211835 。
    -  可访问性：
       -  ExploreByTouchHelper、AccessibilityEventCompat。
       -  AccessibilityNodeInfoCompat、AccessibilityNodeProviderCompat。
       -  AccessibilityDelegateCompat
    -  数据处理：
       -  Loader：可以支持异步加载数据。在V4支持库中也提供了这个类的具体实现，包括CursorLoader和AsyncTaskLoader。
       -  FileProvider：可以支持应用程序之间共享的私人文件。
　　提示：还有许多其他`API`包含在这个库中。更详细的介绍，请参看`android.support.v4`包。这个库放在：`<sdk>\extras\android\support\v4`目录下。

## V7 支持库 ##
　　除了`v4`以外，还有一些库设计用于与`Android 2.1`(`API level 7`)或更高的平台。v7支持库其实是一组库的集合，它们分别用于不同的场景，其中有几个子库体积比较大，因而不再像`v4`包那样合在一起，而是将它们分开发布，下面将依次介绍它们。

<br>**V7 appcompat 库**
　　这个库添加了对 [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html) 的支持，同时也支持`Android5.0`（`Android Lollipop`）提出的`Material Design`风格。
　　值得注意的是，这个库需要依赖于`v4`库，如果你使用`Ant`或者`Eclipse`，要确保这个库中的类可以引用到`v4`库中的类。

　　这里有几个关键的类包含在`v7 appcompat`库：

    -  ActionBar：提供了ActionBar的实现。
    -  ActionBarActivity：需要使用ActionBar的Activity必须派生自此类。
    -  ShareActionProvider：可以支持一个标准化共享行动(比如电子邮件或发布到社交应用程序)，可以包含在操作栏中。

　　这个库放在：`<sdk>/extras/android/support/v7/appcompat/`目录下。

<br>**V7 cardview 库**
　　这个库添加了对`CardView`类的支持，它是`Android 5.0`推出的一个卡片式的`View`组件。卡片化是全新的`Material`风格设计中重要的组成部分之一，卡片设计适合重要信息的展示，以及在`List`中作为一个包含有复杂操作的`item`使用。
　　`CardView`继承自`FrameLayout`类，同时卡片可以包含圆角、阴影。
　　经常使用`Google APP`的人应该对卡片式设计非常熟悉，不管是`Google Now`、`Google Email`、`Google+`等都沿用了卡片式设计的风格，非常清新明了，感觉没有任何冗余的信息。它的具体用法此处就不在展开介绍了。
　　这个库放在：`<sdk>/extras/android/support/v7/gridlayout/ `目录下。

效果参看：

- [《Android L中的RecyclerView 、CardView 、Palette的使用》](http://blog.csdn.net/xyz_lmn/article/details/38735117)
- [《使用CardView实现卡片式Google Plus布局》](http://www.yiqivr.com/2015/01/%E4%BD%BF%E7%94%A8cardview%E5%AE%9E%E7%8E%B0%E5%8D%A1%E7%89%87%E5%BC%8Fgoogle-plus%E5%B8%83%E5%B1%80/)

<br>**V7 gridlayout 库**
　　这个库添加了对`GridLayout`类的支持，它是在`Android 4.0`中，新引入的一个布局控件。`GridLayout`可以用来做一个象`TableLayout`这样的布局样式，但其性能及功能都要比`Tablelayout`要好，比如`GridLayout`的布局中的单元格可以跨越多行，而`Tablelayout`则不行，此外，其渲染速度也比`Tablelayout`要快。它的具体用法此处就不在展开介绍了。
　　这个库放在：`<sdk>/extras/android/support/v7/gridlayout/`目录下。

效果参看：

- [《Android 4.0开发之GridLayOut布局实践》](http://tech.it168.com/a2011/1213/1287/000001287977.shtml)

<br>**V7 mediarouter 库**
　　这个库添加支持`MediaRouter`，`MediaRouteProvider`以及其他相关的媒体类。
　　当用户使用无线技术连接电视，家庭影院和音乐播放器时，他们希望`android apps`的内容可以在这些大屏幕的设备上播放。启用这种方式的播放，能把一台设备一个用户模式变为一个乐于分享的、令人激动和鼓舞的多用户模式。
　　android媒体路由（`mediarouter`）接口被设计成可以在二级设备上呈现和播放媒体。
　　这个库放在：`<sdk>/extras/android/support/v7/mediarouter/`目录下。注意：该库依赖于`v7 apcompat`库，因此你的项目要同时导入这两个库。

参考阅读：

- [《media and camera 框架之二： MediaRouter》](http://blog.csdn.net/go_strive/article/details/19973011)


<br>**V7 palette 库**
　　这个库添加了对`Palette`类的支持，它是`Android 5.0`推出的一个新特性。`Palette` 可以从一张图片中提取颜色，我们可以把提取的颜色融入到`App UI`中，可以使`UI`风格更加美观融洽。比如，我们可以从图片中提取颜色设置给`ActionBar`做背景颜色，这样`ActionBar`的颜色就会随着显示图片的变化而变化。
　　这个库放在：`<sdk>/extras/android/support/v7/palette/`目录下。

参考阅读：

- [《Android Lollipop 新特性 - Palette》](http://baoyz.com/android/2014/10/21/android-palette-use/)

<br>**V7 recyclerview 库**
　　这个库添加了对`RecyclerView`类的支持，它是`Android 5.0`推出的一个新`View`组件，用来取代`ListView`的。
　　`RecyclerView`的特性（但不限于）如下：

    -  支持设置横向和纵向显示Item。
    -  默认情况下，删除或者增加IteM的时候有动画，当然也可以自定义。

　　这个库放在：`<sdk>/extras/android/support/v7/recyclerview/`目录下。它的具体用法此处就不在展开介绍了。

## V8 支持库 ##
　　这个库设计用于与`Android 2.2`(`API level 8`)或更高的平台，它增加了对`RenderScript`计算框架的支持，这些`APIs`被包含在`android.support.v8.renderscript`包中。值得注意的是，包含这个支持库到你项目中的方法不同于其他支持库，更多信息请参看`RenderScript`开发手册。

## V13 支持库 ##
　　这个库设计用于与`Android 3.2`(`API level 13`)或更高的平台。它增加了对`FragmentCompat`类的支持，以及更多与`Fragment`相关的支持类。
　　这个库放在：`<sdk>/extras/android/support/v13/ `目录下。

<br>**本节参考阅读：**
- [Support Library](http://developer.android.com/tools/support-library/index.html)


## Multidex 支持库 ##
　　随着Android的不断发展，不论是官方还是`Github`上都出现了大量的新功能、新类库。如果你是一名幸运的Android应用开发者，正在开发一个前景广阔的应用，那么当你不断地加入新功能、添加新的类库，并达到一定规模时，就会遇到下面的构建错误，它表明你的应用已经达到了一个Android应用程序构建架构的限制。早期版本的构建系统报告这个错误如下：
``` c
Conversion to Dalvik format failed:
Unable to execute dex: method ID not in [0, 0xffff]: 65536
```

　　最近版本的Android构建系统会显示一个不同的错误信息，但它们是一个同样的问题:
``` c
trouble writing output:
Too many field references: 131000; max is 65536.
You may try using --multi-dex option.
```
<br>　　这两个错误条件显示一个共同的数字：`65536`。这个数字很重要，因为它代表了“方法的总数”。Android平台的Java虚拟机`Dalvik`在执行`DEX`格式的Java应用程序时，使用原生类型`short`来索引`DEX`文件中的方法。这意味着单个`DEX`文件可被引用的方法总数被限制为`65536`。通常`APK`包含一个`classes.dex`文件，因此Android应用的方法总数不能超过这个数量，这个数量是Android框架、第三方类库和你自己开发的代码的总和。如果你已经建立了一个Android应用，并且收到了这个错误，那么恭喜你，你的方法数已经超过了这个限制。

　　这个问题可以通过将一个`DEX`文件分拆成多个`DEX`文件解决。在`Facebook`、`Android Developers`博客等地方，介绍了通过自定义类加载过程的方法来解决此问题，但这些方法有些复杂而且并不优雅。

　　随着新的`MultiDex支持库`发布，`Google`正式为解决此问题提供了官方支持，[《Building Apps with Over 65K Methods》](http://developer.android.com/tools/building/multidex.html)介绍了如何使用`Gradle`构建多`DEX`应用。

<br>**在Android 5.0是个分水岭**

　　`Android 5.0`之前版本的平台使用的`Dalvik`执行应用程序代码。默认情况下，`Dalvik`限制每`APK`文件只能有一个`classes.dex`的字节码。为了绕过这个限制，您可以使用`Multidex支持库`，它允许你的`APK`中包含多个`dex`文件。

　　`Android 5.0`和`更高版本`使用`ART`来执行应用程序代码，它原生支持从`APK`文件加载多个`DEX`文件。在应用安装时，它会执行预编译，扫描`classes(..N).dex`文件然后将其编译成单个`.oat`文件用于执行。


<br>**仍需控制项目大小**

　　虽然`Google`解决了应用总方法数限制的问题，但并不意味着开发者可以任意扩大项目规模。`Multidex`仍有一些限制：

    1、DEX文件安装到设备的过程非常复杂，如果第二个DEX文件太大，可能导致应用无响应。
    2、由于Dalvik linearAlloc的Bug，应用可能无法在Android 4.0之前的版本启动，如果你的应用要支持这些版本就要多测试。
    3、同样因为Dalvik linearAlloc的限制，如果请求大量内存可能导致崩溃。Dalvik linearAlloc是一个固定大小的缓冲区。在应用的安装过程中，系统会运行一个名为dexopt的程序为该应用在当前机型中运行做准备。dexopt使用LinearAlloc来存储应用的方法信息。Android 2.2和2.3的缓冲区只有5MB，Android 4.x提高到了8MB或16MB。当方法数量过多导致超出缓冲区大小时，会造成dexopt崩溃。
    4、Multidex构建工具还不支持指定哪些类必须包含在首个DEX文件中，因此可能会导致某些类库（如某个类库需要从原生代码访问Java代码）无法使用。

<br>　　避免应用过大、方法过多仍然是Android开发者要注意的问题。`Mihai Parparita`的开源项目[ dex-method-counts ](https://github.com/mihaip/dex-method-counts)可以用于统计`APK`中每个包的方法数量。通常开发者自己的代码很难达到这样的方法数量限制，但随着第三方类库的加入，方法数就会迅速膨胀。因此选择合适的类库对Android开发者来说尤为重要。

　　开发者应该避免使用`Google Guava`这样的类库，它包含了`13000`多个方法。尽量使用专为移动应用设计的`Lite/Android`版本类库，或者使用小类库替换大类库，例如用`Google-gson`替换`Jackson JSON`。而对于`Google Protocol Buffers`这样的数据交换格式，其标准实现会自动生成大量的方法。采用`Square Wire`的实现则可以很好地解决此问题。


<br>**本节参考阅读：**
- [Android应用打破65K方法数限制](http://www.infoq.com/cn/news/2014/11/android-multidex)

# 第二节 ActionBar 和 Toolbar #

　　在`Android 3.0`中除了`Fragment`外，`ActionBar`也是一个重要的内容。`ActionBar`主要是用于代替传统的标题栏与Menu按钮：

    -  标题栏：使用ActionBar来做标题栏，可以展示更多丰富的内容，且方便操控。
    -  Menu按钮：
       -  在Android 3.0系统之前，Android手机有一个专门的Menu按钮用于打开设置选项。
       -  从Android 3.0系统开始，系统通过引进ActionBar移除了系统对于硬件Menu按钮的依赖，让用户在屏幕的标题栏上直接可以看到各种设置选项。当然，这不意味着OEM厂商们不会继续在手机上提供这个按钮，但从Google的角度来说，Menu按钮已经是过去时了。

## ActionBar ##

　　本节将以添加`V7 appcompat`库的方式来讲解如何使用`ActionBar`。

　　`ActionBar`有多种形式，你既可以在上面同时放置多个图标、按钮，也可以什么都不放。但对于大多数应用来说，`ActionBar`可以分割为`3`个不同的功能区域，下面是一张使用`ActionBar`的界面截图：

<center>
![ActionBar示意图](/img/android/android_b07_01.png)
</center>

　　其中，`[1]`是`ActionBar`的图标与标题，`[2]`是两个`action`按钮，`[3]`是`overflow`按钮（就是那三个点，点击后会打开一个列表）。
<br>　　提示：

    -  如果你使用的是Eclipse，并且搭建了最新的开发环境，那么在创建新项目的时候，会自动引入v7 appcompat库，如果没有自动引用，可以从<sdk>/extras/android/support/v7/appcompat/中复制一份。

### 图标和标题 ###
<br>　　下图展示的是一个新项目的`MainActivity`在`三星S5`手机（左）以及`Android2.2模拟器`（右）中的运行效果。

<center>
![图1](/img/android/android_b07_02.png)
</center>

<br>　　从上图中我们可以看到，当`ActionBar`运行在`Android3.0`以下的版本中时，标题栏的右上角不会出现`overflow`按钮，但是我们可以通过点击设备的`Menu`按钮来弹出`overflow`菜单。也就是说`Android3.0`中的`overflow`按钮就等价于`Android3.0`之前的`Menu`按钮。（不过，即便系统版本高于`Android3.0`也不一定保证会显示`overflow`按钮，具体后述）。
　　现在我们要实现如下图所示的一个效果：

<center>
![图2](/img/android/android_b07_03.png)
</center>

<br>　　由于`ActionBar`在不同的Android版本中显示的效果是不一样的，因此为了提供统一的视觉效果，我们接下来要修改一下`ActionBar`的背景图片。
　　范例1：修改背景图片。
``` java
public class MainActivity extends AppCompatActivity {
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 修改ActionBar的背景图片。
        getSupportActionBar().setBackgroundDrawable(getResources().getDrawable(R.drawable.bg_topbar));
    }
    // 以下省略其它代码。
}
```
    语句解释：
    -  getSupportActionBar()方法继承自android.support.v7.app.AppCompatActivity类。
    -  getSupportActionBar()方法返回一个ActionBar对象，我们可以通过这个对象来修改ActionBar的样式。
    -  ActionBar的setBackgroundDrawable()方法就是用来修改背景图片的。

<br>　　`ActionBar`支持两个文本标题，在上面的被称为主标题（`title`），在下面的被称为子标题（`subTitle`）。
　　范例2：修改标题文本。
``` java
public class MainActivity extends ActionBarActivity {
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 修改ActionBar的背景图片。
        getSupportActionBar().setBackgroundDrawable(getResources().getDrawable(R.drawable.bg_topbar));

        // 修改标题文字。
        getSupportActionBar().setTitle("店小二。");
        getSupportActionBar().setSubtitle("陌陌号：18204884");
    }
    // 以下省略其它代码。
}
```
    语句解释：
    -  调用setTitle()方法来设置标题文本。
    -  调用setSubtitle()方法来设置子标题文本。如果不设置子标题，则默认只显示主标题，且是垂直居中。
    -  调用getSupportActionBar().setDisplayShowTitleEnabled(false);可以同时隐藏两个标题。
    -  调用getSupportActionBar().setDisplayShowHomeEnabled(true);显示图标。
    -  调用getSupportActionBar().setIcon(R.drawable.ic_launcher);设置图标。

<br>　　仔细观察上面的`图1`可以发现，当程序运行在`三星S5`手机上时，标题的字体颜色为`白色`，而运行在`Android2.2模拟器`时，颜色则为`黒色`。这个情况肯定是不能忍的，需要给它们设置一个统一的颜色。
　　为了方便管理与更新，我们不会去直接修改`V7 appcompat`库里的属性，而是在自己项目里创建一个`res\values\custom_actionbar.xml`文件，并在该文件中创建一些与`V7 appcompat`库中同名的属性，即用我们的值覆盖掉它们的值。

　　范例3：修改字体颜色。
``` xml
<resources>
    <!-- 基本的主题 -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    </style>

    <!-- 定义主题，继承自@style/AppTheme。 -->
    <style name="MyActionBarTheme" parent="@style/AppTheme">
        <item name="actionBarStyle">@style/MyActionBar</item>
    </style>

    <!-- 定义ActionBar的样式 -->
    <style name="MyActionBar" parent="@style/Widget.AppCompat.ActionBar">
        <!-- 背景色 -->
        <item name="background">@android:color/black</item>
        <!-- 主标题的字体样式 -->
        <item name="titleTextStyle">@style/MyActionBarTitleText</item>
        <!-- 子标题的字体样式 -->
        <item name="subtitleTextStyle">@style/MyActionBarSubtitleText</item>
    </style>

    <style name="MyActionBarTitleText" parent="@style/Base.TextAppearance.AppCompat.Title">
        <item name="android:textColor">#5ec0e8</item>
        <item name="android:textSize">13sp</item>
    </style>
    <style name="MyActionBarSubtitleText" parent="@style/Base.TextAppearance.AppCompat.Subhead">
        <item name="android:textColor">#cccfff</item>
        <item name="android:textSize">8sp</item>
    </style>

</resources>
```
    语句解释：
    -  首先，定义一个名为MyActionBarTheme的主题，并为该主题指定了新的ActionBar样式。
    -  然后，又为ActionBar指定了背景色、主标题、子标题的样式。
    -  最后，就可以在清单文件中为某个Activity使用这个主题了。

### ActionButton ###
　　前面已经说了，在`Android3.0`之后菜单被移到了标题栏上，以便用户直接可以看到各种设置选项。
　　`ActionButton`相当于之前`Menu`菜单下的一个菜单项（`MenuItem`），它也包含一个`图标`和`文字`，同时它会直接显示在`ActionBar`上。当然，如果按钮过多导致`ActionBar`上显示不完，多出的一些按钮可以隐藏在`overflow`里面，点击一下`overflow`按钮就可以看到全部的`ActionButton`了。

　　现在我们要实现如下图所示的一个效果：

<center>
![](/img/android/android_b07_04.gif)
</center>


<br>　　范例1：创建菜单文件（`res\menu\main.xml`）。
``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto" >

    <item
        android:id="@+id/action_plus"
        android:icon="@drawable/actionbar_add_icon"
        android:title="@string/action_plus"
        app:showAsAction="always"/>
    <item
        android:id="@+id/action_album"
        android:icon="@drawable/ofm_photo_icon"
        android:title="我的相册"
        app:showAsAction="never"/>
    <item
        android:id="@+id/action_collection"
        android:icon="@drawable/ofm_collect_icon"
        android:title="我的收藏"
        app:showAsAction="never"/>
    <item
        android:id="@+id/action_card"
        android:icon="@drawable/ofm_card_icon"
        android:title="我的银行卡"
        app:showAsAction="never"/>
    <item
        android:id="@+id/action_settings"
        android:icon="@drawable/ofm_setting_icon"
        android:title="设置"
        app:showAsAction="never"/>
    <item
        android:id="@+id/action_feed"
        android:icon="@drawable/ofm_feedback_icon"
        android:title="意见反馈"
        app:showAsAction="never"/>

</menu>
```
    语句解释：
    -  各个Activity的Actionbar里包含的选项可能是不同的，因而每个Activity会有一个对应的menu.xml文件。
    -  在menu.xml中，每一个<item>标签都表示一个ActionButton。
    -  其中android:id、android:icon、android:title依次表示ActionButton的id、图标、文本标题。
    -  其中app:showAsAction属性用来设置ActionButton显示的位置，注意这个属性是在app命名空间里的。常用取值为：
       -  always：表示永远显示在ActionBar中，如果屏幕空间不够则无法显示。
       -  ifRoom：表示屏幕空间够的情况下显示在ActionBar中，不够的话就显示在overflow中。
       -  never：则表示永远显示在overflow中。

<br>　　范例2：将菜单添加到Activity的`ActionBar`中。
``` java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    // Inflate the menu; this adds items to the action bar if it is present.
    getMenuInflater().inflate(R.menu.main, menu); 
    return true;
}
```
    语句解释：
    -  在MainActivity中重写此方法即可，当Activity检测到它自己没有创建过菜单时，就会调用此方法，此方法只会调用一次。系统会根据方法的返回值做出相应的反应：
       -  返回true意味着菜单已初始化完毕，系统会在ActionBar中显示出overflow按钮。
       -  返回false意味着菜单没被初始化，此时ActionBar中则不会出现overflow按钮。
    -  MenuInflater类是一个用来将菜单文件加载并解析为一个Menu对象的工具类。调用Activity的getMenuInflater()方法可以获取一个MenuInflater对象。   

<br>　　范例3：响应菜单项的点击事件。
``` java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    // Handle action bar item clicks here. The action bar will
    // automatically handle clicks on the Home/Up button, so long
    // as you specify a parent activity in AndroidManifest.xml.
    int id = item.getItemId();
    switch(id){
    case R.id.action_album:
        Toast.makeText(this, "我的相册", Toast.LENGTH_SHORT).show();
        break;
    case R.id.action_plus:
        Toast.makeText(this, "加号被点击", Toast.LENGTH_SHORT).show();
        break;
    }
    return super.onOptionsItemSelected(item);
}
```
    语句解释：
    -  当用户选择了选项菜单中的一个菜单项（也包括ActionBar中的ActionButton），系统会调用Activity的onOptionsItemSelected()方法。这个方法把用户选择的菜单项作为参数来传递。你能够通过调用getItemId()方法来识别菜单项，这个方法返回了对象菜单项的唯一ID（这个ID是在菜单资源的android:id属性中定义的，或者是传递给add方法的一个整数）。你能够把这个ID与已知的菜单项匹配，让它执行对应的动作。
    -  在MainActivity中重写此方法即可。

<br>　　前面说了，即使手机系统版本高于`Android3.0`，也不保证在`ActionBar`中一定会显示出`overflow`按钮。
　　[《Android ActionBar完全解析，使用官方推荐的最佳导航栏(上)》](http://blog.csdn.net/guolin_blog/article/details/18234477) 中解释了，即`overflow`按钮的显示情况和手机的硬件情况是有关系的，如果手机没有物理`Menu`键的话，`overflow`按钮就可以显示，如果有物理`Menu`键的话（比如Android模拟器都有物理`Menu`键），`overflow`按钮就不会显示出来。

<br>　　范例4：显示出`overflow`按钮。
``` java
private void setOverflowShowingAlways() {
    try {
        ViewConfiguration config = ViewConfiguration.get(this);
        Field menuKeyField = ViewConfiguration.class.getDeclaredField("sHasPermanentMenuKey");
        menuKeyField.setAccessible(true);
        menuKeyField.setBoolean(config, false);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
    语句解释：
    -  在Activity的onCreate()方法的最后一行调用setOverflowShowingAlways()方法即可。
    -  在ViewConfiguration这个类中有一个叫做sHasPermanentMenuKey的静态变量，系统就是根据这个变量的值来判断手机有没有物理Menu键的。本范例就是使用反射的方式将sHasPermanentMenuKey的值设置成false。


<br>　　如果你此时运行程序，然后点击`overflow`按钮，你会发现里面的`ActionButton`都是只显示文字不显示图标的。这是官方的默认效果，但我们可以通过反射来改变这一默认行为。

<br>　　范例5：让菜单项显示图标。
``` java
@Override
public boolean onMenuOpened(int featureId, Menu menu) {
    if (featureId == Window.FEATURE_ACTION_BAR && menu != null) {
        if (menu.getClass().getSimpleName().equals("MenuBuilder")) {
            try {
                Method m = menu.getClass().getDeclaredMethod("setOptionalIconsVisible", Boolean.TYPE);
                m.setAccessible(true);
                m.invoke(menu, true);
            } catch (Exception e) {}
        }
    }
    return super.onMenuOpened(featureId, menu);
}
```
    语句解释：
    -  直接在Activity中重写onMenuOpened()方法即可。
    -  由MenuBuilder这个类的setOptionalIconsVisible()方法来决定overflow中的ActionButton应不应该显示图标，如果我们在overflow被展开的时候给这个方法传入true，那么里面的每一个ActionButton对应的图标就都会显示出来了。
    
<br>　　最后，来我们就要修改一下`overflow`菜单以及其内的各个`ActionButtom`的样式了，比如字体颜色等。

<br>　　范例6：修改样式。
``` xml
<resources xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- overflow弹出菜单的样式 -->
    <style name="Base.Widget.AppCompat.ListPopupWindow" parent="">
        <!-- 菜单默认弹出的位置是0，此处将它在垂直方向的偏移量设置为52dp，即让弹出菜单向下移动一些位置 -->
        <item name="android:dropDownVerticalOffset">52dp</item>
        <!-- 弹出菜单的背景图片 -->
        <item name="android:popupBackground">@drawable/bg_dropmenu_topbar</item>
    </style>

    <!-- overflow弹出菜单内部的样式 -->
    <style name="Base.Widget.AppCompat.ListView.DropDown" parent="android:Widget.ListView">
        <!-- overflow弹出菜单内部，每个Item之间的分割线 -->
        <item name="android:divider">@drawable/ic_divider</item>
        <!-- overflow弹出菜单内部，每个Item的selector -->
        <item name="android:listSelector">@drawable/actionbar_item_selector</item>
    </style>

     <!-- overflow出菜单内部的每个Item的字体颜色与大小 -->
     <style name="Base.TextAppearance.AppCompat.Menu">
        <item name="android:textSize">16sp</item>
        <item name="android:textColor">@android:color/black</item>
    </style>

</resources>
```

<br>　　范例7：现在我们要实现如下图所示的一个效果：

<center>
![](/img/android/android_b07_05.png)
</center>

<br>　　上图中添加了两个新修改，一个是标题栏左边的`“←”`，另一个是`overflow`按钮左边是一个文本的`ActionButton`。

    -  前者，调用getSupportActionBar().setDisplayHomeAsUpEnabled(true);即可。
       -  当它被点击时同样会调用onOptionsItemSelected()方法，菜单项id为android.R.id.home。
    -  后者，在main.xml文件中，只为<item>标签指定android:title属性，而不指定android:icon属性即可。

　　下面是文本`ActionButton`相关的样式：
``` xml
// 字体的大小、背景selector
<style name="Base.Widget.AppCompat.ActionButton" parent="">
    <item name="android:textSize">14sp</item>
    <item name="android:background">@drawable/actionbar_text_actionbtn_selector</item>
</style>
```

### 自定义布局 ###
　　`ActionBar`为用户提供了统一方式来展示操作和导航，但是这并不意味着你的`app`要看起来和其他`app`一样，我们可以自定义自己的布局。  
　　将`ActionBar`划分为如下所示的4个区域：

<center>
![](/img/android/android_b07_06.png)
</center>

　　其中：

    -  1表示返回按钮和图标。
    -  2表示标题
    -  3表示ActionButton、ActionProvider、ActionView。
    -  4表示Overflow按钮。

<br>　　我们可以通过调用如下代码来自定义`ActionBar`的布局：
``` java
getSupportActionBar().setDisplayShowCustomEnabled(true);
getSupportActionBar().setCustomView(R.layout.common_title);
```
    语句解释：
    -  自定义的布局将显示在上图中“2”的位置，如果你把其他三个位置都给隐藏掉，那么我们自定义的布局将占据整个ActionBar的空间。

<br>　　`ActionBar`的高级用法（`ActionView`、`ActionProvider`），笔者不打算继续介绍了，下面的参考阅读中都有。

<br>**本节参考阅读：**
- [Android ActionBar使用介绍](http://blog.csdn.net/wangjinyu501/article/details/9360801)
- [Android ActionBar完全解析，使用官方推荐的最佳导航栏(上)](http://blog.csdn.net/guolin_blog/article/details/18234477)
- [Android ActionBar完全解析，使用官方推荐的最佳导航栏(下)](http://blog.csdn.net/guolin_blog/article/details/25466665)
- [How to center align the ActionBar title in Android?](http://stackoverflow.com/questions/12387345/how-to-center-align-the-actionbar-title-in-android)
- [Android-Action Bar使用方法-活动栏(一)](http://www.oschina.net/question/54100_34400)

## Toolbar ##
　　`Toolbar`是`Android 5.0`中新引入的一个控件，其出现的目的就是为了取代`ActionBar`。
<br>　　**为什么要替换ActionBar呢？**

　　因为ActionBar是属于应用UI的一部分，但是我们却又不能对其完全控制：

    -  ActionBar是由系统创建并对其进行相关参数的初始化。
    -  ActionBar限制多、定制困难（比如标题居中、修改字体样式等）。

　　基于这两点（但不限于），Google在`Android 5.0`后推出了`Toolbar`。

<br>　　这里先说一下，Toolbar所带来的自由性与其本身的定位密不可分，因为它是一个`ViewGroup`：
``` java
public class Toolbar extends ViewGroup {
    // 此处省略了Toolbar内的代码
}
```

<br>　　既然Toolbar是用来替代ActionBar的，那么就意味着之前ActionBar能实现的功能，Toolbar也能实现，下面就来介绍它。

<br>　　范例1：添加Gradle依赖。
``` gradle
dependencies {
    compile 'com.android.support:appcompat-v7:23.1.1'
}
```
    语句解释：
    -  由于Toolbar被放到了appcompat-v7库中，所以我们需要先引用该库。

<br>　　回想一下，我们以前使用ActionBar时，都会让项目的`AppTheme`去继承ActionBar的主题，比如：
``` xml
<!-- Base application theme. -->
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    
</style>
```
　　接着，会再让Application去使用这个主题，这样项目里的所有Activity里就会显示出ActionBar了：
``` xml
<application
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:theme="@style/AppTheme" >
</application>
```

<br>　　不过，如果想用Toolbar来替代ActionBar，则就需要修改`AppTheme`主题了：
``` xml
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">

</style>
```
    语句解释：
    -  这么做是为了不让Toolbar和ActionBar的样式起冲突。
    -  当然也可以不修改AppThem，而是直接在清单文件的activity标签上修改。

<br>　　范例2：在布局文件中创建Toolbar。
``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:id="@+id/mToolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</RelativeLayout>
```
    语句解释：
    -  因为Toolbar是一个ViewGroup，所以可以直接在布局文件中使用它。
    -  此时的Toolbar可能没有背景色，后面会介绍如何给它设置。

<br>　　范例3：Activity的代码。
``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Toolbar toolbar = (Toolbar) findViewById(R.id.mToolbar);
        // 将toolbar做为ActionBar设置到Activity中。
        setSupportActionBar(toolbar);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }
}
```
    语句解释：
    -  注意，这里继承的是AppCompatActivity类，第10行调用的setSupportActionBar方法也是继承自该类。
    -  另外，onCreateOptionsMenu方法返回的菜单同样会被放到Toolbar中。

<br>　　范例4：修改标题栏。

<center>
![Android2.2模拟器上的运行效果图](/img/android/android_b03_01.png)
</center>

``` java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    Toolbar toolbar = (Toolbar) findViewById(R.id.mToolbar);
    setSupportActionBar(toolbar);
    
    toolbar.setTitle("店小二");
    toolbar.setSubtitle("陌陌号：18204884");
    // 设置一个不可点击图标。
    toolbar.setLogo(R.mipmap.ic_launcher);
    // 设置一个可以点击的图片，即上图最左侧的那个“三条横线”的图。
    toolbar.setNavigationIcon(R.drawable.ic_menu);
    toolbar.setNavigationOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            Toast.makeText(MainActivity.this, "AAA", Toast.LENGTH_SHORT).show();
        }
    });
}
```
    语句解释：
    -  有两点需要注意：
       -  第一，要先调用setSupportActionBar，然后再调用setNavigationOnClickListener才会有效果。
       -  第二，如果执行本代码时，发现标题并没有变成“店小二”，则有两个方法可以解决：
          -  1、将第8行代码放到第6行之前。
          -  2、将第8行代码改成getSupportActionBar().setTitle()。
          -  这是因为当我们把toolbar设置到Activity上之后，调用toolbar的某些方法进行修改，是无法反映到Activity中的。

<br>　　范例5：修改字体颜色。

<center>
![属性对应图](/img/android/android_b03_02.png)
</center>

``` html
<style name="AppTheme" parent="Theme.AppCompat.NoActionBar">
    <!--导航栏背景色-->
    <item name="colorPrimary">#2196F3</item>
    <!--状态栏背景色-->
    <item name="colorPrimaryDark">#1976D2</item>
    <!--导航栏上的主标题颜色-->
    <item name="android:textColorPrimary">@android:color/white</item>
    <!--导航栏上的子标题颜色-->
    <item name="android:textColorSecondary">@android:color/white</item>
    <!--Activity的背景色-->
    <item name="android:windowBackground">@android:color/white</item>
</style>
```
    语句解释：
    -  上面设置的色值会作用于整个项目，以textColorPrimary和textColorSecondary为例：
       -  为它们设置颜色之后，不止会影响Toolbar的标题的颜色，还会影响到其他多个地方的字体颜色。
       -  因此笔者不推荐通过修改它们二者来达到修改标题栏字体的目的。
       -  而应该使用Toolbar提供的setTitleTextColor和setSubtitleTextColor方法去修改。
    -  windowBackground会影响使用此主题的所有Activity（但不限于Activity）的背景色，使用时也要注意。



<br>　　由于Toolbar自带的设置会覆盖上面的`colorPrimary`，所以还需要修改一下它：
``` xml
<android.support.v7.widget.Toolbar
    android:id="@+id/mToolbar"
    android:background="?colorPrimary"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
　　其中`?colorPrimary`表示引用当前主题下的`colorPrimary`属性的值，也可以写成`?attr/colorPrimary`。

<br>　　范例6：修改overflow菜单样式。
``` xml
<style name="PopupMenu" parent="ThemeOverlay.AppCompat.Light" >
    <!--菜单背景色-->
    <item name="android:colorBackground">#0000ff</item>
    <item name="android:textColor">#ffffff</item>
    <item name="android:textSize">10sp</item>
</style>
```
　　然后在布局文件中设置：
``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:id="@+id/mToolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary"
        app:popupTheme="@style/PopupMenu" />

</RelativeLayout>
```
    语句解释：
    -  使用popupTheme属性设置菜单的样式。

<br>　　范例7：自定义Toolbar。
``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:id="@+id/mToolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary"
        app:popupTheme="@style/PopupMenu">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:text="Title"
            android:textColor="@android:color/white"
            android:textSize="22sp" />
    </android.support.v7.widget.Toolbar>
</RelativeLayout>
```
    语句解释：
    -  由于Toolbar是一个ViewGroup，因此直接在Toolbar标签里添加控件即可。
    -  本范例用于创建一个标题居中的Toolbar，在执行之前还需要在Activity里把原有的标题给隐藏了：
       -  getSupportActionBar().setDisplayShowTitleEnabled(false);

<br><br>　　上面只是列举了Toolbar的基本用法，更高级的应用请阅读：[《实战篇　第三章 开源库》](http://cutler.github.io/android-O03/)。


<br>**本节参考阅读：**
- [Toolbar：上位的小三](http://blog.csdn.net/aigestudio/article/details/47090167)
- [【Android】Toolbar](http://my.oschina.net/xesam/blog/356855)
- [Android Self study course (manterial design)—Toolbar(2)](http://www.codes9.com/mobile-development/android/android-self-study-course-manterial-design-toolbar2-3/)


# 第二节 DrawerLayout #
　　相信大家都听说过`DrawerLayout`控件（如果未听过那请去搜索），它是`v4`库中提供的一个侧滑菜单控件。

　　本节将介绍如何让`DrawerLayout`和`Toolbar`配合使用。

<br>　　范例1：添加Gradle依赖。
``` gradle
dependencies {
    compile 'com.android.support:appcompat-v7:23.1.1'
}
```
    语句解释：
    -  由于Toolbar被放到了appcompat-v7库中，所以我们需要先引用该库。
    -  由于appcompat-v7库需要依赖v4库，所以AS也会默认把v4导入进项目中，因此上面只需要加一个v7引用即可。

<br>　　本质上看，`DrawerLayout`的作用就是，为我们的界面加上左右两个滑动菜单，因此它由三个部分组成：

    -  主布局
    -  左侧滑动菜单
    -  右侧滑动菜单。

<br>　　范例2：布局文件。
``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <android.support.v7.widget.Toolbar
        android:id="@+id/mToolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary" />

    <android.support.v4.widget.DrawerLayout
        android:id="@+id/drawer"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:id="@+id/content"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="onClick"
                android:text="中间的按钮" />
        </LinearLayout>

        <LinearLayout
            android:id="@+id/left"
            android:layout_width="220dp"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            android:background="@android:color/white"
            android:orientation="vertical">
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="左侧的按钮" />
        </LinearLayout>

        <LinearLayout
            android:id="@+id/right"
            android:layout_width="220dp"
            android:layout_height="match_parent"
            android:layout_gravity="end"
            android:background="@android:color/white"
            android:orientation="vertical">
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="右侧的按钮" />
        </LinearLayout>

    </android.support.v4.widget.DrawerLayout>
</LinearLayout>
```
    语句解释：
    -  DrawerLayout标签内部最多支持3个子View（但也可以不设置第三个子View）：
      -  第一个子View表示界面的主布局，会一直显示出来。
      -  第二个子View表示界面左侧的菜单，需要滑动才能显示出来。
      -  第三个子View表示界面右侧的菜单，需要滑动才能显示出来。
    -  我们把第二、第三个子View称为抽屉View，抽屉View必须要使用android:layout_gravity属性来指定滑动方向：
       -  start表示当前抽屉View显示在左侧。
       -  end表示当前抽屉View显示在右侧。
    -  抽屉View的宽度最好不要超过320dp，这样可以让用户总能看到内容视图的一部分。


<br>　　范例3：Activity代码。
``` java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout drawer;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 设置Toolbar的标题。
        Toolbar toolbar = (Toolbar) findViewById(R.id.mToolbar);
        toolbar.setTitle("绳命是乳次的灰黄");
        setSupportActionBar(toolbar);

        drawer = (DrawerLayout) findViewById(R.id.drawer);
        // ActionBarDrawerToggle用来监听DrawerLayout的打开与关闭状态。
        ActionBarDrawerToggle mDrawerToggle = new ActionBarDrawerToggle(this, 
            drawer, toolbar, R.string.app_name, R.string.app_name);
        // 设置让ActionBarDrawerToggle的开关状态随着DrawerLayout的开关状态而改变。
        mDrawerToggle.syncState();
        // 将监听器设置到DrawerLayout中。
        drawer.setDrawerListener(mDrawerToggle);
    }

    public void onClick(View view){
        // 通过代码的方式打开左侧的滑动菜单。
        drawer.openDrawer(Gravity.LEFT);
    }

    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        if (id == R.id.action_settings) {
            return true;
        }
        return super.onOptionsItemSelected(item);
    }
}
```
    语句解释：
    -  需要注意的是，本范例使用的ActionBarDrawerToggle是v7包中的那个。

<br>　　此时此刻任何语言都是苍白的，点击[ 下载源码 ](http://download.csdn.net/detail/github_28554183/9439065)（里面也包含apk），安装运行之后，你就能知道什么效果了。

<center>
![运行效果图](/img/android/android_o03_01.jpg)
</center>

<br>　　上图左上角的那个箭头是带动画的，当抽屉View展开时显示一个箭头，抽屉View关闭是显示三条线。
<br>　　最后，若想让`DrawerLayout`在展开时将`Toolbar`给盖住，只需要将布局文件里的`Toolbar`移动到`DrawerLayout`中即可。

# 第三节 Android 5.0 #
　　`Android 5.0`（Lollipop）是`Google`于`2014/10/15`日（美国太平洋时间）发布的全新`Android`操作系统。
　　它是迄今为止规模最大、最为雄心勃勃的`Android`版本，它不仅为用户推出了各种崭新的新功能，为开发者则提供了数千个新的API，还将`Android`的疆土扩展得更远，小到手机、平板电脑和穿戴式设备，大到电视和汽车，都可以是它活跃的领地。

　　要深入了解面向开发者的新`API`，请参阅[ Android 5.0 API ](http://developer.android.com/intl/zh-cn/about/versions/android-5.0.html)概述，本章只会介绍一些`Android5.0`中已经被广泛使用的技术。

　　`Android 5.0`将`Material design`设计引入`Android`系统，您可以借助`Material design`设计创建应用，使其呈现动态的视觉效果并为用户提供自然的界面元素过渡效果。此支持包括：

    -  素材主题背景
    -  视图阴影
    -  RecyclerView 小部件
    -  可绘制的动画和样式效果
    -  Material Design 设计动画和活动过渡效果
    -  基于视图状态的视图属性动画生成器
    -  可自定义的界面小部件和应用栏（含您可以控制的调色板）
    -  基于 XML 矢量图形的动画和非动画图形内容

　　要详细了解如何向您的应用添加`Material Design`设计功能，请参阅[ Material Design ](http://developer.android.com/intl/zh-cn/training/material/index.html)设计。

　　上面这些文字都是从百度、`Google`官方文档上复制过来的，说的很抽象，不过没关系，我们接下来将分多个小结依次介绍`Android5.0`在界面上新增的内容。

## CardView ##
　　`Android5.0`中增加了一个全新的控件`CardView`，它是`FrameLayout`类的子类，并在`FrameLayout`基础上添加了`圆角`和`阴影`效果。
　　也就是说，从此以后我们可以方便的通过代码来实现`圆角`和`阴影`的效果，而不再像以前那样依赖于美工做图了（而且给的图不易进行微调）。

<br>　　`Google`已经将`CardView`放到一个名为[ v7 cardview library ](http://developer.android.com/intl/zh-cn/tools/support-library/features.html#v7-cardview)的支持库中了，我们可以在`Android2.1`之上的任何版本中使用它。在项目中添加对该库的依赖：
``` gradle
dependencies {
    compile 'com.android.support:cardview-v7:21.0.0'
}
```

<br>　　`CardView`的使用方法十分简单，添加完项目依赖后，直接修改咱们的布局文件即可。

<br>　　范例1：`activity_main.xml`。

<center>
![运行效果](/img/android/android_o06_01.png)
</center>

``` xml
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="20dp"
    card_view:cardCornerRadius="5dp"
    card_view:cardElevation="2dp">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:text="HELLO" />

</android.support.v7.widget.CardView>
```
    语句解释：
    -  由于CardView的父类是FrameLayout，因而它的使用方法和FrameLayout是一样的。
    -  本范例中定义了android和card_view两个命名空间，CardView所提供的属性都放到了card_view中，也是不多说。
    -  属性解释：
       -  cardCornerRadius：设置CardView的圆角半径，值越大圆角就越明显。
       -  cardElevation：设置CardView的阴影高度，值越大阴影就越明显。
       -  cardBackgroundColor：设置CardView的背景色。
       -  contentPadding、contentPaddingLeft、contentPaddingRight、contentPaddingTop、contentPaddingBottom：你懂的。

<br>　　范例2：显示层级。

<center>
![运行在Android5.0设备上的效果](/img/android/android_o06_02.png)
</center>

``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.CardView
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="20dp"
        card_view:cardBackgroundColor="#ff0000"
        card_view:cardCornerRadius="10dp"
        card_view:cardElevation="3dp" />

    <android.support.v7.widget.CardView
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="50dp"
        card_view:cardBackgroundColor="#00ff00"
        card_view:cardCornerRadius="10dp"
        card_view:cardElevation="2dp" />

</RelativeLayout>
```
    语句解释：
    -  Android5.0中View在绘制的时候增加一个z轴的概念（即显示层级），层级高的View将显示在层级低的View的上面。
    -  CardView的cardElevation属性既是用来设置阴影高度，也是用来设置显示层级的，按照以往的情况，由于绿色的CardView是后添加的，因而它会被放到上面显示，但由于它的cardElevation值低，因此最终它被放到了下面去显示。
      -  注意，显示层级是Android5.0提出的，也就是说这段代码只有运行在Android5.0的设备上时，红色的才会盖在绿色上面，否则仍然是绿色在上。

## RecyclerView ##
　　`Android5.0`中增加了另一个重要的控件就是`RecyclerView`，它用来取代现有的`ListView`和`GridView`控件。

<br>**问题是这样的**

　　在开发中，我们都或多或少的会对`ListView`的性能有所要求，以便给用户提供流畅的滑动体验，常见的优化方案有如下几个：

    -  第一，使用convertView来重用现有的Item。
    -  第二，使用ViewHolder来减少findViewById方法的调用次数。
    -  第三，尽可能的降低getView方法内对象产生的数量以及大小。如果Item中包含图片那么应该进行异步加载。

<br>　　而且除了效率上的要求，我们还希望`ListView`能实现：

    -  第一，能实横向、纵向、九宫格、瀑布流等各种布局那就太好了。
    -  第二，能在添加和删除每个Item的时候，都播放一个动画那就太好了。

<br>　　现实的情况是，不论是效率还是功能，`ListView`都不是最优的，因而`Google`提出了`RecyclerView`。

<br>**是什么、能做什么？**
　　从类名上看，`RecyclerView`代表的意义是只管`回收和复用View`，其他的功能由你自己去完成，给予你充分的定制自由，实现了高度的解耦。在结构上`RecyclerView`与`ListView`的区别在于：

    -  ListView：
       -  数据由Adapter提供。
       -  Item的创建/重用由ListView来完成。
       -  Item的排列方式（只支持垂直）由ListView来控制。
    -  RecyclerView：
       -  数据由Adapter提供。
       -  Item的创建/重用由RecyclerView来完成。
       -  Item的排列方式（垂直、水平、瀑布流等任意方式）由LayoutManager来完成。如果你想自定义自己的排列方式，那么只需要自定义一个LayoutManager类即可。

<br>　　`RecyclerView`在提升效率方面上做了如下操作（但不限于）：

    -  第一，强制使用ViewHolder。
       -  Android推荐（非强制）LisView使用ViewHolder来减少findViewById()的使用以提高效率。
       -  在RecyclerView中变成了必须使用的模式，Adapter要求返回的值也从普通的View变成了ViewHolder。
    -  第二，使用局部更新。
       -  当LisView的数据改变时会更新整个列表，而RecyclerView既支持整体刷新也支持局部刷新（只会更新发生变化的View）。

　　同时，`RecyclerView`在功能方面上已经实现了刚才我们提到的两点：自定义`Item`排列方式和`Item`动画。
<br>　　提示：如果你想阅读各个`support`库的源码，请点击[ platform_frameworks_support ](https://github.com/android/platform_frameworks_support)。

<br>**开始使用**
　　同样的`Google`也已经将`RecyclerView`放到一个名为[ v7 recyclerview library ](http://developer.android.com/intl/zh-cn/tools/support-library/features.html#v7-recyclerview)的支持库中了，我们可以在`Android2.1`之上的任何版本中使用它。在项目中添加对该库的依赖：
``` gradle
dependencies {
    compile 'com.android.support:recyclerview-v7:21.0.0'
}
```

<br>　　范例1：`activity_main.xml`。
``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/my_recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</RelativeLayout>
```

<br>　　`RecyclerView`与`AdapterView`类似，实现了数据和视图的分离，数据仍然是使用`Adapter`来提供。`RecyclerView`的使用步骤为：

    -  第一，获取RecyclerView对象。
    -  第二，为RecyclerView设置Item的排列方式。
    -  第三，为RecyclerView设置Adapter。

<br>　　范例2：`MainActivity`。
``` java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 第一，获取RecyclerView对象。
        RecyclerView mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

        // 第二，为RecyclerView设置Item的排列方式。下面的代码使用线性布局排列子元素，垂直排列。
        LinearLayoutManager mLayoutManager = new LinearLayoutManager(this);
        mLayoutManager.setOrientation(LinearLayoutManager.VERTICAL);
        mRecyclerView.setLayoutManager(mLayoutManager);

        // 第三，为RecyclerView设置Adapter。
        List<String> data = Arrays.asList("Android","ios","jack","tony","window","mac");
        mRecyclerView.setAdapter(new MyRecyclerAdapter(data));
    }
}
```
    语句解释：
    -  系统内置的Item布局方式还有网格（GridView）和瀑布流两种，稍后会介绍它们。

<br>　　范例3：`MyRecyclerAdapter`。
``` java
// 注意：MyRecyclerAdapter的父类是RecyclerView.Adapter类型的。
public class MyRecyclerAdapter extends RecyclerView.Adapter<MyRecyclerAdapter.ViewHolder> {
    private List<String> items;

    public MyRecyclerAdapter(List<String> items) {
        this.items = items;
    }

    // 此方法相当于ListView的getView()方法，当需要返回控件时会调用它。
    // 注意此方法的返回值被强制要求返回RecyclerView.ViewHolder类型的。
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_recyclerview, parent, false);
        return new ViewHolder(v);
    }

    // 当需要更新holder所对应的Item中的信息时，会调用此方法。
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        String item = items.get(position);
        holder.text.setText(item);
        // 下面这行代码为了以后能方便通过holder.itemView来获取到这个item对象，下面会介绍holder.itemView是什么。
        holder.itemView.setTag(item);
    }

    // 返回适配器中的元素个数。
    @Override
    public int getItemCount() {
        return items.size();
    }

    // 自定义ViewHolder类，注意此类我们用static修饰，可以防止ViewHolder持有Adapter的引用。
    public static class ViewHolder extends RecyclerView.ViewHolder {
        // 和以前一样，ViewHolder将它所管理的每一个控件都作为一个字段，方便外界访问。
        public TextView text;

        public ViewHolder(View itemView) {
            // 调用父类的构造方法，在父类中有一个名为itemView字段，专门用来持有参数itemView的引用的，因为父类在后续操作中会用到。
            // 而itemView的子控件的引用，则由当前ViewHolder类的属性来持有，具体请参阅源码。
            super(itemView);
            // 从View中获取各个子控件的引用，以减少findViewById方法的调用次数。
            text = (TextView) itemView.findViewById(R.id.text);
        }
    }
}
```
    语句解释：
    -  如果你没理解holder.itemView的含义，请自行去阅读源码。

<br>　　范例4：`item_recyclerview.xml`。
``` xml
<android.support.v7.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginRight="10dp"
    card_view:contentPadding="10dp"
    card_view:cardCornerRadius="4dp"
    card_view:cardElevation="3dp">

    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</android.support.v7.widget.CardView>
```
    语句解释：
    -  使用了CardView作为根节点，其内部只有一个TextView，用来显示一行文字。

<center>
![运行效果](/img/android/android_o06_03.png)
</center>

<br>**设置点击事件**
　　`RecyclerView`没有提供设置点击事件的方法，我们可以直接在`ViewHolder`类上添加事件。

<br>　　范例1：点击和长按事件。
``` java
public static class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener, View.OnLongClickListener {
    public TextView text;

    public ViewHolder(View itemView) {
        super(itemView);
        text = (TextView) itemView.findViewById(R.id.text);
        text.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                Toast.makeText(v.getContext(),"text click", Toast.LENGTH_SHORT).show();
            }
        });
        // 给根View设置点击和长按事件。
        itemView.setOnClickListener(this);
        itemView.setOnLongClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Toast.makeText(v.getContext(),"itemView click", Toast.LENGTH_SHORT).show();
    }

    @Override
    public boolean onLongClick(View v) {
        Toast.makeText(v.getContext(),"itemView long click", Toast.LENGTH_SHORT).show();
        return true;
    }
}
```
    语句解释：
    -  直接在构造方法内部给itemVIew设置事件监听器即可。

<br>**Item排列方式**

<br>　　范例1：网格、瀑布流。
``` java
// 线性布局排列
mRecyclerView.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false));

// 网格布局排列
mRecyclerView.setLayoutManager(new GridLayoutManager(this, 2, GridLayoutManager.VERTICAL, false));

// 交错网格布局，类似于网格布局，但每个格子的高度或者长度可以不一样。
mRecyclerView.setLayoutManager(new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL));
```
    语句解释：
    -  LinearLayoutManager构造方法的三个参数：
       -  context：也是不多说。
       -  orientation：元素排列方向，取值有LinearLayoutManager.VERTICAL和LinearLayoutManager.HORIZONTAL。
       -  reverseLayout：是否反转布局。即元素将从下到上显示，效果参看微信、QQ等软件的聊天界面。
    -  GridLayoutManager构造方法的第二个参数：
       -  spanCount：设置每一排上元素的个数。
    -  StaggeredGridLayoutManager构造方法的第一个参数也是spanCount，与GridLayoutManager一样。


<br>　　下图展示了`GridLayoutManager`（左）和`StaggeredGridLayoutManager`（右）排列方式的区别：
<center>
![运行效果](/img/android/android_o06_04.png)
</center>

<br>**数据更新**
　　`RecyclerView`提供了两种数据更新的方式：

    -  全部更新：让整个RecyclerView所有正在显示的控件都更新。
       -  调用RecyclerView.Adapter类的notifyDataSetChanged()方法即可。
    -  局部更新：如果你往Adapter中“增加、删除、更新”了一个或多个Item，那么就可以调用对应的方法来只刷新这几个Item，从而提升效率。
    -  添加：notifyItemInserted(position)、notifyItemRangeInserted(position, itemCount)。
       -  前者告诉RecyclerView，当前往position位置上插入了1个新元素。
       -  后者是从position位置开始，加入了itemCount个元素。
    -  删除：notifyItemRemoved(position)、notifyItemRangeRemoved(position, itemCount)。
    -  更新：notifyItemChanged(position)、notifyItemChanged(position, itemCount)。

<br>　　范例1：局部更新。
``` java
public class MyRecyclerAdapter extends RecyclerView.Adapter<MyRecyclerAdapter.ViewHolder> {
    private List<String> items;

    public MyRecyclerAdapter(List<String> items) {
        this.items = items;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_recyclerview, parent, false);
        // 注意此处把MyRecyclerAdapter的引用传递了过去。
        return new ViewHolder(v, this);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        String item = items.get(position);
        holder.text.setText(item);
    }

    @Override
    public int getItemCount() {
        return items.size();
    }

    // 添加
    public void appendData(List<String> newItems){
        if (newItems != null && newItems.size() > 0) {
            items.addAll(newItems);
            notifyItemRangeInserted(items.size()-newItems.size(), newItems.size());
        }
    }

    public void removeData(int position) {
        if (position >= 0 && position < items.size()) {
            items.remove(position);
            notifyItemRemoved(position);
        }
    }

    public static class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
        // 弱引用
        private SoftReference<MyRecyclerAdapter> mAdapter;

        public TextView text;

        public ViewHolder(View itemView, MyRecyclerAdapter adapter) {
            super(itemView);
            mAdapter = new SoftReference<MyRecyclerAdapter>(adapter);
            text = (TextView) itemView.findViewById(R.id.text);
            itemView.setOnClickListener(this);
        }

        public void onClick(View v) {
            if (mAdapter != null && mAdapter.get() != null) {
                // 调用RecyclerView.ViewHolder类的getPosition()获取当前位置
                mAdapter.get().removeData(getPosition());
            }
        }
    }
}
```
    语句解释：
    -  如果一个对象被两个地方引用，一个是弱引用，一个是强引用，那么在强引用被释放之前，弱引用会始终有值。


<br>**下拉刷新**
　　我们也可以很容易的给`RecyclerView`加上下拉刷新功能。

<br>　　范例1：`SwipeRefreshLayout`。
``` xml
<android.support.v4.widget.SwipeRefreshLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/refreshLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/my_recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</android.support.v4.widget.SwipeRefreshLayout>
```
    语句解释：
    -  使用v4包中的SwipeRefreshLayout类将需要下拉刷新的控件包裹一下就可以了。


<br>　　范例2：设置下拉事件监听器。
``` java
final SwipeRefreshLayout mRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.refreshLayout);
// 调用此方法设置下拉监听器，当用户触发下拉刷新时，会在主线程中回调onRefresh()方法。
mRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
        // 1.5秒后将下拉刷新的状态置为false。
        new Handler().postDelayed(new Runnable() {
            public void run() {
                mRefreshLayout.setRefreshing(false);
            }
        }, 1500);
    }
});
```
    语句解释：
    -  你也可以直接调用setRefreshing(true);来触发下拉刷新的动画，但是不会导致onRefresh方法被调用。
    -  下拉刷新的箭头颜色等都是可以修改的。


<br>　　[点击下载源码 ](http://download.csdn.net/detail/github_28554183/8920585)

　　如果你没有`AndroidStudio`环境，可以在`MateriaDesign\app\build\outputs\apk`目录中找到`apk`文件。

<br>**其它特性**
　　`RecyclerView`还支持`Item分割线`、`多种Item类型`等功能，由于篇幅关系（同时也很容易在网上搜索到答案），笔者暂时就不介绍了。

<br>**本节参考阅读：**
- [百度百科 - Android 5.0](http://baike.baidu.com/view/5930831.htm)
- [Android Lollipop](http://developer.android.com/intl/zh-cn/about/versions/lollipop.html)
- [RecyclerView体验简介](http://blog.iderzheng.com/first-date-with-recyclerview/)

## Design支持库 ##
　　`Android5.0`是有史以来最重要的安卓版本之一，这其中有很大部分要归功于material design的引入，这种新的设计让整个安卓的用户体验焕然一新。谷歌在`2015 I/O`大会时，宣布了一个名叫`Design`的支持库，在这个库里提供了一堆material design控件。

　　接下来我们就一起来看看它们到底是何方神圣。

　　首先要知道的是，我们可以在`Android2.1`之上的任何版本中使用`Design`库，只需要在项目中添加对该库的依赖：
``` gradle
dependencies {
    compile 'com.android.support:design:22.2.0'
}
```

<br>
### NavigationView ###
　　在本章的第二节已经介绍了`DrawerLayout`是由三部分的组成的，可以用它来创建两个滑动菜单。

　　简单的说，`NavigationView`就是一个Google帮我们好的滑动菜单控件，它的外表大致为：

<center>
![知乎客户端](/img/android/android_o03_02.jpg)
</center>

　　从上图可以看出来，`NavigationView`分为上下两部分：

    -  上面的蓝色部分被称为是headerLayout，它可以放任意的控件。
    -  下面的白色部分是一组功能按钮，通常是单选的，某一时刻只能有一个被选中。

<br>　　下面就来介绍如何让`Toolbar`、`DrawerLayout`和`NavigationView`三者配合使用。
<br>　　范例1：首先在主题下面添加如下两个属性。
``` xml
<resources>
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="colorPrimary">#2196F3</item>
        <item name="colorPrimaryDark">#1976D2</item>
    </style>

    <style name="AppThemeBase" parent="AppTheme">
    </style>
</resources>
```
    语句解释：
    -  AppTheme主题下的两个属性分别用来设置状态栏和标题栏的背景色。
    -  然后在清单文件中，直接让Activity或者Application使用AppThemeBase主题即可。

<br>　　范例2：布局文件。
``` xml
<android.support.v4.widget.DrawerLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <android.support.v7.widget.Toolbar
            android:id="@+id/mToolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="?attr/colorPrimary" />
    </LinearLayout>

    <android.support.design.widget.NavigationView
        android:id="@+id/navigation"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/drawer_header"
        app:menu="@menu/drawer" />
</android.support.v4.widget.DrawerLayout>
```
    语句解释：
    -  根节点是DrawerLayout，内容布局是LinearLayout，左侧滑动菜单是NavigationView。
    -  NavigationView的headerLayout属性用来设置顶部的布局。
    -  NavigationView的menu属性用来指定菜单文件。这个属性几乎是必选的，不然NavigationView控件就失去意义了（我们用NavigationView就是为了导航的，结果却不为它设置导航菜单？）。
       -  另外，也可以在运行时动态改变menu属性。

<br>　　范例3：headerLayout。
``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="192dp"
    android:background="?attr/colorPrimaryDark"
    android:padding="16dp"
    android:theme="@style/ThemeOverlay.AppCompat.Dark"
    android:orientation="vertical"
    android:gravity="bottom">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Username"
        android:textAppearance="@style/TextAppearance.AppCompat.Body1"/>

</LinearLayout>
```
    语句解释：
    -  这个布局可以任意写，上面的代码是我copy网上的。

<br>　　范例4：菜单文件。
``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item
            android:id="@+id/nav_home"
            android:checked="true"
            android:icon="@drawable/ic_dashboard"
            android:title="Home" />
        <item
            android:id="@+id/nav_messages"
            android:icon="@drawable/ic_event"
            android:title="Messages" />
        <item
            android:id="@+id/nav_friends"
            android:icon="@drawable/ic_headset"
            android:title="Friends" />
        <item
            android:id="@+id/nav_discussion"
            android:icon="@drawable/ic_forum"
            android:title="Discussion" />
    </group>

    <item android:title="Sub items">
        <menu>
            <item
                android:icon="@drawable/ic_dashboard"
                android:title="Sub item 1" />
            <item
                android:icon="@drawable/ic_forum"
                android:title="Sub item 2" />
        </menu>
    </item>
</menu>
```
    语句解释：
    -  每个菜单项使用item标签来表示，菜单项可以设置标题和图标。
    -  上面使用group标签来定义一个菜单组，组内的菜单同一时间只能有一个被选中，且默认选择第一个。
    -  菜单是可以嵌套的，嵌套时会将菜单的名字显示出来，同时自动出现一个分割线，具体效果请自行写代码体验。

<br>　　范例5：Activity代码。
``` java
public class MainActivity extends AppCompatActivity {

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        final DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer);

        // 初始化Toolbar
        Toolbar toolbar = (Toolbar) findViewById(R.id.mToolbar);
        toolbar.setTitle("张德彪");
        // 将overflow按钮的颜色改为白色，这个用法是蒙出来的。
        toolbar.getOverflowIcon().setColorFilter(Color.WHITE, PorterDuff.Mode.DST);
        setSupportActionBar(toolbar);
        toolbar.setTitleTextColor(Color.WHITE);
        toolbar.setNavigationIcon(R.drawable.ic_menu);
        toolbar.setNavigationOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                // 用户点击标题栏左侧的按钮时，直接打开左侧的滑动菜单。
                drawer.openDrawer(Gravity.LEFT);
            }
        });

        // 初始化NavigationView
        NavigationView navigationView = (NavigationView) findViewById(R.id.navigation);
        // 设置菜单的点击事件。
        navigationView.setNavigationItemSelectedListener(
                new NavigationView.OnNavigationItemSelectedListener() {
                    public boolean onNavigationItemSelected(MenuItem menuItem) {
                        // 当菜单被点击时，就将它设置为选中状态。
                        menuItem.setChecked(true);
                        // 关闭滑动菜单。
                        drawer.closeDrawers();
                        return true;
                    }
                });
    }

    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        if (id == R.id.action_settings) {
            return true;
        }
        return super.onOptionsItemSelected(item);
    }
}
```
    语句解释：
    -  注意：如果你运行项目的时候发现NavigationView里的菜单看不见了，那应该是你在AppTheme主题下为textColorPrimary和textColorSecondary属性设置值了。

<br>　　**针对Android 5.0及以上版本**

　　运行上面的代码时，仔细观察状态栏的背景色，会发现状态栏的颜色和`NavigationView`的颜色重叠了（下面的左图）：

<center>
![](/img/android/android_o03_03.jpg)
</center>

　　我们可以通过配置，让程序变成上面右图的效果，即让状态栏的颜色透明。

<br>　　第一步，创建`res\values-v21\styles.xml`，并加入如下代码：
``` xml
<style name="AppThemeBase" parent="AppTheme">
    <item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
</style>
```
<br>　　第二步，修改布局文件，给`DrawerLayout`加上如下属性：
``` xml
android:fitsSystemWindows="true"
```
    语句解释：
    -  这个属性用来告诉系统由将DrawerLayout来接管状态栏的背景的绘制工作。

<br>　　最后，[点击此处下载源码 ](http://download.csdn.net/detail/github_28554183/9440751)（里面也包含apk），安装运行之后，就能体验`NavigationView`了。
<br>　　另外，笔者认为知乎的Android端并没有使用`NavigationView`，而是自己实现的一个类似的布局。

<br>
### TextInputLayout ###
　　在以前，`EditText`中的`hint`属性会在用户点击文本框，准备输入内容的时候就消失。

　　这就会导致下面这样的问题：

    假设一个页面有多个EditView，很有可能用户在输入多个EditView之后，不知道当前EditView需要输入什么内容。
　　`TextInputLayout`用来包裹`EditText`对象，当`EditText`获取输入焦点时，它会自动将`hint`移动到`EditText`上方显示。
　　另外，当用户输入的内容不合法时，`TextInputLayout`还可以在文本框的底部，显示一条错误信息。

　　笔者推荐大家去阅读：[《使用TextInputLayout创建一个登陆界面》](http://www.jcodecraeer.com/a/basictutorial/2015/0821/3338.html)，写的很详细。

<br>
### Snackbar ###
　　`Snackbar`提供了一个介于`Toast`和`Dialog`之间的轻量级控件，它可以很方便的显示消息和处理点击事件。

<br>　　范例1：显示Snackbar。

<center>
![Snackbar显示效果](/img/android/android_o03_04.jpg)
</center>

``` java
final Snackbar snackbar = Snackbar.make(view,"这是一条Snackbar消息", Snackbar.LENGTH_LONG);
// 当用户点击“关闭”二字的时候，就把Snackbar给关掉。
snackbar.setAction("关闭",new View.OnClickListener() {
    public void onClick(View v) {
        snackbar.dismiss();
    }
});
snackbar.show();
```
    语句解释：
    -  Snackbar的使用方法和Toast类似，其中view可以取值为当前界面上的任何一个View。

<br>　　范例2：修改字体的颜色。
``` java
final Snackbar snackbar = Snackbar.make(view,
        Html.fromHtml("<font color='green'>这是一条Snackbar消息</font>"), Snackbar.LENGTH_LONG);
// 设置action文本的字体颜色。
snackbar.setActionTextColor(Color.RED);
snackbar.setAction("关闭",new View.OnClickListener() {
    public void onClick(View v) {
        snackbar.dismiss();
    }
});
snackbar.show();
```
    语句解释：
    -  笔者想说的是，既然Snackbar并没有提供直接修改消息字体颜色的方法，那就表明不希望我们修改，它会帮我们处理各个系统之间的差异。
    -  如果实在是想修改，则可以使用Html类，因为Snackbar可以显示一个CharSequence类型的文本。

<br>　　`Toast`和`SnackBar`的区别是，前者是悬浮在所有布局之上的包括软键盘，后者则是显示在当前界面的View树上的。

<br>　　这一点从的源码上可以看出来：
``` java
private static ViewGroup findSuitableParent(View view) {
    ViewGroup fallback = null;
    do {
        if (view instanceof CoordinatorLayout) {
            // We've found a CoordinatorLayout, use it
            return (ViewGroup) view;
        } else if (view instanceof FrameLayout) {
            if (view.getId() == android.R.id.content) {
                // If we've hit the decor content view, then we didn't find a CoL in the
                // hierarchy, so use it.
                return (ViewGroup) view;
            } else {
                // It's not the content view but we'll use it as our fallback
                fallback = (ViewGroup) view;
            }
        }

        if (view != null) {
            // Else, we will loop and crawl up the view hierarchy and try to find a parent
            final ViewParent parent = view.getParent();
            view = parent instanceof View ? (View) parent : null;
        }
    } while (view != null);

    // If we reach here then we didn't find a CoL or a suitable content view so we'll fallback
    return fallback;
}
```
    语句解释：
    -  这段代码主要的作用是，从我们传递给Snackbar的View开始往上找，直到找到CoordinatorLayout或FrameLayout，然后Snackbar将被显示到找到的布局里。
    -  也就是说：
       -  如果界面中有CoordinatorLayout或android.R.id.content对象，则Snackbar会被直接放进去。
       -  如果界面中没有找到这两者中的任何一个，则Snackbar会被放到最后找到的（也就是最外层的）FrameLayout中去。
       -  因此网上有人说不要传递给Snackbar一个ScrollView对象的说法不完全成立，因为若你的布局中有android.R.id.content，则就不用担心。

<br>　　另外，有哥们在[《Snackbar使用及其注意事项》](http://blog.csdn.net/jywangkeep_/article/details/46405301)中说多次弹出Snackbar会让内存不足：

    -  笔者认为这个问题完全不用担心，就算可用内存变成0了，这都没关系，只要Snackbar不存在内存泄漏就行。
    -  因为当系统需要分配新内存且检测到内存不足时，就会自动触发gc，这样就会瞬间把之前的Snackbar占据的内存给回收回来。
    -  不信你可以写个demo，在真机上运行看看，当可用内存为0的时候，是不是瞬间就还原了。

<br>
### TabLayout ###
　　`TabLayout`用来实现`Tabs`选项卡界面，效果类似网易新闻客户端的Tab。

<center>
![TabLayout显示效果](http://www.jcodecraeer.com/uploads/20150731/1438306976405961.gif)
</center>

　　其实在`Github`上有很多开源库可以实现这个效果，只是谷歌把它官方化了，使得开发者无需引用第三方库就能使用。

　　它的用法网上有很多教程，笔者在这里简单说一下它的特点：

    -  支持Tab页滚动。
    -  支持和ViewPager连动。
    -  支持各种基础操作：事件监听、动态增删Tab等等。
　　更多的细节不打算细说，推荐阅读：[《android design library提供的TabLayout的用法》](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0731/3247.html)。

<br>
### FloatingActionButton ###
　　顾名思义，`FloatingActionButton`是一个浮动按钮，它的特点就是可以浮动显示在其它控件之上。

<br>　　从源码上可以看出，它本质上就是一个按钮：
``` java
public class FloatingActionButton extends ImageButton {
    // 省略若干行代码...
}
```
    语句解释：
    -  FloatingActionButton继承自我们早就知道的ImageButton类。

<br>　　范例1：使用FloatingActionButton。

<center>
![程序运行效果](/img/android/android_o03_05.jpg)
</center>

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="10dp">

    <android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"
        app:fabSize="mini" />

    <android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_done"
        app:elevation="1dp"
        app:backgroundTint="#000af4"
        app:fabSize="normal" />

</LinearLayout>
```
    语句解释：
    -  src属性，设置按钮所显示的图片。
    -  fabSize属性，设置按钮的尺寸，有mini(小尺寸)和normal(正常尺寸)两个取值。
    -  elevation属性，设置按钮的阴影高度。
    -  backgroundTint属性，设置按钮的背景色。
    -  layout_anchor属性，为浮动按钮指出它要浮动在哪个View上，取值是View的id。
    -  layout_anchorGravity属性，设置浮动按钮在View上的位置。


<br>　　需要说明的是，`FloatingActionButton`需要和配合`CoordinatorLayout`使用才能发挥它真正的威力，下面就来介绍它。

<br>
### CoordinatorLayout ###
　　`Coordinator`中文翻译为协调者、协调器，也就是说它本身不会在屏幕上显示内容，而是做为其它控件的协调器。
　　
　　这一点从的源码上可以看出来：
``` java
public class CoordinatorLayout extends ViewGroup implements NestedScrollingParent {
    // 省略若干行代码...
}
```
    语句解释：
    -  之前我们说过，默认情况下ViewGroup是不绘制任何内容的。

<br>　　范例1：放置控件。
``` xml
<android.support.design.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left|bottom"
        android:onClick="onClick"
        android:text="Button1" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right|top"
        android:text="Button2" />

</android.support.design.widget.CoordinatorLayout>
```
    语句解释：
    -  CoordinatorLayout的子元素需要使用layout_gravity属性来设置自己的位置。
       -  水平方向的取值有：start、end、left、right等。
       -  垂直方向的取值有：top、bottom等。
    -  另外，CoordinatorLayout对Snackbar做了增强：
       -  当Snackbar被放到CoordinatorLayout中显示时，那么在Snackbar自动关闭之前，用户还可以手动滑动删除它。

<br>　　范例2：与`FloatingActionButton`配合使用。
``` xml
<android.support.design.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|bottom"
        android:layout_margin="16dp"
        android:onClick="onClick"
        android:src="@drawable/ic_done" />

</android.support.design.widget.CoordinatorLayout>
```
    语句解释：
    -  当Snackbar在显示的时候，往往出现在屏幕的底部，若我们把FloatingActionButton也放到屏幕右下角的话，那么当Snackbar弹出时，浮动按钮就会被遮挡。
    -  不过CoordinatorLayout专门为做了浮动按钮做了适配，当Snackbar弹出时：
       -  若Snackbar将会遮挡浮动按钮，则就会将浮动按钮向上移动，Snackbar关闭时再移回来。
       -  若不会遮挡浮动按钮，则不移动。
       -  对于其他非浮动按钮控件来说，即便是Snackbar遮挡了它们，它们也不会移动。

<br>　　事实上，`CoordinatorLayout`也可以和`Toolbar`组合在一起实现各种效果。
　　但是它们是无法直接配合的，我们需要在它们之间添加一个名为`AppBarLayout`的控件，该控件是`LinearLayout`的子类，默认垂直排列元素。

　　也就是说，我们的布局文件的结构最终的应该为：
``` xml
<CoordinatorLayout>

    <AppBarLayout>

        <Toolbar />

    </AppBarLayout>

    <RecyclerView />

</CoordinatorLayout>
```
    语句解释：
    -  整个配合过程为：
       -  首先，使用CoordinatorLayout来监听RecyclerView、ViewPager等控件的滑动事件。
       -  然后，CoordinatorLayout将它得到的滑动事件交给AppBarLayout。
       -  接着，AppBarLayout来修改Toolbar的状态（显示或隐藏、尺寸等）。

<br>　　范例3：修改标题栏。
``` xml
<android.support.design.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycle"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|enterAlways"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

            <!-- 你可以在此处添加一个Button控件，但是不为它设置layout_scrollFlags属性，然后观察效果。-->
            <!-- 正确的效果应该是，Toolbar会随着滚动而显示或隐藏，而Button会一直显示。-->
            <!-- 因此比较常见的做法是，会在这里加一个TabLayout，这样就可以保证Tab总会显示了。-->
             

    </android.support.design.widget.AppBarLayout>
</android.support.design.widget.CoordinatorLayout>
```
    语句解释：
    -  首先，RecyclerView和AppBarLayout定义的位置无所谓，CoordinatorLayout会自动让标题栏显示在上方。
    -  然后，第11行代码用来让CoordinatorLayout来监听RecyclerView的滑动事件。
    -  接着要知道的是，AppBarLayout内部可以包含多个控件，因此当AppBarLayout接到CoordinatorLayout的事件后，会遍历所有子控件并进行判断：
       -  若当前子控件愿意接收滑动事件，则进行后续处理。
       -  若不愿接收，则跳过该子控件，继续检查下一个。
    -  也就是说，子控件使用layout_scrollFlags属性来告诉AppBarLayout它愿意接收事件，以及当事件发生时它希望的处理方式。
       -  如果愿意接收滑动事件，则取值中必须得包含scroll关键字。
       -  多个取值之间使用‘|’符号间隔。
    -  layout_scrollFlags属性的常用取值：
       -  enterAlways：不论RecyclerView当前滚动到何处，只要它向下滑动就隐藏标题栏，向上滚动就显示标题栏。
       -  enterAlwaysCollapsed：只有RecyclerView滚动到顶部时，才会显示和隐藏标题栏。
       -  exitUntilCollapsed：稍后介绍。


<br>　　我们还可以让`Toolbar`实现折叠效果，只需要使用`CollapsingToolbarLayout`包裹`Toolbar`即可。

<br>　　范例4：折叠标题栏。

<center>
![程序运行效果](http://www.jcodecraeer.com/uploads/allimg/150717/1G0543025-1.gif)
</center>

``` xml
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycle"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="192dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:contentScrim="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                app:layout_collapseMode="pin"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>
</android.support.design.widget.CoordinatorLayout>
```
    语句解释：
    -  必须得为AppBarLayout显式的指定高度，Toolbar的高度不能是wrap_content，而需要是具体的数值。
    -  需要为Toolbar设置layout_collapseMode属性，且值为pin。
    -  contentScrim属性，ToolBar被折叠到顶部固定时候的背景。
    -  layout_collapseMode属性，设置折叠的方式，有两个取值：
       -  pin：固定模式，在折叠的时候最后固定在顶端，效果如上图所示。
       -  parallax：视差模式，在折叠的时候会有个视差折叠的效果，效果如下图所示。

<br>　　范例5：视差滚动。

<center>
![程序运行效果](http://www.jcodecraeer.com/uploads/allimg/150717/1G0541510-7.gif)
</center>

``` xml
<android.support.design.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycle"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="192dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            android:fitsSystemWindows="true"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleMarginStart="48dp"
            app:expandedTitleMarginEnd="64dp">

            <ImageView
                android:id="@+id/backdrop"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                android:src="@drawable/cheese_1"
                android:fitsSystemWindows="true"
                app:layout_collapseMode="parallax" />

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                app:layout_collapseMode="pin" />

        </android.support.design.widget.CollapsingToolbarLayout>

    </android.support.design.widget.AppBarLayout>

    <android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_done"
        app:layout_anchor="@id/appbar"
        app:layout_anchorGravity="bottom|right|end" />

</android.support.design.widget.CoordinatorLayout>
```
    语句解释：
    -  使用layout_anchorGravity和layout_anchor属性可以让FloatingActionButton可以跟着AppBarLayout一起消失。




<br>**本节参考阅读：**
- [官方博客：Android Design Support Library](http://android-developers.blogspot.hk/2015/05/android-design-support-library.html)
- [中文翻译：Android的材料设计兼容库](http://www.jcodecraeer.com/a/anzhuokaifa/developer/2015/0531/2958.html)
- [通过 Navigation View 创建导航抽屉](http://myihsan.farbox.com/post/use-navigation-view-to-make-navigation-drawer)
- [Android SnackBar](http://www.cnblogs.com/wingyip/p/4604461.html)
- [Android M新控件之AppBarLayout，NavigationView，CoordinatorLayout，CollapsingToolbarLayout的使用](http://blog.csdn.net/feiduclear_up/article/details/46514791)
- [探索新的Android Material Design支持库](https://www.aswifter.com/2015/06/21/andorid-material-design-support-library/)
- [CoordinatorLayout与滚动的处理](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0717/3196.html)
- [android CoordinatorLayout使用](http://blog.csdn.net/xyz_lmn/article/details/48055919)





<br><br>