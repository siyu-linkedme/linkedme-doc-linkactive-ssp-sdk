## iOS集成文档


### 1.在需要加载广告的ViewController中导入头文件
    #import "LMManger.h"
    #import "LMSpot.h"
    #import "LMBannerView.h"

###1.1 设置LinkedME_Key

描述：

- 注册LinkActive并在后台获取LinkedME_key。

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
| linkedme_key | LinkedME key  |  |
---


###2.1 添加Banner广告

描述：

- 请求并加载Banner广告。

方法：

```swift
- (void)showBanner :(UIViewController *)controller frame:(CGRect)rect withAdPosition:(NSString *)ad_position;
```

示例：
 
```swift
LMBannerView *banner = [LMBannerView new];
CGRect rect = CGRectMake(0, [UIScreen mainScreen].bounds.size.height - 60, [UIScreen mainScreen].bounds.size.width, 60);
[banner showBanner:self frame:rect withAdPosition:@"4000036_112"];
```
#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| rect | Banner广告frame设置  |  |
| controller | Banner广告加载在那个视图控制器中  |  |
| ad_position | 广告位ID|在LinkActive后台获取|

---


###2.2 添加插屏广告

描述：

- 开始请求插屏广告

方法：

```swift
+ (void)requestSpotDatawithAdposition:(NSString *)ad_position callback:(PosCompleteBlock)block;
```

示例：
 
```swift
 [LMSpot requestSpotDatawithAdposition:@"4000036_112" callback:^(BOOL isSucceed, NSError *error) {
        [LMSpot showSpotWithCompleteBlock:^(BOOL isSucceed, NSError *error) {
            
        }];
    }];  
```



#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| ad_position | 广告位ID|在LinkActive后台获取|
| block | 广告是否获取成功  |  广告数据获取状态|
---

描述：

- 显示插屏广告

方法：

```swift
+ (void)showSpotWithCompleteBlock:(PosCompleteBlock)block;
```

示例：
 
```swift
 [LMSpot requestSpotDatawithAdposition:@"4000036_112" callback:^(BOOL isSucceed, NSError *error) {
        [LMSpot showSpotWithCompleteBlock:^(BOOL isSucceed, NSError *error) {
            
        }];
    }];  
```

#### 参数说明

| 参数 | 说明 | 备注 |
| --- | --- | --- |
| block | 广告加载状态 |成功/失败   |
---