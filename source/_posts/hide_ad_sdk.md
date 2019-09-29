title: 公司广告SDK分享
date: 2019-07-20 16:24:12
categories: 隐藏分类
---

　　本次分享的目的：帮助大家更加熟悉SDK内部实现，提高大家解决广告相关问题的能力。

　　本次分享的内容：

>第一节：基础信息
>第二节：广告配置文件
>第三节：初始化流程简述
>第四节：初始化流程UML的解读
>第五节：踩坑经历
>第六节：讨论环节

# 第一节 基础信息 #

　　1、仓库地址：[点击查看](https://bitbucket.org/sealcn/androidadsdk/src/master/) ，里面有很多分支，目前主要维护`master`分支。

　　2、接入文档地址：[点击查看](https://sdkdoc.dailyinnovation.biz/adsdk/Android.html) ，介绍了SDK的基本使用方法。

　　3、支持有11个广告平台：

    Facebook、Admob、Unity、Toutiao、TencentGdt
    AppLovin、Vungle、AdColony、ChartBost、IronSource、MoPub

　　默认情况下，SDK只开启了`Facebook`和`Admob`的广告，如果你需要其他平台，则需要另外开启。

　　4、支持有5种广告类型：

    插屏、激励视频、banner、原生、开屏（头条和广点通才有）

　　5、支持广告相关的统计事件打点，方便变现组进行广告优化。

    adsdk_is_ready：调用广告位的isReady方法时触发。
    adsdk_try_to_load：调用广告位的request方法时触发。
    adsdk_try_show：调用广告位的show方法时触发。
    adsdk_fill：请求广告成功时触发。
    adsdk_req：开始请求广告时触发。
    adsdk_show：广告被显示方法时触发。

　　由于这六个事件量比较大，所以支持事件抽样，具体代码在`MeeviiADManager.isEventSampleSizeOk()`中。

　　默认情况下，采样的规则为：

    首先，在SDK初始化时创建一个10000以内的随机数A。
    然后，从广告配置文件中读取我们配置的一个数字B，若没配置则使用默认值10000。
    最后，在发送事件之前，会判断是否A<=B，若是则发送事件。

　　6、广告SDK支持ABTest，只需要在初始化SDK的时候，传递ABTest组号即可。


# 第二节 广告配置文件 #

　　下面是简单的示范文件：

``` json
{
    "configVersion": 1,
    "platformCfg": [
        {
            "platform": "buad",
            "appID": "5018214"
        }
    ],
    "placements": [
        {
            "adType": "interstitial",
            "placementId": "enterEdit",
            "optionAdUnits": [
                {
                    "adUnitPriority": 1,
                    "adUnitPlatform": "buad",
                    "adUnitId": "918214558"
                }
            ]
        }
    ],
    "sample_size": 5000,
    "configName": "CN_Default"
}
```

　　1、属性`configVersion`，用来指定当前文件的版本号。

　　2、属性`platformCfg`，用来设置各个广告平台的账号id。

    平台关键字有：applovin、unityads等。
    其他平台的取值在com.meevii.adsdk.adsdk_lib.notify.ADConfigUtils类。

　　3、属性`placements`，配置所有的广告位。

    每个广告位都有一个adType属性，表示它的广告的类型。
    取值在ADConfigUtils.GetADTypeFormString方法中。

　　4、属性`sample_size`，统计事件的采样比率。

　　5、属性`configName`，是配置文件的名称，最好定义一个，变现组需要用到它。

# 第三节 初始化流程简述 #

　　按照接入文档介绍，调用`MeeviiADManager.Instance().init()`方法执行初始化。

``` java
AdConfig adConfig = new AdConfig.Builder(app)
                .setAdApi(new AdApi.Builder(app)
                        .setPackageName("sandbox.pixel.number.coloring.book.page.art.free.v2")
                        .isDebug(isDebug)
                        .setGroupId(ABTestManager.getInstance().getGroupId())
                        .build())

```

　　下面是初始化时所涉及到的UML类图：

<center>
![](/img/meevii/uml_01.png)
</center>

　　其中：

>AdConfig类广告配置文件相关的操作（比如加载本地、请求网络和保存），输出数据是一个json串。
>MeeviiADManager抽象类是，提供了统一的对外接口，ADManagerImpl是具体的实现类。

　　在整个初始化流程中，所做的事情主要有：

>1、通过AdConfig类去加载配置文件。
>2、解析platformCfg字段，解析出各个广告平台的基础信息。
>3、解析placements字段，解析并初始化各个广告位。

<br>**注意事项**

　　广告SDK是支持按国家配置广告的，默认会依据用户手机号所在国家拉取配置，如果没有手机号则以手机当前语言来去服务器拉取配置。

　　以BitColor为例，产品分为国内版（主打头条）和海外版（主打admob和fb），遇到的问题是：

>一个安装了国内版的中国用户，若它的语言是英文，那么SDK会去拉国外的配置，进而显示的就是admob和fb的广告，由于是在国内环境，所以广告的展示量就会很低。

　　为了解决这个问题，可以在创建`AdApi.Builder`的时候，明确的设置国家代码，比如国内包的配置：

``` java
new AdApi.Builder(app)
      .setPackageName("sandbox.pixel.number.coloring.book.page.art.free.v2")
      .isDebug(false)
      .setGroupId(ABTestManager.getInstance().getGroupId())
      .setCuntry("CN"))
      .build()
```

# 第四节 初始化流程UML的解读 #

　　下面是广告SDK执行初始化时，所能涉及到的类的UML类图：

<center>
![](/img/meevii/uml_02.png)
</center>

　　从图中可以看到，`ADManagerImpl`类将流程分为了两条：

     -  广告平台的初始化
     -  广告位的初始化

　　按照常规的逻辑，我们先介绍平台的初始化，然后再去介绍广告位的初始化。


<br>**广告平台的初始化**

　　（先来介绍一下UML类图，再说下面的，再把代码切换到UML的分支处。）

　　广告SDK的特点是，支持各个广告平台的热插拔，所以广告平台的初始化也会稍微有些特殊，大致的流程为：

　　第一，在开发阶段通过`compileOnly`来引入各个广告平台的SDK。

　　第二，在SDK初始化阶段，通过解析配置文件，创建出一个个`ADPlatformSetting`对象。

　　第三，当某个ADTask的Request被调用时，会执行`ADPlatformSetting`的`Init`方法执行具体的初始化，此时会通过反射判断对应平台的SDK是否安装就位。


<br>**广告位的初始化**

　　1、每个广告位对应一个ADGroupSet。
　　2、每个ADGroupSet对象内部，默认会按照广告平台，将旗下的所有广告id，分成多个ADGroup。
　　3、每个ADTask表示一个广告id，每个ADGroup里有多个ADTask对象，按优先级降序排列。
　　4、ADCacheTask继承自ADTask用来处理各个广告id的失败重试、发送统计事件等事情。旗下有多个实现类，以admob平台为例：

    -  InterstitialAdMobTask
    -  BannerAdMobTask
    -  RewardAdMobTask
    -  NativeAdMobTask

　　5、ADContainer及其子类，用来封装各个平台的广告view的初始化、请求、显示、回调的操作。旗下有多个实现类，以admob平台为例：

    -  AdMobInterstitalView
    -  AdMobBannerView
    -  AdMobRewardView
    -  NativeAdMobView

　　每个ADCacheTask内部都会包含一个对应的ADContainer对象。

<br>**口述：广告位的请求流程**

　　请求流程中，需要注意的点：

    -  某个广告位下的所有ADGroup会通知发出请求。
    -  某一个ADGroup内部的广告ID会按照优先级的高低去请求。

　　基于上述的请求 —— 回调流程中，就会出现一种情况：ADGroupSet是全局唯一的，如果先后有两处代码都需要显示一个广告位，即都把自己的回调设置给某个ADGroupSet，那么只有最后设置的回调才有效。

>以BitColor的激励视频为例，涂色界面有两种方式触发视频的观看：
>1、底部道具栏中，道具的数量不足了，点击道具图标会触发看视频。
>2、从进入涂色界面开始，就会安排个定时器，每2分钟检测一次是否有广告，若有则弹出浮动按钮，提示用户可以看视频拿道具。
> 
>假设此时用户点击了底部道具栏上的按钮，程序会先检测是否有广告，假设一切顺利，开始播放视频了，并把自己的回调设置给SDK。
>此时一个后台定时逻辑被触发了，同样会检测是否有广告，发现没有，就会接着去调用请求方法，并把自己的回调设置给SDK。
> 
>问题出现了：那么当激励视频播放完毕后，会通知倒计时设置的回调去领奖，而不是道具栏的那个回调，因为回调被覆盖掉了，进而导致我们不会发放奖励。

# 第五节 踩坑经历 #

　　BitColor在使用SDK的过程中遇到了几个问题，本节就来分享它们。

## 1、Banner不显示 ##

　　问题描述：接入mopub广告SDK时，发现banner无法显示。

　　需要现场阅读代码，(此问题已被老戴修复，详见BannerMoPubView)。


## 2、Banner展示率低 ##

　　问题描述：变现组发现BitColor的Banner展示率很低。

　　定位结果：程序切到后台后，Banner广告依然会定时请求，这些请求到的广告无法被显示，导致展示率低。

　　通过查看PBN的代码以及Debug后发现，需要在咱们的BaseActivity中写入如下代码才行：

``` java
    @Override
    protected void onResume() {
        super.onResume();
        OnApplicationPause(this, false);
    }

    @Override
    protected void onPause() {
        super.onPause();
        OnApplicationPause(this, true);
    }

    /**
     * 当Actvity的可见状态改变时，需要调用此方法。
     * 目前调用它是来解决：当界面不可见时，banner广告仍然会每30秒一次的定时去请求。
     */
    public static void OnApplicationPause(Activity activity, boolean bPause) {
        int initState = MeeviiADManager.GetInstanceState(null);
        if (initState == 1) {
            MeeviiADManager.Instance().OnApplicationPause(activity, bPause);
        }
    }
```

## 3、Admob Banner点击后消失 ##

　　问题描述：测试发现，点击了Admob的Banner后，Banner就会消失。

　　定位结果：Admob的Banner广告被点击后，会回调onAdClosed方法。

　　通过查看PBN的代码发现，需要重写回调接口的OnAdClosed方法，并再次发起请求才行：

``` java
public static void showBannerAd(Activity activity, String position, String imageId) {
        try {
            if (!placementIsReady(position) || isAdShowing(position)) {
                return;
            }
            IADGroupSet groupSet = MeeviiADManager.Instance().GetADGroupsetByID(position);
            // 如果当前有广告则尝试显示
            groupSet.SetNotify(new SimpleAdListener() {
                @Override
                public void OnAdGroupLoad(String s) {
                    showBannerAd(activity, position, imageId);
                }

                @Override
                public void OnAdClosed(String s) {
                    // 对于Admob的banner来说，当点击广告后，会自动回调onClose方法，所以此处需要再次调用显示
                    showBannerAd(activity, position, imageId);
                }
            });
            if (groupSet.IsCanShowAD()) {
                groupSet.ShowAD(activity.findViewById(R.id.adBanner));
            } else {
                groupSet.StartRequest();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

# 第六节 讨论环节 #

　　讨论的主题：你对现在的SDK有什么疑问、建议？

　　我先抛砖引玉：

>从上面的踩坑记录来看，SDK除了提供核心功能外，还应该提供一个基础的AdsManager给大家扩展用，把广告操作的细节对我们屏蔽。


<br><br>