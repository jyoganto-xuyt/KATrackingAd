> [English Doc](https://github.com/KATracking/KATrackingAd/blob/master/KATrackingAd_iOS/README_EN.md)
# 下载链接
1. iOS 当前版本 3.2.1: [SDK download](https://github.com/KATracking/KATrackingAd/blob/master/KATrackingAd_iOS/KATracking.zip)
2. 聚合平台 SDK: [Mediation SDK download](https://github.com/KATracking/KATrackingAd/blob/master/KATrackingAd_iOS/MediationSDK.zip) 包含TalkingData统计SDK，如果不需要使用KATracking对TD进行集成，可忽略不加入成。
3. iOS SDK接入 Demo: [Demo Project](https://github.com/KATracking/KATrackingAd/tree/master/KATrackingAdDemo/iOS_Native)

# 接入说明

## 准备工作
### 加入KATracking依赖库
* KATracking.framework
* Resources.bundle

### 加入其它依赖库
* SystemConfiguration.framework
* CoreTelephony.framework
* QuartzCore.framework
* GLKit.framework
* AdSupport.framework
* UIKit.framework
* StoreKit.framework
* CoreLocation.framework
* CFNetwork.framework
* CoreMotion.framework
* AVFoundation.framework
* CoreData.framework
* CoreText.framework
* WebKit.framework
* libsqlite3.0.tbd
* libxml2.2.tbd
* libz.tbd

### 设置Deployment Target : iOS 8.0
![deployment target](https://github.com/KATracking/KATrackingAd/blob/master/README_Res/ios_deployment_target.png)

### 添加 -ObjC linker flag
![linker flag](https://github.com/KATracking/KATrackingAd/blob/master/README_Res/linker_flag.png)

## 开始接入

### SDK 初始化
请在应用生命周期尽可能早的步骤中启动SDK初始化，这样SDK可以尽快开始预加载广告。

`AppDelegate`
```Objective-c
#import <KATracking/KASDK.h>
```
`AppDelegate : application:didFinishLaunchingWithOptions:`
```Objective-c
[[KATracking sharedInstance] initWithAppId:@"<AppId>" channel:@"<AppChannel>"];
```
请向运营人员索取
* **AppId** - 应用标识
* **AppChannel** - 应用渠道号

# 原生广告

### 构建广告
创建一个原生广告的实例

`KAAdNative`
```Objective-c
KAAdNative *ad = [[KAAdNative alloc] initWithSlot:<AdSlot> delegate:<Delegate>];
```
* **AdSlot** - 广告位SlotId，用于请求广告
* **Delegate** - id<KAAdNativeDelegate> 实例，用于接收请求广告的回调

请求并加载广告

`KAAdNative`
```Objective-c
[ad load];
```

检测广告是否已经可以使用

`KAAdNative`
```Objective-c
BOOL ready = [ad isReady];
```

### 上报广告展示
当广告被展示后，调用此方法上报展示事件

`KAAdNative`
```Objective-c
[ad nativeAdRenderedWithView:adView];
```

* **adView** - 装在广告素材的容器UIView

### 上报广告点击
当用户点击了广告，调用此方法上报点击事件

`KAAdNative`
```Objective-c
[ad nativeAdClickedAtPointAndOpenLandingPage:touchPoint];
```
**强烈建议** 使用此方法让SDK来处理展示广告落地页

**或者使用**

`KAAdNative`
```Objective-c
NSString *landingpage = [ad nativeAdClickedAtPoint:touchPoint];
```

* **touchPoint** - 点击在容器UIView中的CGPoint
* **landingPage** - 广告落地页的地址字符串

### 广告素材信息
当成功加载一个原生广告后，下列参数包含了广告相关的素材信息

* **ka_slot** - 广告为的SlotId
* **ka_requestId** - 请求ID
* **ka_adTitle** - 广告文字标题
* **ka_adDescription** - 广告文字说明
* **ka_adIconImageView** - 广告图标图片的UIImageView
* **ka_adImageView** - 广告大图素材的UIImageView

### 广告回调
使用以下回调接收加载广告成功和失败的事件

`KAAdNativeDelegate`
```Objective-c
// Request has completed with nativeAd
- (void) nativeAdRequestCompletedWithAd:(nonnull KAAdNative *)nativeAd;

// Request has failed
- (void) nativeAdRequestFailedForSlot:(nonnull NSString *)nativeAdSlot
withStatus:(nonnull NSError *)nativeAdStatus;
```

# 开屏广告

### 构建广告
创建一个开屏广告的实例

`KAAdSplash`
```Objective-c
KAAdSplash *splash = [[KAAdSplash alloc] initWithSlot:<AdSlot> delegate:<Delegate>];
```

* **AdSlot** - 广告位SlotId，用于请求广告
* **Delegate** - id<KAAdSplashDelegate> 实例，用于接收广告事件回调

### 展示广告
调用下面方法加载并展示开屏广告

`KAAdSplash`
```Objective-c
[splash loadAndPresentWithViewController:<Controller>];
```

* **Controller** - 用于展示开屏广告的UIViewController

### 广告回调
使用以下回调接收加载广告的事件

`KAAdSplashDelegate`
```Objective-c
// Ad is successfully presented
- (void) splashAdPresentDidSuccess:(nonnull KAAdSplash *)splashAd;

// Ad present has failed
- (void) splashAdPresentDidFail:(nonnull NSString *)splashAdSlot
withError:(nonnull NSError *)error;

// Ad has been clicked
- (void) splashAdDidClick:(KAAdSplash *)splashAd;

// Ad view has been dismissed from screen
- (void) splashAdDidDismiss:(KAAdSplash *)splashAd;
```

# 插屏广告

### 构建广告
创建一个插屏广告的实例

`KAAdInterstitial`
```Objective-c
KAAdInterstitial *interstitial = [[KAAdInterstitial alloc] initWithSlot:<AdSlot> delegate:<Delegate>];
```

* **AdSlot** - 广告位SlotId，用于请求广告
* **Delegate** - id<KAAdInterstitialDelegate> 实例，用于接收广告事件回调

请求并加载广告

`KAAdInterstitial`
```Objective-c
[interstitial load];
```

检测广告是否已经可以使用

`KAAdInterstitial`
```Objective-c
BOOL ready = [interstitial isReady];
```

### 展示广告
调用下面方法加载并展示开屏广告

`KAAdInterstitial`
```Objective-c
[interstitial presentFromRootViewController:<Controller>];
```

* **Controller** - 用于展示插屏广告的UIViewController

### 广告回调
使用以下回调接收加载广告的事件

`KAAdInterstitialDelegate`
```Objective-c
// Interstitial Ad load success
- (void) interstitialAdLoadDidSuccess:(nonnull KAAdInterstitial *) interstitialAd;

// Interstitial Ad load fail
- (void) interstitialAdLoadDidFailForSlot:(nonnull NSString *) interstitialAdSlot
withError:(nonnull NSError *) interstitialAdStatus;

// Interstitial Ad presented successful
- (void) interstitialAdDidPresent:(nonnull KAAdInterstitial *) interstitial;


// Interstitial Ad has been clicked
- (void) interstitialAdDidClick:(nonnull KAAdInterstitial *) splashAd;

// Interstitial Ad has been dismissed from screen
```

# 激励视频广告

### 如何使用
激励视频广告在SDK中为单例，因此无需在创建新的实例，可以直接使用类方法展示广告，视频广告在SDK初始化成功后立即开始自动加载。

检测广告是否已经可以使用

`KAAdIncentivized`
```Objective-c
BOOL ready = [KAAdIncentivized isReady];
```

### 展示广告
调用下面方法加载并展示极力视频广告

`KAAdIncentivized`
```Objective-c
[KAAdIncentivized presentFromRootViewController:<Controller>];
```

* **Controller** - 用于展示激励视频广告的UIViewController

### 广告回调
设置一个激励视频的回调实例

`KAAdIncentivized`
```Objective-c
[KAAdIncentivized setDelegate:<Delegate>];
```

* **Delegate** - id<KAAdIncentivizedDelegate> 实例，用于接收广告事件回调

### 广告回调
使用以下回调接收加载广告的事件

`KAAdIncentivizedDelegate`
```Objective-c
// Incentvized video Ad has failed to present
- (void) incentivizedAdPresentDidFailWithError:(NSError *)error;

// Incentivized video Ad has presented successful
- (void) incentivizedAdPresentDidSuccess;

// Incentivized video Ad has complete without skip 
- (void) incentivizedAdPresentDidComplete;

// Incentivized video Ad has complete with skip 
- (void) incentivizedAdPresentDidSkip;
```

