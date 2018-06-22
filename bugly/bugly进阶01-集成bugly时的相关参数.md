#bugly进阶01-集成bugly时的相关参数
[个人github][https://github.com/MrYu4]
[CSDN博客][https://blog.csdn.net/ecjtuacm_yuewei]
##前言
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bugly的集成十分的简单，在代码中只需要简单的一个语句就可以轻松[集成](https://bugly.qq.com/docs/user-guide/instruction-manual-ios/?v=20170912151050)：
```
- (BOOL)application:(UIApplication *)application 
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [Bugly startWithAppId:@"此处替换为你的AppId"];
    return YES;
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而实际上官方还为我们提供了另一个类似的语句：
```
/**
 *  使用指定配置初始化Bugly
 *
 *  @param appId 注册Bugly分配的应用唯一标识
 *  @param config 传入配置的 BuglyConfig
 */
+ (void)startWithAppId:(NSString * BLY_NULLABLE)appId
                config:(BuglyConfig * BLY_NULLABLE)config;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个方法和前者相比多了一个BuglyConfig的类，通过它，我们可以让bugly按照我们的意愿为我们更好的工作。

##BuglyConfig可设置参数
```
@property (nonatomic, assign) BOOL debugMode;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bugly sdk的debug信息开关。默认为NO，如果设置为YES，在控制台中你将会看到bugly自带的输出（BuglyLog），一般我们在开发的过程中会需要到它，但是上线的时候就没有打开的必要了，推荐设置为：
```
#if DEBUG
    config.debugMode = YES;
#endif
```

----
```
@property (nonatomic, copy) NSString *channel;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;自定义渠道标识。在“运营统计”模块很多和渠道有关的地方用到它，运营人员可以轻而易举地得知用户喜欢从哪个应用商店下载APP（虽然iOS开发者不太需要使用这个，毕竟苹果的应用商店只有一个），但是稍微了解一下，推荐设置为：
```
#if DEBUG
        config.channel = @"测试模式";
#else
        config.channel = @"苹果应用商店";
#endif
```

----
```
@property (nonatomic, copy) NSString *version;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;自定义版本号。默认是空，但是如果不填的话会用你在xcode中设置的版本号，也就是你在项目target中Bundle Identifier的下一行Version里面写的那行字符串，无论是查看崩溃日志还是查看版本分布，你在很多地方都能看到版本号。推荐设置为：
```
```
（皮一下:-D）

----
```
@property (nonatomic, copy) NSString *deviceIdentifier;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;自定义设备唯一标识。（留空）推荐设置为：
```
```

----
```
@property (nonatomic) BOOL blockMonitorEnable;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;卡顿监控开关。默认是NO，也就是不监控卡顿，在bugly的“异常上报”模块中有检测卡顿的功能，那里会有记录。

----
```
@property (nonatomic) NSTimeInterval blockMonitorTimeout;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;卡顿监控判断间隔。默认为3.5秒。也就是说卡顿超过这么久之后会上报卡顿。

----
```
@property (nonatomic, copy) NSString *applicationGroupIdentifier;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;App Groups Id。这个要结合[APP Extension SDK](https://bugly.qq.com/docs/user-guide/instruction-manual-ios-app-extension/?v=20170912151050)一起使用，如果你听得一头雾水的话，那么这个字符串就没有必要理会。

----
```
@property (nonatomic) BOOL symbolicateInProcessEnable;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;进程内还原开关。默认是YES。

----
```
@property (nonatomic) BOOL unexpectedTerminatingDetectionEnable;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;非正常退出事件记录开关，默认关闭。这个一开始我也看得百思不得其解，然后在官方文档中的一个[阴暗角落](https://bugly.qq.com/docs/user-guide/upgrading-2.x-ios/)找到了答案，非正常退出事件实际上就是unix的专业术语的一个指令事件，叫做"SIGKILL"，而"SIGKILL"一般不是用户或者开发者的问题，而是[程序已经进入suspend状态、applicationDidEnterBackground已经回调的时候，由系统发出"SIGKILL"强制程序直接结束](https://blog.csdn.net/liqinghua1653/article/details/28433233)。所以推荐不做修改或者设置为YES。

----
```
@property (nonatomic) BOOL viewControllerTrackingEnable;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;页面信息记录开关，默认开启。当你查看日志的时候，里面有个“跟踪数据”，点进去之后你会发现里面还有“页面追踪”模块，当你开启的时候你会发现里面会用内容，这表示用户使用过程经过的controller，如果关闭，则不会有。所以推荐不做修改或者设置为YES。
![页面追踪](https://github.com/MrYu4/MyUploadPicture/blob/master/%E9%A1%B5%E9%9D%A2%E8%BF%BD%E8%B8%AA.png?raw=true)

----
```
@property (nonatomic, assign) id<BuglyDelegate> delegate;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;不多说了，大家都懂。

----
```
@property (nonatomic, assign) BuglyLogLevel reportLogLevel;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 控制自定义日志上报等级，默认值为BuglyLogLevelSilent，即关闭日志记录功能。bugly提供了类似于``NSLog``的输出方法，叫做``BLYLog``。而且这个方法在``BuglyConfig.debugMode``为真的时候可以支持在编译器的控制台输出显示，而且无论debugMode是否真假，在上传的日志中点进“跟踪日志”可以查看输出显示；不过这和``NSLog``有所不同的是分为Error、Warn等多个等级。![自定义日志](https://github.com/MrYu4/MyUploadPicture/blob/master/bugly-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%97%A5%E5%BF%97.png?raw=true)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 设置完上报等级之后，就表明bugly将上传该等级以及以上等级的输出，假设你设置的等级为``BuglyLogLevelWarn``，那么上报的输出的等级会包括warn和error这两个等级，而较低的Info、Debug将不会上传。建议设置为```BuglyLogLevelVerbose```，也就是最低等级。

----
```
@property (nonatomic, assign) BOOL consolelogEnable;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;控制台日志上报开关，默认开启，如果该值为真，并且如果控制台有输出的话，日志中的会有“控制台信息”模块，可以点进去查看控制台的输出（如果按照本文推荐的设置的话你看普通用户上报的日志中在“控制台信息”是不会看到```BLYLog```输出的信息的，你猜是为什么？提示在``BuglyConfig.debugMode``的推荐设置）,所以推荐不做修改或者设置为YES。
![控制台日志](https://github.com/MrYu4/MyUploadPicture/blob/master/%E6%8E%A7%E5%88%B6%E5%8F%B0.png?raw=true)

（开发时如果出现崩溃XCode也会打印出类似的信息）

----
```
@property (nonatomic, assign) NSUInteger crashAbortTimeout;
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;崩溃退出超时时间，默认为5，单位秒。如果监听到崩溃后，App一直没有退出，则到达超时时间后会自动abort进程退出。如果你设置为0的话则不会使用此项功能。

##其他可以设置的参数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了BuglyConfig之外在``Bugly.h``中也有一些可以设置的参数，但是有意思的是只能通过set方法赋值而不是直接对变量赋值。

----
```
/**
 *  设置用户标识
 *
 *  @param userId 用户标识
 */
+ (void)setUserIdentifier:(NSString *)userId;
/**
 *  更新版本信息
 *
 *  @param version 应用版本信息
 */
+ (void)updateAppVersion:(NSString *)version;

```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用户标识默认为空，版本信息默认为APP的版本号+内部版本号。二者都可以在可以在日志中“最近一次上报”模块中会有出现，分别对应的是用户ID、应用版本。![最近一次上报](https://github.com/MrYu4/MyUploadPicture/blob/master/%E6%9C%80%E8%BF%91%E4%B8%80%E6%AC%A1%E4%B8%8A%E6%8A%A5.png?raw=true)

&nbsp;
&nbsp;
&nbsp;
##打赏
![支付宝](https://github.com/MrYu4/MyUploadPicture/blob/master/%E6%94%AF%E4%BB%98%E5%AE%9D.png?raw=true)

![微信](https://github.com/MrYu4/MyUploadPicture/blob/master/%E5%BE%AE%E4%BF%A1.png?raw=true)

（这个求打赏有些丑啊……有什么办法改进一下？）

