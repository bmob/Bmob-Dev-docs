## AndroidSDK错误码列表

|错误码      |    内容 | 含义  |
|:-------- |:--------| :-- |
|9001|AppKey is Null, Please initialize BmobSDK. |Application Id为空，请初始化.|
|9002|Parse data error|解析返回数据出错|
|9003|upload file error|上传文件出错|
|9004|upload file failure|文件上传失败|
|9005|A batch operation can not be more than 50|批量操作只支持最多50条|
|9006|objectId is null|objectId为空|
|9007|BmobFile File size must be less than 10M.|文件大小超过10M|
|9008|BmobFile File does not exist.|上传文件不存在|
|9009|No cache data.|没有缓存数据|
|9010|The network is not normal.(Time out)|网络超时|
|9011|BmobUser does not support batch operations.|BmobUser类不支持批量操作|
|9012|context is null.|上下文为空|
|9013|BmobObject Object names(database table name) format is not correct.|BmobObject（数据表名称）格式不正确|
|9014|第三方账号授权失败|第三方账号授权失败|
|9015|其他错误均返回此code|其他错误均返回此code|
|9016|The network is not available,please check your network!|无网络连接，请检查您的手机网络.|
|9017|与第三方登录有关的错误，具体请看对应的错误描述|与第三方登录有关的错误，具体请看对应的错误描述|
|9018|参数不能为空|参数不能为空|
|9019|格式不正确：手机号码、邮箱地址、验证码|格式不正确：手机号码、邮箱地址、验证码|
|9020|保存CDN信息失败|保存CDN信息失败|
|9021|permission not defined.You must write android.permission.WAKE_LOCK in AndroidManifest.xml "<uses-permission android:name="android.permission.WAKE_LOCK". |文件上传缺少wakelock权限|
|9022|upload failure,please retry.|文件上传失败，请重新上传|
|9023|please call Bmob.initialize to init sdk.|请调用Bmob类的initialize方法去初始化SDK|

## RESTAPI错误码列表
|HttpResponseCode|错误码|含义|
|------|:---------|:---------|
|401||unauthorized|
|500||It is busy...Try it later!
当HttpResponseCode的值为401或500时，接口返回的内容格式如下：

```JSON
{
    "error": "unauthorized"
}
```
error为上表中含义的值

当HttpResponseCode的值为400时，接口返回的内容格式如下：

```JSON
{
    "code": 101,
    "error": "object not found for e1kXT22L"
}
```

code为下表中的错误码，error为下表中的内容
以下是HttpResponseCode为404时返回内容的详细说明，信息中的%s，%d，%f将替换为详细的信息或具体的值。

|错误码|内容|含义|
|:--|:--|:--|
|101|object not found for %s. OR username or password incorrect|查询的 对象或Class 不存在 或者 登录接口的用户名或密码不正确|
|102|Invalid key '%s' for find OR Invalid value for key '%s'. OR %s: invalid geopoint object.|查询中的字段名是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。，或查询对应的字段值不匹配，或提供的地理位置格式不正确|
|103|objectId required. OR classname '%s' must start with a letter.|查询单个对象或更新对象时必须提供objectId 或 非法的 class 名称，class 名称是大小写敏感的，并且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线.|
|104|relation className '%s' not exists.|关联的class名称不存在|
|105|invalid field name: %s. OR It is a reserved field: %s.|字段名是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线 或 字段名是[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")默认保留的，如objectId,createdAt,updateAt,ACL|
|106|%s: is not a valid Pointer.|不是一个正确的指针类型|
|107|invalid json: %s.|输入的json不是正确的json格式|
|107|This endpoint only supports Content-Type: application/json requests, not %s.|请求只支持Header头部Content-Type值为application/json或application/json; charset=utf-8
|
|107|invalid date: %s.|时间格式不正确|
|107|ACL shoud be like: {"*":{"read":true},"eAfHB29gP9":{"write":true}}.|ACL应该像这样的{"*":{"read":true},"eAfHB29gP9":{"write":true}}|
|107|invalid op value|不是正确的__op的值|
|108|username and password required.|用户名和密码是必需的|
|109|login data required.|登录信息是必需的，如邮箱和密码时缺少其中一个提示此信息|
|111|invalid type for key '%s', expected '%s', but got '%s'.|传入的字段值与字段类型不匹配，期望是这样(%s)的，但传过来却是这样(%s)的|
|112|requests must be an array.|requests的值必须是数组|
|113|every request shoud be an object like:{"method": "POST","path": "/1/classes/GameScore","body": {"score": 1337,"playerName": "Sean Plott"}}|requests数组中每个元素应该是一个像这样子的json对象|
|114|requests array larger than %d|requests数组大于50|
|117|Latitude must be in [-90, 90]: %f. OR Longitude must be in [-180, 180]: %f.|纬度范围在[-90, 90] 或 经度范围在[-180, 180]|
|120|Email verify should be opened in your app setup page of bmob|要使用此功能，请在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")后台应用设置中打开邮箱认证功能开关|
|131|Invalid device token:%s|不正确的deviceToken|
|132|Invalid installation ID:%s|不正确的installationId|
|133|Invalid device type:%s|不正确的deviceType|
|134|device token '%s' already token.|deviceToken已经存在|
|135|installation ID '%s' already token.|installationId已经存在|
|136|%s cannot be changed by this operation OR deviceToken may not be set for deviceType android|只读属性不能修改 或 android设备不需要设置deviceToken|
|138|%s is read only.|表是只读的|
|139|Role names must be restricted to alphanumeric characters, dashes(-), underscores(_), and spaces.|角色名称是大小写敏感的，并且必须以英文字母开头，有效的字符仅限在英文字母、数字、空格、横线以及下划线。|
||role name '%s' already taken.|角色名称已经存在。|
|141|Missing the push data.|缺失推送需要的data参数|
|142|%s shoule be like: 2013-12-04 00:51:13|时间格式应该如下： 2013-12-04 00:51:13|
|143|%s must be a number"|必须是一个数字|
|144|%s cannot before now|不能是之前的时间|
|145|file size error|文件大小错误|
|146|file name error|文件名错误|
|147|file offeset error|文件分页上传偏移量错误|
|148|file ctx error|文件上下文错误|
|149|empty file|空文件|
|150|file upload error|文件上传错误|
|151|file delete error|文件删除错误|
|160|image error|图片错误|
|161|image mode error|图片模式错误|
|162|image width error|图片宽度错误|
|163|image height error|图片高度错误| 
|164|image longEdge error|图片长边错误| 
|165|image shortgEdge error|图片短边错误| 
|201|%s missing|缺失数据|
|202|username '%s' already taken.|用户名已经存在|
|203|email '%s' already taken.|邮箱已经存在|
|204|you must provide an email.|必须提供一个邮箱地址|
|205|no user found with email '%s'.|没有找到此邮件的用户|
||no user found with username '%s'.|没有找到此用户名的用户|
|206|User cannot be altered without sessionToken Error.|登录用户才能修改自己的信息。RestAPI的Http Header中没有提供sessionToken的正确值，不能修改或删除用户|
|207|code error.|验证码错误|
|208|authData error.|authData不正确|
||authData already linked by other user.|authData已经绑定了其他用户账户|
|209|mobilePhoneNumber '%s' already taken.|该手机号码已经存在|
|210|old password incorrect.|旧密码不正确|
|301|%s|验证错误详细提示，如邮箱格式不正确 |
|302|your app setting '%s'.|Bmob后台设置了应用设置值， 如'不允许SDK创建表 '|
|310|%s|云端逻辑运行错误的详细信息|
|311|invalid cloudcode name: %s.|云端逻辑名称是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。|
|401|unique index cannot has duplicate value: %s|唯一键不能存在重复的值|
|402|query where larger than %d bytes.|查询的wher语句长度大于具体多少个字节|
|601|Invalid bql:%s|不正确的BQL查询语句|

## VIP付费限制相关错误码

|错误码|内容|含义|
|:--|:--|:--|
|1002|Tables of the app can create has reached to limit|该应用能创建的表数已达到限制|
|1003|Rows of the table can add has reached to limit|该表的行数已达到限制|
|1004|Fields of the table can add has reached to limit|该表的列数已达到限制|
|1005|Request api of the this month has reached to limit|每月api请求数量已达到限制|
|1006|Crontab jobs of the app has reached to limit|该应用能创建定时任务数已达到限制|
|1007|CloudCodes of the app has reached to limit|该应用能创建云端逻辑数已达到限制|
|1500|The file is larger than file size you can upload|你上传的文件大小已超出限制|


## 短信功能相关错误码

短信功能包含的错误码，除了包括以上的109, 207, 209 状态码外，还定义了以下错误码：

|错误码|内容|含义|
|:--|:--|:--|
|10010|mobile '%s' send message limited.|该手机号发送短信达到限制(对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条)|
|10011|no remaining number for send messages.|该账户无可用的发送短信条数|
|10012|your credit info must verify ok.|身份信息必须审核通过才能使用该功能|
|10013|sms content illegal.|非法短信内容|

