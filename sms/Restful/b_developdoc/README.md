在一些应用场景下，你可能希望用户验证手机号码后才能进行一些操作，例如充值等。这些操作跟用户系统没有关系，可以通过我们提供的的短信验证API来实现。

每个 Bmob 帐户有 10 条免费额度的短信数量用于测试，超过需要购买短信条数才能继续使用。

默认使用 【比目科技】 作为签名，可以在控制台进行修改。

## 请求发送短信内容

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

**请求**

- url ：https://api.bmob.cn/1/requestSms

- method ：POST

- header:

```
X-Bmob-Application-Id: Your Application ID
X-Bmob-REST-API-Key: Your REST API Key
Content-Type: application/json
```

- body:

```
{
  "mobilePhoneNumber": phoneNum,
  "content": content,
  "sendTime":YYYY-mm-dd HH:ii:ss (选填)
}
```

**成功时响应**

- status: 200 OK

- body:

```
{
	"smsId": smsId（可用于后面使用查询短信状态接口来查询该条短信是否发送成功）
}
```

**例子**


输入手机号码、短信内容，就可以给用户的手机号码发送自定义的短信内容了，以下的content只要在不违反相关规定的前提下，你可以随意输入：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "content":"您的验证码是：222222, 有效期是10分钟。"}' \
  https://api.bmob.cn/1/requestSms
```

你可以选择定时发送，比如未来的某一时刻给某个手机发送一条短信，sendTime的格式必须是YYYY-mm-dd HH:ii:ss， 如: 2015-05-26 12:13:14，请求如下：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "content":"您的验证码是：222222, 有效期是10分钟。感谢您使用Bmob。", "sendTime":"2016-05-26 12:13:14"}' \
  https://api.bmob.cn/1/requestSms
```

成功返回短信验证码ID：

```
{
	"smsId": 1232222
}
```

## 请求短信验证码


**请求描述**

使用特定的模板请求验证码，如果没有在管理后台创建好模板，可使用默认的模板，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 默认的模板是: **您的验证码是%smscode%，有效期为%ttl%分钟。您正在使用%appname%的验证码**

**请求**

- url ：https://api.bmob.cn/1/requestSmsCode

- method ：POST

- header:

```
X-Bmob-Application-Id: Your Application ID
X-Bmob-REST-API-Key: Your REST API Key
Content-Type: application/json
```

- body:

```
{
  "mobilePhoneNumber": phoneNum,
  "template": templateName(选填，需先在管理后台创建)
}
```

**成功时响应**

- status: 200 OK

- body:

```
{
	"smsId": smsId（可用于后面使用查询短信状态接口来查询该条短信是否发送成功）
}
```

**例子**

使用默认的模板请求短信验证码：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx"}' \
  https://api.bmob.cn/1/requestSmsCode
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：

```
{
	"smsId": 1232222
}
```

如果你已经在 [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 后台设置了自己的模板，并已经是审核通过了，则可以使用自己的模板给用户的手机号码发送短信验证码了：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "template":"注册模板"}' \
  https://api.bmob.cn/1/requestSmsCode
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：

```
{
	"smsId": 1232222
}
```

## 验证短信验证码

**请求描述**

通过以下接口，你可以验证用户输入的验证码是否是有效。

**请求**

- url ：https://api.bmob.cn/1/verifySmsCode/smsCode(用户收到的6位短信验证码)

- method ：POST

- header:

```
X-Bmob-Application-Id: Your Application ID
X-Bmob-REST-API-Key: Your REST API Key
Content-Type: application/json
```

- body:

```
{
  "mobilePhoneNumber": phoneNum
}
```

**成功时响应**

- status: 200 OK

- body:

```
{
	"msg":"ok"
}
```

**例子**

例如，要验证 `186xxxxxxxx` 号码输入的 `876845` 验证码是否正确可使用以下请求：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx"}' \
  https://api.bmob.cn/1/verifySmsCode/876845
```



## 查询短信状态

**请求描述**

通过以下接口，你可以查询某条短信是否发送成功，如果是使用了验证码接口，还可以查询该验证码是否被验证过。

**请求**

- url ：https://api.bmob.cn/1/querySms/:smsId （注意smsId前有冒号(:)）

- method ：GET

- header:

```
X-Bmob-Application-Id: Your Application ID
X-Bmob-REST-API-Key: Your REST API Key
Content-Type: application/json
```


**成功时响应**

- status: 200 OK

- body:

```
{
  "sms_state": state, 
  "verify_state": isVerify
}
```
sms_state是发送状态，有值: SENDING-发送中，FAIL-发送失败 SUCCESS-发送成功
verify_state是验证码是否验证状态， 有值: true-已验证 false-未验证

**例子**

例如，如果在上述发送短信接口或者请求验证码接口请求成功后返回的 `smsId` 为 `123678`，则可以使用以下请求查询这些短信的状态。

```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  https://api.bmob.cn/1/querySms/:123678
```

成功返回以下JSON：
```
{
  "sms_state": "SENDING", 
  "verify_state": false
}
```

**注意事项**

关于短信条数的计算规则如下:

1. 实际计算的短信字数 = 模板的内容或自定义短信的内容字数 + 6。加上6是因为默认的签名【比目科技】占了6个字。
2. 实际计算的短信字数在70个字以下算1条。
3. 实际计算的短信字数超过70字的以67字为一条来计算的。也就是135个字数是计算为3条的。
4. 计算得到的短信条数在本条短信发送成功后将会从你的账户剩余的短信条数中扣除。

**短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条**

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
