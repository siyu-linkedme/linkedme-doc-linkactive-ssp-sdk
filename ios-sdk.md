## iOS集成文档

### 1.在需要加载广告的ViewController中导入头文件

```
#import "LMManger.h"
#import "LMSpot.h"
#import "LMBannerView.h"
```

### 1.1 设置LinkedME\_Key

描述：

* 注册LinkActive并在后台获取LinkedME\_key。

方法：

```swift
+ (void)setLinkedmeKey:(NSString *)linkedme_key;
```

示例：

```swift
[LMManger setLinkedmeKey:@"5a01adb233bdbfbabc78c2141084e007"];
```

#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| linkedme\_key | LinkedME key |  |

---

### 2.1 添加Banner广告

描述：

* 请求并加载Banner广告。

方法：

```swift
- (void)showBanner :(UIViewController *)controller frame:(CGRect)rect withAdPosition:(NSString *)ad_position userInfo:(NSDictionary *)info isTest:(BOOL)test;
```

示例：

```swift
LMBannerView *banner = [LMBannerView new];
    
NSDictionary * userInfo =  @{@"id":@"linkedme",         //用户标识
                             @"age":@"19900101",        //出生年月日
                             @"gender":@"M",            //性别
                             @"user_tag":@"美食,技术",    //兴趣，用逗号分隔
                             };
CGRect rect = CGRectMake(0, [UIScreen mainScreen].bounds.size.height - 60, [UIScreen mainScreen].bounds.size.width, 60);
[banner showBanner:self frame:rect withAdPosition:@"4000036_112" userInfo:userInfo isTest:NO];

```

#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| rect | Banner广告frame设置 |  |
| controller | Banner广告加载在那个视图控制器中 |  |
| ad\_position | 广告位ID | 在LinkActive后台获取 |
| userInfo | 用户信息 | 填写用户的基础信息和兴趣信息精准匹配广告 |
| test | 是否使用测试模式 | 测试模式，调用回调接口后不删除该条广告，调试时建议设置为YES，默认为NO<br>YES:debug模式，不删除广告<br>NO:删除广告，下次请求广告不再显示该条广告 |





---

### 2.2 添加插屏广告

描述：

* 开始请求插屏广告

方法：

```swift
+ (void)requestSpotDatawithAdposition:(NSString *)ad_position userInfo:(NSDictionary *)info isTest:(BOOL)test callback:(PosCompleteBlock)block;
```

示例：

```swift
 NSDictionary * userInfo =  @{@"id":@"linkedme",        //用户标识
                             @"age":@"19900101",        //出生年月日
                             @"gender":@"M",            //性别
                             @"user_tag":@"美食,技术",    //兴趣，用逗号分隔
                             };
    
    [LMSpot requestSpotDatawithAdposition:@"4000036_112" userInfo:userInfo isTest:NO callback:^(BOOL isSucceed, NSError *error) {
        [LMSpot showSpotWithCompleteBlock:^(BOOL isSucceed, NSError *error) {
            
        }];
    }];
```

#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| ad\_position | 广告位ID | 在LinkActive后台获取 |
| block | 广告是否获取成功 | 广告数据获取状态 |
| userInfo | 用户信息 | 填写用户的基础信息和兴趣信息精准匹配广告 |
| test | 是否使用测试模式 | 测试模式，调用回调接口后不删除该条广告，调试时建议设置为YES，默认为NO<br>YES:debug模式，不删除广告<br>NO:删除广告，下次请求广告不再显示该条广告 |





---

描述：

* 显示插屏广告

方法：

```swift
+ (void)showSpotWithCompleteBlock:(PosCompleteBlock)block;
```

示例：
```swift
 NSDictionary * userInfo =  @{@"id":@"linkedme",         //用户标识
                             @"age":@"19900101",        //出生年月日
                             @"gender":@"M",            //性别
                             @"user_tag":@"美食,技术",    //兴趣，用逗号分隔
                             };
    
    [LMSpot requestSpotDatawithAdposition:@"4000036_112" userInfo:userInfo isTest:NO callback:^(BOOL isSucceed, NSError *error) {
        [LMSpot showSpotWithCompleteBlock:^(BOOL isSucceed, NSError *error) {
            
        }];
    }];
```



#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| block | 广告加载状态 | 成功/失败 |

---



