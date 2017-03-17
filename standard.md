# 需求说明

媒体方按照该文档接入LinkActive平台，主要“拉活”各行业TOP5的APP，给用户提供良好的跳转体验，和知名品牌合作，最终也获得较好的经济收益。

拉活各方面的监控数据必须真实，实时的返回LinkActive平台，该数据将作为最终结算的参考依据。

# 接入方式及优劣
媒体方接入LinkActive可以通过SDK或者API来实现。


|接入方式|优势|劣势|
|---|---|---|
|SDK接入|标准接入流程，SDK实现全部逻辑，不需要客户端做太多处理，数据准确度高|不够灵活，占用一定的体积，需要重新发版|
|API接入|接入灵活，不需要重新发版|需要媒体方判断用户的手机上是否安装了广告主的App；两方联调成本高|

SDK和API接入的形式各有优劣，我们建议您使用SDK方式接入。

# SDK接入
## SDK接入流程

![](/assets/SDK-1.png)

## SDK接入文档
查看SDK接入文档：https://www.linkedme.cc/docs/activesdk.html

## SDK接入注意事项
接入前请向LinkedME要SDK包，以及linkedme_key值和ad_position的值。

# API接入
## API接入前提
<font color="red">对于Android平台</font>，如果您想要采用API接入，那么您的APP<font color="red">需要满足两个条件</font>：

1）能检测广告主的APP是否已安装；因为只有安装了的用户才展示广告，点击广告能唤起APP，唤起APP后才能有收益；  
2）能获取到唤起广告主APP的状态，并回传给LinkedActive；因为这是结算依据。
理论上来讲，如果第一个条件满足了（能准确判断广告主的APP是否安装），第二个条件也满足了，因为只要点击了广告，一定能唤起广告主的APP。

<font color="red">注意事项 1</font>：  
如果以上两个条件不满足，需要客户端发版支持；如果媒体方有其他方式知道用户的手机上是否安装了广告主的APP，也可以不用发版。

<font color="red">注意事项 2</font>：  
为了您的用户有更好的体验，客户端需要接收三个参数，uri_scheme、h5地址（可能是对应APP内容页的h5地址或引导用户下载的页面地址）和apk下载地址，先通过pkg_name判断是否安装，如果安装了则展示广告；如果没有安装，则可以做两种处理：  
1）不展示广告；  
2）展示广告，用户点击后跳转到h5地址(建议同时获取apk下载地址开启后台下载apk），h5地址可能是一个引导用户下载apk的地址，若h5地址是在APP内WebView中打开，需要注意处理点击h5页面内apk下载链接的情况；若在外置浏览器中打开则无需处理。  
目前我们可以只做第一种处理；第二种处理的逻辑可以先埋进去，方便以后做用户拉新。

<font color="red">对于iOS平台</font>，如果您想要采用API接入，那么您的APP<font color="red">需要满足两个条件</font>：  
1）把广告主的Url Schemes写入配置文件（为了判断广告主的APP是否安装）；只有安装了广告的APP才展示广告，点击广告能唤起APP，唤起APP后才能有收益；(图4.1)  
2）能获取到唤起广告主APP的状态，并回传给LinkActive；因为这是结算依据。

![](/assets/WX20170317-140024@2x.png)

<font color="red">注意事项 1</font>：  
如果以上两个条件不满足，需要客户端做出相应修改，并在AppStore中更新版本。

<font color="red">注意事项 2</font>：  
为了您的用户有更好的体验，客户端需要接收三个参数，uri_scheme、h5地址（对应APP内容页的h5地址，或者活动地址等）和下载地址（AppStore下载地址），先通过scheme判断是否安装，如果安装了则展示广告；如果没有安装，则可以做两种处理：  
1）不展示广告；  
2）展示广告，用户点击后跳转到h5地址(然后立即跳转到AppStore，这样用户点击返回，还可以看到h5的内容页)；  
目前我们可以只做第一种处理；第二种处理的逻辑可以先埋进去，方便以后做用户拉新。

## API接入流程

![](/assets/API-1.png)

## API接入文档
查看API接入文档：https://www.linkedme.cc/docs/activeapi.html
## 客户端处理逻辑
<font color="red">Android端处理逻辑</font>：
1. 调用“/ad/openapi/get_ad”接口获取广告列表数据，LinkedME可能返回多条广告；获取数据后，根据pkg_name逐条判断应用是否已安装，直到获取第一条已安装广告数据，显示广告，若均未安装则不展示广告。
2. 用户点击广告唤起APP
  当用户点击广告时，通过uri scheme唤起APP(如果第一步展示了未安装的APP广告，跳转到h5地址，建议同时后台下载apk包)。
3. 调用“/ad/openapi/record_status”接口向LinkedME服务器发送广告行为通知。

<font color="red">iOS端处理逻辑</font>：
1. 把广告主的Url Schemes写入配置文件（为了判断广告主的APP是否安装）
2. 调用“/ad/openapi/get_ad”接口获取广告列表数据，LinkedME可能返回多条广告；获取数据后，根据scheme逐条判断应用是否已安装，直到获取第一条已安装广告数据，显示广告，若均未安装则不展示广告。
3. 用户点击广告，通过scheme唤起APP(如果第二步展示了未安装的APP广告，点击后跳转到AppStore；
4. 调用“/ad/openapi/record_status”接口向LinkedME服务器发送广告行为通知。

<font color="red">发送回调通知说明</font>：  
1. 展示广告，status值为11  
2. 点击广告，status值为12  
3. 唤起APP，status值为13  
4. 点击广告，没有唤起APP，status值为14  
5. 点击广告，去下载APP，status值为15


## API接入注意事项
**1，接入准备**  
接入前需要先获取linkedme_key和ad_position，这两个字段在接口调用时来标记当前app及广告位。由于当前没有配置后台，请联系LinkedME获取。

**2，联调需要准备哪些内容**  
联调请准备您测试设备的设备ID（Android平台为imei，iOS平台为idfa）并提供广告位的尺寸标注示例图；联调时间请与LinkedME协商。

**3，点击是否就是唤起**  
<font color="red">Android平台</font>，要求根据包名检测APP的安装状态，展示广告则一定是APP已安装，点击后一定会唤起APP。  
<font color="red">iOS平台</font>，需要在配置文件里加入广告主的Url Schemes，才能判断广告主的APP是否安装，如果媒体方加入了配置，并且写了判断是否安装的逻辑，那么展示的广告一定是已安装的，点击一定能唤起APP。

**4，广告列表的顺序**  
LinkActive平台返回的是多个广告，并且广告是有一定优先级的，所以不能对广告列表的顺序进行更改。

**5，建议API的调用放在服务端，方便后期优化升级**  
如果API的调用放在客户端，每次改动都需要客户端发版。并且建议API的调用添加开关。