
## 简介

支付SDK接口是Bmob为广大开发人员提供的统一、正规的收费手段，让没有企业认证的个人开发者，也能通过支付宝向用户收费。该文档可以让您快速为自己的应用接入Bmob的支付功能。下图为使用支付的流程。

![](image/flow.png)


## 使用需知

使用Bmob支付组件前，请认真阅读[Bmob服务协议](http://www.bmob.cn/service)。

对于以下两种情况，开发者需要特别注意：

1.违反服务协议，特别是含有以下内容的应用：

**1）封建迷信和/或淫秽、色情、下流的信息或教唆犯罪的信息；**
**2）博彩有奖、赌博游戏、“私服”、“外挂”等非法互联网出版活动；**
Bmob平台有权进行独立判断并采取技术手段予以删除、屏蔽或断开链接。同时，本平台有权视用户的行为性质，采取包括但不限于暂停或终止服务，限制、冻结或终止本平台网站账号的使用，追究法律责任等措施。
2.应用遇到过多的用户投诉，如应用的使用者支付了相关款项，但是该应用却没有提供相应的服务。本平台有权限制或冻结该应用支付收入的所有款项，并保留追究法律责任的权利。

另外请注意，需要上架App Store的应用在集成支付前请先确认支付用途，如果是用于虚拟交易(游戏道具、应用会员、去广告等)不允许使用第三方支付，只能使用苹果提供的内购功能进行支付，只有实物交易(衣物、电子产品等)才允许使用第三方支付，用户在集成前必须了解清楚苹果的上架政策。

## 打款需知

1.打款前请先在控制台填写以下信息

![](image/14579272227324.jpg)

2.每月的1、2、16、17号为申请打款时间，15号、月尾日为打款时间，确保用户有半个月的追诉期。Bmob将收取5%手续费。

## 支持平台

目前iOS的支付只支持支付宝。

## 启用须知

 - 在Bmob财务管理平台进行身份认证，以保证资金安全
 
 - 建立的支付界面必须包含类似于以下界面的声明，即”收款方为第三方支付服务商，(内容自定，如付款后您的vip将在当天开通等)。如有问题，请联系（你产品的客服联系方式）“，这里的收款方必须注明为（第三方支付服务商）"，并且必须有留下开发者的客服联系方式。针对每一款支付应用我们都会进行审核，如果不满足要求，Bmob有权停用支付功能。

![](image/C200B86F2D3E.png)

 - 如有任何疑问或者建议，欢迎加入Bmob支付的技术支持QQ群：521591577

## 创建项目

1. 点击[link](http://bmob.cn/site/sdk#android_pay_sdk_tab)，下载iOS支付SDK。
2. 将压缩包中的AlipaySDK、AlipaySDK.bundle以及BmobPay.framework添加到项目中，并导入依赖库SystemConfiguration.framework、CoreLocation.framework、CoreTelephony.framework、CoreMotion.framework以及libz.tdb、libc++.tdb。
3. 点击项目名称,点击“Info”选项卡,在“URL Types”选项中,点击“+”, 在“URL Schemes”中输入一个标识,如“bmobSDKDemo”,该标识尽量设置成唯一。
4. 在 `AppDelegate.m` 的 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`方法中使用 `[BmobPaySDK registerWithAppKey:@" replace your app key"];` 进行注册。

*注意：目前iOS端支付SDK只支持支付宝。*

另外，官方提供了一个demo，下载地址如下[https://github.com/bmob/bomb-iOSPay-Demo](https://github.com/bmob/bomb-iOSPay-Demo)

## 支付功能

具体步骤如下：

1. 首先在使用支付功能的文件中引入头文件
		#import <BmobPay/BmobPay.h>

2. 声明BmobPayDelegate,代码如下：
	
		@interface ViewController ()<BmobPayDelegate>

		@end

3. 使用支付功能，具体如下：

    1)声明BmobPay对象;

    2)设置代理;

    3)设置商品属性，含价格、商品名以及具体描述;

    4)设置appScheme;

    5)调用支付。

*注意：商品名及商品描述只支持输入中文、英语、数字、下划线(_)及英文破折号(-)，其余字符自动过滤。*

关键代码如下：

    BmobPay* bPay = [[BmobPay alloc] init];
    //设置代理
    bPay.delegate = self;
    //设置商品价格、商品名、商品描述    
    [bPay setPrice:priceNum];
    [bPay setProductName:productName];
    [bPay setBody:body];
    //appScheme为创建项目第4步中在URL Schemes中添加的标识
    [bPay setAppScheme:appScheme];
    //调用支付宝支付
    [bPay payInBackgroundWithBlock:^(BOOL isSuccessful, NSError *error) {
        if (isSuccessful) {
            NSLog(@"支付跳转成功");
        } else{
            NSLog(@"%@",[error description]);
        }
    }];
    
另外，支付宝支付是优先跳转至支付包钱包，如果用户有安装支付宝钱包,在跳转支付宝客户端支付的过程中,商户app在后台很可能被系统kill了，因此需要在AppDelegate.m中加入以下代码，以获得回调。

	- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
	{
		if ([url.host isEqualToString:@"safepay"]) {
       
        [[AlipaySDK defaultService] processOrderWithPaymentResult:url standbyCallback:^(NSDictionary *resultDic) {}];
   		 }
		return YES;
	}
    
设置代理后可以使用如下方法以提示用户支付状态：
	
	-(void)paySuccess{
    	UIAlertView *alter = [[UIAlertView alloc] initWithTitle:@"支付结果" message:@"支付成功" delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil];
    	[alter show];
	}

	-(void)payFailWithErrorCode:(int) errorCode{
    	switch(errorCode){

        case 6001:{
            UIAlertView *alter = [[UIAlertView alloc] initWithTitle:@"支付结果" message:@"用户中途取消" delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil];
            [alter show];
        }
            break;
            
        case 6002:{
            UIAlertView *alter = [[UIAlertView alloc] initWithTitle:@"支付结果" message:@"网络连接出错" delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil];
            [alter show];
        }
            break;
            
        case 4000:{
  
            UIAlertView *alter = [[UIAlertView alloc] initWithTitle:@"支付结果" message:@"订单支付失败" delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil];
            [alter show];
        }
            break;
  	  }
	}

详细用法可参照SDK压缩包中提供的Demo。


## 订单查询

首先在使用支付功能的文件中引入头文件
		
		#import <BmobPay/BmobPay.h>

分为3步：

1. 声明BmobPay对象；
2. 传入要查询的订单号；
3. 调用查询。

关键代码如下：

    BmobPay* bPay = [[BmobPay alloc] init];
    [bPay setTradeNo:@"传入要查询的订单号"];
    [bPay queryInBackgroundWithBlock:^(BOOL isSuccessful, NSError *error) {
        if (isSuccessful) {
            NSLog(@"订单状态为：%@",[bPay tradeStatus]);
        }else{
            NSLog(@"%@",[error description]);
        }
    }];
    
## 查询版本号

可使用以下方法打印版本号，如向Bmob工程师询问问题，请将打印版本号告诉工程师。

```
    //打印版本号
    NSLog(@"%@",BmobPayVersion());
```

## 支付回调

如图，可以在支付-支付配置处填入通知url。

![](image/14598448796882.jpg)

这样在支付成功后会向该url（SDK使用异步通知URL，PHP等调用网页支付的使用同步返回URL）发送post请求，结构如下：

```
{
	"trade_status":"1",
	"out_trade_no":"809488d695ed42ec56b57546d2df94cc",
	"trade_no":"2016033021001004810225607152"
}
```
trade_status：表示支付状态，目前只有支付成功才产生回调，值恒为1.
out_trade_no：Bmob返回的订单号
trade_no：支付宝或微信返回的订单号

    
## 错误码列表

 |错误码 |含义        |
 |------|:---------:|
 | 10077 | 重复支付，当上一个支付操作未完成时就调用下一次支付操作会引发该错误|
 | 30000 | 价格为空|
 | 3003 | appScheme为空|
 | 3004 | 订单号为空|
 | 20002| 网络连接失败|
 | 20010| 未知错误|








