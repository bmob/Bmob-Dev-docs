短信服务的API集成在BmobSDK中，因此不熟悉的朋友在使用前先可以了解一下BmobSDK的集成[JS 快速入门](http://docs.bmob.cn/data/JavaScript/a_faststart/doc/index.html)

在一些应用场景下，你可能希望用户验证手机号码后才能进行一些操作，例如充值等。这些操作跟用户系统没有关系，可以通过我们提供的的短信验证API来实现。

每个 [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 帐户有 10 个免费额度的短信数量，超过需要购买短信条数才能继续使用。

为了保障短信的下发速度和送达率，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 为所有用户申请了一致的独享通道，默认使用 **【云验证】** 作为签名，且不可更改。

## 请求发送自定义的短信内容

**使用场景**

 此方法允许开发者使用 Bmob 的短信功能灵活发送自定义的短信内容到用户的手机上，可运用于多种场景下，比如 `发送祝福短信、推送消息、通知`等


**使用前准备**

有两种方式可以使用自定义模板
1.开发者可在Bmob后台提交身份证信息（`Bmob后台->账户管理->身份验证`），并经`Bmob官方审核`通过，可以自定义模板。
2.不提交身份信息，直接提交短信模板，并经`Bmob官方审核`通过，可以使用该模板。

**短信模板需要符以下要求，否则模板审核不予通过。**

注意：请不要发送任何敏感内容，请不要定制广告、营销以诱导用户行为的信息。具体如下：
1.不得包含敏感关键，[关键字列表文档下载](https://github.com/bmob/bmob-public-docs)，不得包含【】和 [] 
2.带联系方式（电话、qq微信、邮箱地址）的短信。如：“目前我司推出xx产品最新优惠，联系陈生了解详情，电话123-1234-1234”;
3.诱导用户行为，特别是带超链接。如“我司推出新作xxx游戏，下载链接http://xxx.com”等；
当前有两种使用自定义模板

发现上述情况，针对通过身份审核的开发者，第一次给予警告，第二次将直接关闭接口使用，针对通过模板审核请求使用的开发者，模板审核不给予通过。

**调用示例**

输入手机号码、短信内容，就可以给用户的手机号码发送自定义的短信内容了，以下的content只要在不违反相关规定的前提下，你可以随意输入：
```
Bmob.Sms.requestSms({"mobilePhoneNumber": "131xxxxxxxx","content": "您的验证码是：222222, 有效期是10分钟。"} ).then(function(obj) {
  alert("smsId:"+obj.smsId); //
}, function(err){
  alert("发送失败:"+err);
});
```

你还可以选择定时发送，比如未来的某一时刻给某个手机发送一条短信，sendTime的格式必须是YYYY-mm-dd HH:ii:ss， 如: 2015-05-26 12:13:14，请求如下：
```
Bmob.Sms.requestSms({"mobilePhoneNumber": "131xxxxxxxx","content": "您的验证码是：222222, 有效期是10分钟。","sendTime": "2016-05-26 12:13:14"} ).then(function(obj) {
  alert("smsId:"+obj.smsId); //
}, function(err){
  alert("发送失败:"+err);
});
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该条短信是否发送成功：
```
{
	"smsId": 1232222
}
```

## 请求短信验证码
如果没有在管理后台创建好模板，可使用默认的模板，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 默认的模板是: **您的验证码是%smscode%，有效期为%ttl%分钟。您正在使用%appname%的验证码**

使用默认的模板请求短信验证码：
```
Bmob.Sms.requestSmsCode({"mobilePhoneNumber": "131xxxxxxxx"} ).then(function(obj) {
  alert("smsId:"+obj.smsId); //
}, function(err){
  alert("发送失败:"+err);
});
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：
```
{
	"smsId": 1232222
}
```

如果你已经在 [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 后台设置了自己的模板，并已经是审核通过了，则可以使用自己的模板给用户的手机号码发送短信验证码了：
```
Bmob.Sms.requestSmsCode({"mobilePhoneNumber": "131xxxxxxxx", "template":"注册模板"} ).then(function(obj) {
  alert("smsId:"+obj.smsId); //
}, function(err){
  alert("发送失败:"+err);
});
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：
```
{
	"smsId": 1232222
}
```

## 验证短信验证码

通过以下接口，你可以验证用户输入的验证码是否是有效的：
```
Bmob.Sms.verifySmsCode("131xxxxxxxx", 125466).then(function(obj) {
  alert("msg:"+obj.msg); //
}, function(err){
  alert("发送失败:"+err);
});
```

成功返回以下JSON，表明验证码验证通过：
```
{
	"msg":"ok"
}
```

## 查询短信状态

通过以下接口，你可以查询某条短信是否发送成功，如果是使用了Bmob的模板的话还能查询到是否被验证过，其实:smsId是请求短信验证码API返回的smsId值：
```
Bmob.Sms.querySms(6817361).then(function(obj) {
  alert("status:"+obj.sms_state); //
}, function(err){
  alert("发送失败:"+err);
});
```
成功返回以下JSON：
```
{
  "sms_state": "SENDING", 
  "verify_state": false
}
```
其中sms_state是发送状态，有值: SENDING-发送中，FAIL-发送失败 SUCCESS-发送成功
其中verify_state是验证码是否验证状态， 有值: true-已验证 false-未验证


## 购买事项

### 购买方法

短信条数只能输入整数，且不能少于1000条

![短信计费模式][1]

进入账号控制台，财务/财务统计点击购买短信即可。

![购买短信][2]

### 发票事宜

购买金额满100可提供发票，1000元以内的到付，1000以上（含1000）包邮。

登录后台提交工单，提供购买服务的订单号和开票信息。

**个人**

发票抬头、邮寄地址、联系人及电话

**企业**

公司名称、统一社会信用代码、开户行及账号、邮寄地址、联系人及电话


  [1]: http://bmob-file-service-t.b0.upaiyun.com/Doc_File/jfms.png
  [2]: http://bmob-file-service-t.b0.upaiyun.com/Doc_File/14703632600603.jpg
