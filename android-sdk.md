## Android集成文档

### 1.引入jar包并依赖
将jar包复制到项目libs目录下，并添加到项目Module层的build.gradle依赖中，如下示例：

```groovy
dependencies {
  //注意修改jar包名
  compile files('libs/LinkedME-Active-SDK-V1.0.3.jar')
}
```

### 2.配置AndroidManifest.xml文件
#### 2.1 配置点击广告后显示h5页面Activity
```xml
<activity
    android:name="cc.lkme.linkactive.view.LMH5Activity"
    android:configChanges="orientation|keyboardHidden|navigation|screenSize"
    android:exported="false"
    android:screenOrientation="behind"
    android:windowSoftInputMode="adjustResize|stateHidden"/>

```
#### 2.2 配置点击广告后下载apk的Service

```xml
 <service
    android:name="cc.lkme.linkactive.network.LMDownloadService"
    android:exported="false"/>
```
> apk下载存储位置为公共Download文件夹，因Android 7.0系统权限更改，需要通过FileProvider来获取Download文件夹中指定文件的Uri。因此需要添加FileProvider相关配置，具体配置参考官方文档：[Setting Up File Sharing](https://developer.android.com/training/secure-file-sharing/setup-sharing.html)。
> FileProvider配置完成后，需要在FileProvider的xml文件配置中添加\<external-path/>子节点，用于访问Download文件夹中的文件，如下所示：
> 
```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path name="external_files" path="/"/>
</paths>
```

#### 2.3 配置点击广告下载apk安装后的监听
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
### 3.初始化SDK

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
##### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| context | Application Context  | Application 上下文 |
| linkedme_key |LinkedME key |后台Link Active分配的key|
---


### 4.添加广告
广告分两种展现形式：
1. Banner广告，可显示多个广告，同时按一定的时间间隔自动轮播；
2. 插屏广告，显示一个大尺寸广告；
#### 4.1 添加Banner广告
##### 4.1.1 在需要显示Banner广告的布局文件中添加广告视图
 ```xml
  <cc.lkme.linkactive.view.LMBannerView
      android:id="@+id/lm_banner"
      android:layout_width="match_parent"
      android:layout_height="60dp"
      android:visibility="gone"/>
       
 ```
 
##### 4.1.2 加载广告

描述：

- 获取广告，通过传入广告位id获取广告；
- 重写onPause()及onResume()方法，在未处于前台页面时停止轮播。

方法：

```java
public void getAdWithFrame(String ad_position_id, String latitude, String longitude, OnAdStatusListener onAdStatusListener) 
public void lmAdOnResume()
public void lmAdOnPause() 
```

示例：
 
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  LMBannerView lm_banner = (LMBannerView) findViewById(R.id.lm_banner);
  lm_banner.getAdWithFrame("分配的广告位id", new OnAdStatusListener() {
            @Override
            public void onGetAd(boolean status) {
                // 是否有广告可显示，true：有 false：无
            }

            @Override
            public void onClick(AdInfo adInfo) {
                // 哪一个广告被点击
            }

            @Override
            public void onClose() {
                // 广告被关闭
            }
        });
 }


 @Override
 protected void onPause() {
     super.onPause();
     lm_banner.lmAdOnPause();
 }

 @Override
 protected void onResume() {
     super.onResume();
     lm_banner.lmAdOnResume();
 }
```

##### 参数说明

```java
/**
  * 为该Banner广告设置广告位id，并添加地理位置信息，自动加载显示广告
  */
public void getAdWithFrame(String ad_position_id, String latitude, String longitude, OnAdStatusListener onAdStatusListener) 
```
| 参数 | 说明 | 备注 |
| --- | --- | --- |
| ad_position_id | 广告id |  |
| latitude |纬度 |  |
| longitude |经度 |  |
| onAdStatusListener |广告状态监听 |  |
---

#### 4.2 添加插屏广告
##### 4.2.1 在需要显示插屏广告的布局文件中添加广告视图
 ```xml
 <RelativeLayout>
 ...
 
  <cc.lkme.linkactive.view.LMFloatingView
      android:id="@+id/lm_floating_view"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:padding="16dp"
      android:background="#33000000"
      android:layout_centerInParent="true"
      android:visibility="gone"/>
      
...
</RelativeLayout>
 ```
 
##### 4.2.2 加载广告

描述：

- 获取插屏广告，通过传入广告位id获取广告；
- 在适当的时刻显示插屏广告。

方法：

```java
public void getAdWithFrame(String ad_position_id, String latitude, String longitude, OnAdStatusListener onAdStatusListener) 
public void isReady()
```

示例：
 
```java
@Override
protected void onCreate(Bundle savedInstanceState) {

LMFloatingView lm_floating_view = (LMFloatingView) findViewById(R.id.lm_floating_view);
lm_floating_view.getAdWithFrame("分配的广告位id", new OnAdStatusListener() {

    @Override
    public void onGetAd(boolean status) {
    // 是否有广告可显示，true：有 false：无
        if (status) {
            Log.d(LinkedME.TAG, "存在匹配广告，可显示插屏广告");
        } else {
            Log.d(LinkedME.TAG, "无匹配广告，没有可显示的插屏广告");
        }
    }

    @Override
    public void onClick(AdInfo adInfo) {
        // 一个广告被点击
    }

    @Override
    public void onClose() {
        // 广告被关闭
    }
});
        
Button ad_full_view = (Button) findViewById(R.id.ad_full_view);
   ad_full_view.setOnClickListener(new View.OnClickListener() {
       @Override
       public void onClick(View view) {
           if (lm_floating_view.isReady()) {
               // 插屏广告需要判断图片是否已缓存，缓存后再显示插屏广告
               lm_floating_view.setVisibility(View.VISIBLE);
           }
       }
   });
        
 }
```

##### 参数说明

```java
/**
  * 为该插屏广告设置广告位id，并添加地理位置信息，自动加载显示广告
  */
public void getAdWithFrame(String ad_position_id, String latitude, String longitude, OnAdStatusListener onAdStatusListener) 
```
| 参数 | 说明 | 备注 |
| --- | --- | --- |
| ad_position_id | 广告id |  |
| latitude |纬度 |  |
| longitude |经度 |  |
| onAdStatusListener |广告状态监听 |  |

```java
/**
  * 判断插屏广告图片是否已准备就绪（已缓存）
  */
public void isReady() 

```

---

#### 4.3.附加功能
描述：

- 可控制close按钮的大小及是否显示

方法：

```java
public void enableCloseAd(boolean enableCloseAd)
public void setCloseButtonRadius(@Dimension int radius)
```

示例：
 
```java
@Override
protected void onCreate(Bundle savedInstanceState) {

  // 设置显示close按钮，Banner广告默认不显示，插屏广告默认显示
  lm_banner.enableCloseAd(true);
  // 设置close按钮半径的大小，默认8dp，Banner广告及插屏广告均可设置
  lm_banner.setCloseButtonRadius(8);

 }
```

##### 参数说明

```java
/**
  * 是否显示close按钮，允许用户关闭广告，默认不显示
  */
public void enableCloseAd(boolean enableCloseAd)
```
| 参数 | 说明 | 备注 |
| --- | --- | --- |
| enableCloseAd | true:显示关闭按钮，允许用户关闭广告 <br />  false：不显示关闭按钮，不允许用户关闭广告 |  |

```java
/**
 * 设置关闭按钮的大小
 */
public void setCloseButtonRadius(@Dimension int radius) 
```
| 参数 | 说明 | 备注 |
| --- | --- | --- |
| radius | 关闭按钮的半径，单位dp |  |

---
