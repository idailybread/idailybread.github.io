---

title: Cocoapods创建私有库问题汇总
date: 2020-03-9 10:00:36
author: Maizi
categories: iOS开发

---

#### Swift、OC混编Framework

正常的Swift项目可以通过创建bridge桥接文件的方式来导入OC库或文件，但是Framework不行，它会提示以下错误：

```
using bridging headers with framework targets is unsupported
```

如何解决这个问题，主要分以下几种情况：

##### 同一个Framework的混编

**解决方式**

新建Framework时，会自动创建一个和库同名的.h文件(umbrella header/master header)。我们将需要使用的.h文件import到`umbrella header`，然后在`Build Settings`里将`Defines Module`设置为`YES`。

如果编译时提示`Include of non-modular header inside framework module`错误。

你可以在`Build Setting`找到`Allow Non-modular Includes In Framework Modules`并设置为`YES`来忽略掉这个问题。

看起来一切OK，但是当你打包库文件给其他项目使用时，问题依旧存在。

正确的做法是将对应的.h文件加入到`Build Phases -> Headers -> public`里。

**错误原因**

出现这个错误是因为我们创建的一般都是`project header files`，而`umbrella header`是public的。

打包Framework时，Xcode会自动生成一个和库同名的module文件和一个umbrella文件(如果没有的话)，默认的module文件在工程中是不可见的，是在编译时生成的。它包含了所有`当前库里`的`public header`，因此我们可以直接通过`import {module name}`的方式来完成库的引用，而不需要去将每个.h都import一遍，看起来并不优雅。

因此当你在`umbrella header`中引入一个非public的文件时，就会出现上述`non-modular header`的错误。当然，同样的，你在其他`public header`文件中引入非public文件，也会有同样的问题。

**优缺点**

这样做的好处是，你的OC文件通过`umbrella header`暴露给了swift使用，解决了swift和OC混编的问题。但是同样的，也暴露给了外部，而你可能并不想这么做。

官方文档：[Importing Objective-C into Swift](https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift)

##### 不同Framework的混编

虽然swift已经普及，但是对于老项目而言，考虑到成本等各种因素，还是有很大一部分是由OC编写完成的，而我们又不可避免的要使用到它。

很多第三方的静态库Framework并没有使用module，这也是我们不能在Swift中直接引用该静态库的原因，需要在bridge里挨个import我们需要的头文件，但是在Framework里，并不支持bridge。

通过同样的方法，在`umbrella header`中引用三方库文件时，也会报`Include of non-modular header inside framework module`错误，因为默认的module里只允许包含当前库下的`public header`，而你也不能修改对方的Framework。

一种做法是，我们在搞一个OC的Framework，我们暂且叫做BridgeFramework，对引用的三方库进行二次封装，然后通过`import BridgeFramework`的方式来完成swift的引用，因为我们自己创建的库是可以生成module的。

当然，这样做比较繁琐，维护起来也很麻烦。

在这里，我们可能需要使用自定义module的方式，来解决这个问题。

**Module介绍**

Module是一种集成库的方式，在Module出现之前，开发者需要在引入库文件的同时引入需要使用的头文件，以保证编译的正常进行。但是每次引入库的时候都要导入一堆文件，看起来并不优雅。Module和Framework的出现让开发者极大程度上告别了这些不优雅的工作。从Xcode5开始，Apple对系统内置的动态库添加了Module的支持。Xcode6开始将Module开放给开发者，开发者可以让自己的库支持Module。

Module本质上是一个描述文件，用来描述Module中包涵的内容，每个Module中必须包涵一个umbrella头文件，这个文件用来import所有这个Module下的文件。

**大致关系为：**`import module -> import umbrella header -> other header`

**使用Module库的调用方式：**

项目类型     | OC库(MeeviiAds) | Swift库(MeeviiAds)
-|-|-
OC项目      | `#import <MeeviiAds/MeeviiAds.h>` | `#import <MeeviiAds-Swift.h>` 
Swift项目   | `import MeeviiAds` | `import MeeviiAds` 

`MeeviiAds.h`其实就是`umbrella header/master header`。

**自定义Module创建流程**

我们以桥接GDTMobSDK为例。

在项目中新建一个`module.modulemap`和一个`OCBridgeHeader.h`，最好将它们放在同一个文件夹下，否则在`module.modulemap`里配置映射文件路径时，你需要写上完整的`OCBridgeHeader.h `文件路径。

![](/img/iOS/20200307193113.jpg)

`module.modulemap`内容：

```
module ADSDKHeaderBridge {
    header "OCBridgeHeader.h" // 这是文件路径!!!
    export *
}
```

`OCBridgeHeader.h`内容：

```
#ifndef OCBridgeHeader_h
#define OCBridgeHeader_h

#import <GDTMobSDK/GDTMobBannerView.h>
#import <GDTMobSDK/GDTRewardVideoAd.h>
#import <GDTMobSDK/GDTNativeExpressAd.h>
#import <GDTMobSDK/GDTNativeExpressAdView.h>
#import <GDTMobSDK/GDTMobInterstitial.h>
#import <GDTMobSDK/GDTSplashAd.h>

#endif /* OCBridgeHeader_h */
```

配置module路径：

![](/img/iOS/20200307193057.jpg)

然后在需要使用的地方直接`import ADSDKHeaderBridge`就可以愉快的使用GDTMobSDK了，同样的，该方法也适用于同Framework下的混编。

如果想要上传pod，你还需要修改podspec文件：

```
spec.preserve_paths = ["Sources/Module/module.modulemap", "Sources/Module/*.h"]
spec.pod_target_xcconfig = { 
  "SWIFT_INCLUDE_PATHS" => "$(SRCROOT)/MeeviiAds/Sources/Module"
}
spec.header_mappings_dir = "Sources/Module"
spec.header_dir = "Sources/Module"
```

**注意事项**

1、如果已经在`preserve_paths`添加了`modulemap`和`umbrella header`，可以不用在`source_files`里再加一遍，如果要在`source_files`里加也可以，记得指定`public_header_files`。如果没有指定，你自己创建的modulemap也会当做public处理。这样lint的时候会报`Include of non-modular header inside framework module`。

![](/img/iOS/20200307195840.jpg)

2、lint时遇到`Include of non-modular header inside framework module`错误，可以在后面添加`--use-libraries`。虽然能验证和上传通过，但是其他项目引用的时候还是会有问题。

3、虽然我们在`Build Settings`里指定了`Import Paths`，但是在上传pod时，这些设置是不生效的。所以还是需要你通过`pod_target_xcconfig`再指定一遍，该路径和`Import Paths`里稍有不同。

原路径：`$(SRCROOT)/Sources/Module`
pod_target_xcconfig路径：`$(SRCROOT)/MeeviiAds/Sources/Module`

MeeviiAds为你的`spec.name`。

4、`user_target_xcconfig`是针对所有pod的，可能和其他pod存在冲突。`pod_target_xcconfig `是针对当前pod的。

#### 删除已发布的版本

```
pod trunk delete 框架名称 版本号
```

#### 私有repo内的pod库之间相互依赖导致lint和push不通过的问题

lint在对引用库验证时，默认只验证官网的仓库，我们需要手动添加验证源才能通过.

**解决办法：**

指定repo地址：`--sources=******.git`

**示例：**

```
pod spec lint --sources=git@bitbucket.org:sealcn/sealrepo.git,https://github.com/CocoaPods/Specs --allow-warnings --verbose

pod repo push SealRepo MeeviiAds.podspec --sources=git@bitbucket.org:sealcn/sealrepo.git,https://github.com/CocoaPods/Specs --allow-warnings --verbose

```

#### Pod subspec默认无法移除

podspec为库的描述文件，包括应该从何处获取资源，使用什么文件，构建什么版本等。

相关字段介绍可以见：[Podspec文件说明](https://guides.cocoapods.org/syntax/podspec.html#specification)

`default_subspecs`：一个subspec的数组，是用来指定默认subspec依赖的，如果没有指定，则会指定全部subspec作为依赖项。

所以指定好你的`default_subspecs`就可以了。

#### [!] Authentication token is invalid or unverified. Either verify it with the email that was sent or register a new session. ####

```
pod trunk register 邮箱地址 名字
```

会给你发一封邮件，点击链接就可以了。

#### 相关文档

* [grpc-Core.podspec](https://github.com/grpc/grpc/blob/master/gRPC-Core.podspec)
* [Finding a dynamic framework linking solution for Flint](https://flint.tools/blog/finding-a-weak-linking-solution.html)
* [Defining Modules for Static Libraries](http://blog.bjhomer.com/2015/05/defining-modules-for-custom-libraries.html)
