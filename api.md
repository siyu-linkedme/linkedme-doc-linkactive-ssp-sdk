# LinkActive API对接开发文档

**本文档面向LinkActive媒体方，通过调用本文档中的接口来请求拉活的广告及返回广告效果数据。调用文档接口前，请先联系LinkedME，获取媒体方唯一的linkedme_key。**

联系邮箱：support@linkedme.cc


-------

## 获取广告接口
* http://a.lkme.cc/ad/openapi/get_ad

* method: `GET`

* description
  获取广告接口

* 参数说明


|参数	|类型	|是否必填 | 描述|
|---|---|---|---|
|idfa|	String|	iOS必填 | iOS设备标识，原值|
|android_id|	String|	Android必填 | 设备AndroidID，原值|
|imei|	String|	Android必填 | 设备imei号，原值|
|linkedme_key|	String|	必填 | 标识媒体方，LinkedME提供|
|ad_position|	String|	必填 | 标识媒体方的广告位，LinkedME提供|
|os|	String|	必填 | 操作系统，iOS或者Android|
|os_version|	String|	可选 | 操作系统版本|
|mac|	String|	可选 | mac地址|
|device_model|	String|	可选 | 设备型号|
|scrro|	int|	可选 | 屏幕方向<br>1: 竖屏<br>2: 横屏|
|scrwidth|	int|	可选 | 设备物理地址，原值|
|scrheight|	int|	可选 | 操作系统版本|
|ip|	String|	可选 | 用户的ip地址，便于获取个性化广告|
|carrier|	int|	可选 | 运营商<br>0:unknown<br>1:中国移动<br>2:中国联通<br>3:中国电信|
|net|	int|	可选 | 联网方式<br>0:unknown<br>1:wifi<br>2:2G<br>3:3G<br>4:4G|
|ua|	String|	可选 | 用户浏览器的user-agent|
|app_version|	String|	可选 | App版本|
|tags|	String|	可选 | 个性化广告标签|
|lng|	String|	可选 | 经度，十进制保留6位小数，<br>西经用负数表示|
|lat|	String|	可选 | 纬度，十进制保留6位小数，<br>南纬用负数表示|
|timestamp|	long|	必填 | 时间戳，自1970年起的毫秒数|
|retry_times|	int|	必填 | 重试次数，默认为0|

* 调用示例



```
http://a.lkme.cc/ad/openapi/get_ad?android_id=&imei=354707046861622&linkedme_key=7e289a2484f4368dbafbd1e5c7d06903&ad_position=11111_0&tags=&lng=-1&lat=-1&device_model=Nexus+5&app_version=1.0.8&carrier=1&net=1&ua=Mozilla%2F5.0+%28Linux%3B+Android+6.0.1%3B+OPPO+R9s+Plus+Build%2FMMB29M%3B+wv%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Version%2F4.0+Chrome%2F46.0.2490.76+Mobile+Safari%2F537.36&os=Android&os_version=6.0.1&retry_times=0&timestamp=1484560913242
```



* response

iOS

```
[{
    "ad_code": "xx", //[String]广告ID
    "ad_position": "xx", //[String]广告位ID
    "uri_scheme": "xx", //[String] 广告主App的uri_scheme，通过此值来唤起App
    "appstore_url": "xx", //[String] App下载地址
    "img_url":"xx", //[String]广告图片素材地址
    "h5_url":"xx",  //[String]广告内容页的h5地址或者引导用户下载的页面地址
    "active_device_type": "idfa"; //[String]获取到广告的id类型,idfa或androidId或imei，iOS为默认值idfa
    "ad_content": {
        "title":"xx",   //广告标题
        "sub_title":"", //广告副标题
        "content":"xx"  //广告内容
    },
    "request_id":"xx"   //追踪广告行为
}
...
]
```

Android

```
[{
    "ad_code": "xx", //[String]广告ID
    "ad_position": "xx", //[String]广告位id
    "uri_scheme": "xx", //[String] 广告主App的uri_scheme，通过此值来唤起App
    "pkg_name": "xx", //[String]广告主App的包名，用来判断广告主App是否安装了
    "img_url":"xx", //[String]广告图片素材地址
    "h5_url":"xx",  //[String]广告内容页的h5地址或者引导用户下载的页面地址
    "apk_url":"xx", //[String]apk包的下载地址
    "active_device_type": "xx"; //[String]获取到广告的设备id类型,idfa或androidId或imei，Android系统为androidId或者imei
    "ad_content": {
        "title":"xx",   //广告标题
        "sub_title":"", //广告副标题
        "content":"xx"  //广告内容
    },
    "request_id":"xx"   //追踪广告行为
 },
...
]
```

##回调广告行为
* http://a.lkme.cc/ad/openapi/record_status

* method: `GET`

* description 
回调广告行为；媒体方获取广告后，用户对广告的行为，媒体方需要告知LinkedME。

* 参数说明

|参数	|类型	|   是否必填|描述|
|---|---|---|---|
|idfa|	String|	iOS必填| iOS设备标识，原值|
|android_id|	String|	Android必填|	设备AndroidID，原值|
|imei|	String|	Android必填|	设备imei，原值|
|linkedme_key|	String|	必填|	媒体方标识，与get_ad接口<br>里的linkedme_key值一致|
|ad_position|	String|	必填|	媒体方广告位ID，LinkedME提供，<br>与get_ad接口里的ad_position值一致|
|ad_code|	String|	必填|   广告ID，其值为get_ad接口返回结果<br>里的ad_code的值|
|os|	String|	必填|	操作系统,iOS或者Android|
|active_device_type|String|	必填|用哪个设备id提活的广告，</br>其值为get_ad接口返回结果<br>里的active_device_type值|
|status|	int|	必填|	广告行为，其值为11，12，13，14，15</br>11:展示广告</br>12:点击广告</br>13:唤起了App</br>14:点击之后没有唤起App<br>15:点击之后去下载了APP|
|request_id|	String|	必填|	追踪广告行为，其值为get_ad接口<br>返回结果里的request_id的值|
|timestamp|	long|	必填|	时间戳，自1970年起的毫秒数|
|retry_times|	int|	必填|	重试次数，默认为0|
|ip|	String|	可选 | 用户的ip地址| 

* 调用示例



```
http://a.lkme.cc/ad/openapi/record_status?imei=863267033980153&linkedme_key=7e289a2484f4368dbafbd1e5c7d06903&ad_position=11111_0&os=Android&ad_code=11102_0&active_device_type=imei&status=12&request_id=c2e2849252a906b8816ab12fc5cf8bf7&timestamp=1487759017154
```



* response

```
{
    "res":"ok"
}
```



