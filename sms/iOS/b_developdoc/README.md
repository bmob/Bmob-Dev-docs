在一些应用场景下，你可能希望用户验证手机号码后才能进行一些操作，例如充值等。这些操作跟用户系统没有关系，可以通过我们提供的的短信验证API来实现。

每个 Bmob 帐户有 10 条免费额度的短信数量用于测试，超过需要购买短信条数才能继续使用。

默认使用 【比目科技】 作为签名，可以在控制台进行修改。

短信服务除了集成成进原来的BmobSDK包外，还另外拆分了一个独立的SDK包，使用前请先导入 `SystemConfiguration.framework`，  `JavaScriptCore.framework` 和 `CoreLocation.framework`，注册方法还是 `[Bmob registerWithAppKey:@""];`。


## 请求发送自定义短信内容

**使用场景**

 此方法允许开发者使用 Bmob 的短信功能灵活发送自定义的短信内容到用户的手机上，可运用于多种场景下，比如 `发送祝福短信、推送消息、通知`等


**使用前准备**

有两种方式可以使用自定义模板
1.开发者可在Bmob后台提交身份证信息（`Bmob后台->账户管理->身份验证`），并经`Bmob官方审核`通过，可以自定义模板。
2.不提交身份信息，直接提交短信模板，并经`Bmob官方审核`通过，可以使用该模板。


注意：请不要发送任何敏感内容，请不要定制广告、营销以诱导用户行为的信息。具体如下：
1.不得包含敏感关键，[关键字列表文档下载](https://github.com/bmob/bmob-public-docs)，不得包含【】和 [] 
2.带联系方式（电话、qq微信、邮箱地址）的短信。如：“目前我司推出xx产品最新优惠，联系陈生了解详情，电话123-1234-1234”;
3.诱导用户行为，特别是带超链接。如“我司推出新作xxx游戏，下载链接http://xxx.com”等；
当前有两种使用自定义模板

发现上述情况，针对通过身份审核的开发者，第一次给予警告，第二次将直接关闭接口使用，针对通过模板审核请求使用的开发者，模板审核不给予通过。
这个接口可让开发者使用 Bmob 的短信功能灵活发送短信内容到用户的手机上。

发现上述情况，针对通过身份审核的开发者，第一次给予警告，第二次将直接关闭接口使用，针对通过模板审核请求使用的开发者，模板审核不给予通过。


**调用示例**

样例代码如下：

```
    [BmobSMS requestSMSInbackgroundWithPhoneNumber:@"phoneNumber" Content:@"您的XX服务还有XX时间到期，请及时续费。" andSendTime:@"2015-07-01 10:59:00" resultBlock:^(int msgId, NSError *error) {
        if (error) {
            NSLog(@"%@",error);
        } else {
            NSLog(@"smsId:%d",msgId);
        }
    }];
```

```
说明：
1.如果需要立即发送，sendTime设置为nil;
2.sendTime的格式为 yyyy-MM-dd HH:mm:ss;
3.如果sendTime的格式不正确或者是这个时间已经是过去的时间，那么短信会立即发送;
4.smsID可用于查询短信的发送状态。
```


## 获取短信验证码
如下图所示，在使用获取短信验证码功能前可以先设置好几个模板以用于不同的功能。

![](image/message_1.png)

获取短信验证码可使用以下方法

```
    //请求验证码
    [BmobSMS requestSMSCodeInBackgroundWithPhoneNumber:mobilePhoneNumber andTemplate:@"test" resultBlock:^(int msgId, NSError *error) {
        if (error) {
            NSLog(@"%@",error);
        } else {
            //获得smsID
            NSLog(@"sms ID：%d",msgId);
        }
    }];
```

```
注意，短信模板设置后需要通过审核才能使用，以下情况将使用Bmob设定的默认模板（Bmob 默认的模板是: 您的验证码是%smscode%，有效期为%ttl%分钟。您正在使用%appname%的验证码
）。
1.传入模板名为nil或者@“”;
2.传入不存在的模板；
3.传入的模板未通过审核
```

## 验证短信验证码

验证短信验证码状态

```

    //验证
    [BmobSMS verifySMSCodeInBackgroundWithPhoneNumber:mobilePhoneNumber andSMSCode:smsCode resultBlock:^(BOOL isSuccessful, NSError *error) {
        if (isSuccessful) {
            NSLog(@"%@",@"验证成功，可执行用户请求的操作");
        } else {
            NSLog(@"%@",error);
        }
    }];
    
}

```

## 查询短信验证码状态

查询短信验证码状态示例代码：

```
    [BmobSMS querySMSCodeStateInBackgroundWithSMSId:smsId resultBlock:^(NSDictionary *dic, NSError *error) {
        if (dic) {
            NSLog(@"%@",dic);
        } else {
            NSLog(@"%@",error);
        }
    }];
```

```
注：SmsState包含两种状态：

1、smsState（短信状态） :SUCCESS（发送成功）、FAIL（发送失败）、SENDING(发送中)。

2、verifyState（验证状态）:true(已验证)、false(未验证)。
```

注意事项：

关于短信条数的计算规则如下，

1. 实际计算的短信字数 = 模板的内容或自定义短信的内容字数 + 6。加上6是因为默认的签名【比目科技】占了6个字。
2. 实际计算的短信字数在70个字以下算1条。
3. 实际计算的短信字数超过70字的以67字为一条来计算的。也就是135个字数是计算为3条的。
4. 计算得到的短信条数在本条短信发送成功后将会从你的账户剩余的短信条数中扣除。

**短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条。**


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
