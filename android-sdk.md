# LinkActive SDK Android平台集成文档

## 引入jar包并依赖
将jar包复制到项目libs目录下，并添加到项目Module层的build.gradle依赖中，如下示例：

```groovy
dependencies {
  //注意修改jar包名
  compile files('libs/LinkedME-Active-SDK-V1.1.0.jar')
}
```

## 配置AndroidManifest.xml文件
###  配置点击广告后显示h5页面Activity
```xml
<activity
    android:name="cc.lkme.linkactive.view.LMH5Activity"
    android:configChanges="orientation|keyboardHidden|navigation|screenSize"
    android:exported="false"
    android:screenOrientation="behind"
    android:windowSoftInputMode="adjustResize|stateHidden"/>

```
###  配置点击广告后下载apk的Service

```xml
 <service
    android:name="cc.lkme.linkactive.network.LMDownloadService"
    android:exported="false"/>
```
> apk下载存储位置为LMDownload文件夹，因Android 7.0系统权限更改，需要通过FileProvider来获取Download文件夹中指定文件的Uri。因此需要添加FileProvider相关配置，具体配置参考官方文档：[Setting Up File Sharing](https://developer.android.com/training/secure-file-sharing/setup-sharing.html)。
> FileProvider配置完成后，需要在FileProvider的xml文件配置中添加\<external-path/>子节点，用于访问Download文件夹中的文件，如下所示：
> 
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
<paths>
            <external-path
                name="lm_active_download"
                path="LMDownload"/>
</paths>
</resources>
```

###  配置点击广告下载apk安装后的监听
注册该监听以便将广告带来的下载安装状态回传给服务器

```xml
<receiver
    android:name="cc.lkme.linkactive.network.LMInstalledReceiver">
    <intent-filter>
        <action android:name="android.intent.action.PACKAGE_ADDED"/>
        <data android:scheme="package"/>
    </intent-filter>
</receiver>

```  
## 初始化SDK

描述：

- 注册LinkActive并在后台获取LinkedME_key。

方法：

```
public static LinkedME getLinkActiveInstance(@NonNull Context context, String linkedme_key)
```

示例：
 
```java
LinkedME.getLinkActiveInstance(this, "LinkedME后台分配的Link Active SDK key");
if (BuildConfig.DEBUG) {
    //设置debug模式下打印LinkedME日志
    LinkedME.getLinkActiveInstance().setDebug();
}
```
#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| context | Application Context  | Application 上下文 |
| linkedme_key |LinkedME key |后台Link Active分配的key|
---


## 添加广告
广告分以下几种广告形式：
1. Banner广告：显示多个广告，可按一定的时间间隔自动轮播；
2. 插屏广告：显示一个大尺寸广告；
3. 开屏广告：开屏页面广告
4. 原生广告：用户自定义广告视图

###  添加Banner广告
####  创建Banner广告对象
 ```java
   LMBannerAdView lm_banner = new LMBannerAdView(this, "4000061_373");
 ```
 
#### 加载广告及设置监听

方法：

```java
public void setRefresh(int refresh) 
public void setShowClose(boolean showClose)
public void loadAd()
public void setOnAdStatusListener(OnAdStatusListener onAdStatusListener)
```

示例：
 
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  LMBannerAdView lm_banner = new LMBannerAdView(this, "4000061_373");
  lm_banner.setOnAdStatusListener(new OnAdStatusListener() {
            @Override
            public void onClick(AdInfo adInfo) {
                super.onClick(adInfo);
                Log.i(TAG, "点击广告！");
            }

            @Override
            public void onExposure(AdInfo adInfo) {
                super.onExposure(adInfo);
                Log.i(TAG, "曝光广告！");
            }

            @Override
            public void onGetAd(boolean status) {
                super.onGetAd(status);
                if (status) {
                    Log.i(TAG, "有可展示广告！");
                } else {
                    Log.i(TAG, "无展示广告！");
                }
            }

            @Override
            public void onClose() {
                super.onClose();
                Log.i(TAG, "关闭广告！");
            }
        });
        // 设置轮播时间间隔，单位秒，可设置20到200秒
        lm_banner.setRefresh(5);
        // 设置是否显示关闭按钮，默认不显示
        lm_banner.setShowClose(true);
        // 添加视图
        ad_container.addView(lm_banner);
        // 开始加载广告
        lm_banner.loadAd();
 }

```

#### 参数说明

```java
/**
  * 为该Banner广告设置广告位id，自动加载显示广告
  * 
  * @param context      Context
  * @param adPositionId 广告位Id
  */
public LMBannerAdView(@NonNull Context context, @NonNull String adPositionId) 

/**
  * 设置轮播时间间隔，单位秒，可设置20到200秒
  *
  * @param refresh 秒
  */
public void setRefresh(int refresh)

/**
  * 设置是否显示关闭按钮，默认为false不显示
  *
  * @param showClose true:显示 false:不显示
  */
public void setShowClose(boolean showClose)

 /**
   * 加载广告
   */
public void loadAd()
    
```

---

###  添加插屏广告
####  创建插屏广告对象
 ```java
 LMInterstitialAd lmInterstitialAd = new LMInterstitialAd(this, "4000061_374");
 ```
 
####  加载广告并在广告准备就绪后显示广告

方法：

```java
public void loadAd()
public void show()
public void showAsPopWindow()
public void closePopupWindow() 
```

示例：
 
```java
LMInterstitialAd lmInterstitialAd = new LMInterstitialAd(MainActivity.this, "4000061_374");
lmInterstitialAd.loadAd();
lmInterstitialAd.setOnAdStatusListener(new OnAdStatusListener() {

       @Override
       public void onReady() {
          super.onReady();
          lmInterstitialAd.showAsPopWindow();

       @Override
       public void onExposure(AdInfo adInfo) {
          super.onExposure(adInfo);
       }
 });
```

#### 参数说明

```java
 /**
   * 加载广告
   */
 public void loadAd()

 /**
  * 显示插屏广告，背景为灰色透明
  */
 public void show()
 
 /**
  * 显示插屏广告，背景为透明
  */
 public void showAsPopWindow()
 
 /**
  * 关闭背景为透明的插屏广告
  */
 public void closePopupWindow()

```

> 提示：监听广告状态，只有在onReady()回调监听被调用后才可显示插屏广告，增强用户体验！

---
###  添加开屏广告
####  创建开屏广告对象
 ```java
LMSplashAd lmSplashAd = new LMSplashAd(this, "4000061_375", container, skip, this);
 ```
 
####  加载广告并在广告准备就绪后显示广告

方法：

```java
public LMSplashAd(Context context, String adPositionId,
                      ViewGroup adContainer, View skipView, @NonNull OnSplashAdListener onSplashAdListener)

```

示例：
 
```java
 // 标识是否可以跳到app主页面
private boolean canJump = false;

@Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        setTheme(R.style.AppTheme);
        super.onCreate(savedInstanceState);
        // 因广告请求需要imei号，请先获取后再请求广告！！！
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.READ_PHONE_STATE) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.READ_PHONE_STATE, Manifest.permission.WRITE_EXTERNAL_STORAGE}, 1000);
        }
        setContentView(R.layout.activity_splash);
        container = this.findViewById(R.id.splash_container);
        skip = findViewById(R.id.skip);
        launch_default = findViewById(R.id.launch_default);
        lmSplashAd = new LMSplashAd(this, "4000061_375", container, skip, this);
    }
  @Override
    protected void onResume() {
        super.onResume();
        if (canJump) {
            gotoMain();
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
    }

    @Override
    public void onClick(AdInfo adInfo) {
        Log.i("LinkedME", "开屏广告被点击！");
        canJump = true;
    }

    @Override
    public void onGetAd(boolean status) {
        if (status) {
            Log.i("LinkedME", "开屏广告有可展示广告！");
        } else {
            Log.i("LinkedME", "开屏广告无可展示广告！");
            gotoMain();
        }
    }

    @Override
    public void onClose() {
        Log.i("LinkedME", "开屏广告被关闭！");
        gotoMain();
    }

    @Override
    public void onExposure(AdInfo adInfo) {
        Log.i("LinkedME", "开屏广告曝光！");
    }

    @Override
    public void onReady() {
        Log.i("LinkedME", "开屏广告已准备就绪，可以显示！");
        // 开屏广告准备就绪后，需要将启动欢迎页面隐藏掉以显示开屏广告
        launch_default.setVisibility(View.GONE);
    }

    @Override
    public void onTick(long millis) {
        Log.i("LinkedME", "开屏广告倒计时: " + Math.round(millis / 1000f));
        skip.setText(String.format(SKIP_TEXT, Math.round(millis / 1000f)));
    }

    private void gotoMain() {
        Intent intent = new Intent(SplashActivity.this, MainActivity.class);
        startActivity(intent);
        finish();
    }
```

#### 参数说明

```java
public interface OnSplashAdListener {
    /**
     * 广告点击时回调
     *
     * @param adInfo 广告信息
     */
    void onClick(AdInfo adInfo);

    /**
     * 是否有匹配广告，用户可根据该状态判断是否显示LinkedME广告还是显示其他广告
     *
     * @param status true:有匹配广告  false:无可显示的广告
     */
    void onGetAd(boolean status);

    /**
     * 广告被关闭时回调
     */
    void onClose();

    /**
     * 广告曝光时回调
     */
    void onExposure(AdInfo adInfo);

    /**
     * 开屏广告准备好后调用
     */
    void onReady();

    /**
     * 广告倒计时回调
     *
     * @param millis 返回广告还将被展示的剩余时间，单位是ms
     */
    void onTick(long millis);
}

```

> 提示：因广告请求需要imei号，请先获取后再请求广告

---

###  原生广告
####  添加原生广告视图
 ```xml
  <cc.lkme.linkactive.view.LMADContainer
           android:id="@+id/lm_ad_container"
           android:layout_width="match_parent"
           android:background="@color/colorPrimary"
           android:layout_height="wrap_content"
           android:visibility="gone"
           >
       <!--用户自定义广告视图嵌入到LMADContainer视图中-->
       
   </cc.lkme.linkactive.view.LMADContainer>
 ```
 
####  获取广告数据并自定义广告展现形式

方法：

```java
public void getAd(String adPositionId, OnAdStatusListener onAdStatusListener)
```

示例：
 
```java
 lm_ad_container.getAd("4000061_374", new OnAdStatusListener() {
    @Override
     public void onGetAd(boolean status, AdInfo adInfo) {
        if (status) {
         // 调用方法显示广告视图
          lm_ad_container.setAdVisibility(true);
        // 以下处理自定义视图展示
        // 标题
          adInfo.getTitle();
          // 副标题
          adInfo.getSub_title();
          // 内容
          adInfo.getContent();
          // 图片列表
          adInfo.getImgs();
          // 图片列表中的第一张图片
          adInfo.getImg_url();
          } else {
        // 无广告，不展示
            }
        }
});
```

#### 参数说明

```java
 /**
   * <p>为该广告设置广告id，获取广告数据</p>
   *
   * @param adPositionId       广告id
   * @param onAdStatusListener 广告状态监听
   */
 public void getAd(String adPositionId, OnAdStatusListener onAdStatusListener) 

```

---





