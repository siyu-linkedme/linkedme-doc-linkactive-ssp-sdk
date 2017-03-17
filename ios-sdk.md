# iOS平台SDK集成文档

##添加LinkedME_Key

1.打开info.plist文件
2.在列表中点击右键选择add row添加一个分组
3.创建一个新的item名称为linkedme_key，类型为Dictionary
4.在linkedme_key新增一个字符串类型的item, live字段，其值到后台“设置”->“应用”中进行查看(LinkedME Key的值)

### 在Appdelegate中导入头文件并初始化SDK

```
 #import "LinkedME.h"
```


初始化SDK
LinkedME* linkedme = [LinkedME getInstance];

## 添加Framework

1.CoreSpotlight.framework(status:Optional)
2.SystemConfiguration.framework
3.Security.framework
4.AdSupport.framework
5.StoreKit.framework

>  CoreSpotlight.framework标记为可选。

## 加载广告视图

### 在需要加载广告的ViewController中导入头文件

```
#import "LMActivity.h"
```
 
### 加载广告
 
```
 LinkAdView *ad = [LinkAdView getAdWithFrame:CGRectMake(0, 200, 450, 100) withAdid:8005 
 tags:@"" statusCallBack:^(BOOL status, NSArray *content) {
        //加载广告并回调广告状态（是否显示广告）
        if (!status) {
            //广告加载失败，这里处理显示其他广告的代码
        }
        NSLog(@"%d",status);
    }];
```



### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| status | 广告状态，当前是否有广告；</br>YES为有广告，NO无广告 |   |
| content | 广告内容，如标题副标题等信息|   |
| tags | 个性化广告需求;</br>向服务器发送希望的广告内容或者类型;</br>用逗号分隔，没有可不填|如a,b,c|
| adid | 广告位信息，根据Adid返回对应的广告素材和内容|LinkedME提供   |
