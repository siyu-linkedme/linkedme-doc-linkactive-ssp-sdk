### 1.引入jar包并依赖
将jar包复制到项目libs目录下，并添加到项目Module层的build.gradle依赖中，如下示例：

```
dependencies {
  //注意修改jar包名
  compile files('libs/LinkedME-Active-SDK-V1.0.1.jar')
}
```

### 2.配置AndroidManifest.xml文件
#### 2.1 添加LinkedME Key
在AndroidManifest.xml文件中添加LinkedME_Key，如下示例：  

```
<application
  android:name=".activity.LinkedMEDemoApp">
  <!-- LinkedME官网注册应用后,从"设置"页面获取该Key -->
  <meta-data
    android:name="linkedme.sdk.key"
    android:value="替换为后台设置页面中的LinkedME Key" />
</application>
```
#### 2.2 配置点击广告后显示h5页面Activity
```
<activity
    android:name="cc.lkme.linkactive.view.LMH5Activity"
    android:configChanges="orientation|keyboardHidden|navigation|screenSize"
    android:exported="false"
    android:screenOrientation="behind"
    android:windowSoftInputMode="adjustResize|stateHidden"/>

```
#### 2.3 配置点击广告后下载apk的Service
```
 <service
    android:name="cc.lkme.linkactive.network.LMDownloadService"
    android:exported="false"/>
```
> apk下载存储位置为公共Download文件夹，因Android 7.0系统权限更改，需要通过FileProvider来获取Download文件夹中指定文件的Uri。因此需要添加FileProvider相关配置，具体配置参考官方文档：[Setting Up File Sharing](https://developer.android.com/training/secure-file-sharing/setup-sharing.html)。
> FileProvider配置完成后，需要在FileProvider的xml文件配置中添加\<external-path/>子节点，用于访问Download文件夹中的文件，如下所示：
> 
```
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path name="external_files" path="/"/>
</paths>
```

### 3.初始化SDK
```
if (BuildConfig.DEBUG) {
    //设置debug模式下打印LinkedME日志
    LinkedME.getInstance(this).setDebug();
} else {
    LinkedME.getInstance(this);
}
```

### 4.添加广告视图
#### 4.1 在需要显示广告的布局文件中添加广告视图
 ```
  <cc.lkme.linkactive.view.LMImageView
       android:id="@+id/lm_ad"
       android:layout_width="match_parent"
       android:layout_height="100dp"/>
       
 ```
 
#### 4.2 加载广告
```
LMImageView lm_ad = (LMImageView) findViewById(R.id.lm_ad);
lm_ad.getAdWithFrame("分配的广告id", "用户标签1,用户标签2", new OnGetAdListener() {
    @Override
    public void statusCallBack(boolean status, Map<String, String> content) {
        //成功加载并显示广告
        if(!status){
            //无广告可显示或加载失败，可显示其他广告
        }
        if (content != null){
            //广告内容，如title，subtitle
        }
    }
});
```

### 5. 方法说明
```
/**
 * <p>为该LMImageView设置广告id，并添加用户标签及地理位置信息，自动加载显示广告</p>
 *
 * @param adId      分配的广告id
 * @param tags      标签，向服务器发送希望的广告内容或者类型，用逗号分隔，没有可不填
 * @param latitude  纬度，可选项
 * @param longitude 经度，可选项
 * @param onGetAdListener 广告加载结果回调
 */
public void getAdWithFrame(String adId, String tags, String latitude, String longitude, OnGetAdListener onGetAdListener) 
```
