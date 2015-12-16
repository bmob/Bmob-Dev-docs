# 支付接口（iOS）快速入门

## 简介

支付SDK接口是Bmob为广大开发人员提供的统一、正规的收费手段，让没有企业认证的个人开发者，也能通过支付宝向用户收费。该文档可以让您快速为自己的应用接入Bmob的支付功能

## 启用须知

 - 正式上线时请在Bmob管理平台开启安全认证（数据浏览->应用信息->安全认证）

 - 在Bmob财务管理平台进行身份认证，以保证资金安全

 - 如有任何疑问或者建议，欢迎加入Bmob支付的技术支持QQ群：273080081

## 创建项目

1. 点击[link](http://bmob.cn/site/sdk#android_pay_sdk_tab)，下载iOS支付SDK。
2. 将压缩包中的AlipaySDK、AlipaySDK.bundle以及BmobPay.framework添加到项目中，并导入依赖库SystemConfiguration.framework以及CoreLocation.framework。
3. 点击项目名称,点击“Info”选项卡,在“URL Types”选项中,点击“+”, 在“URL Schemes”中输入一个标识,如“bmobSDKDemo”,该标识尽量设置成唯一。
4. 在 `AppDelegate.m` 的 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`方法中使用 `[BmobPaySDK registerWithAppKey:@" replace your app key"];` 进行注册。

*注意：目前iOS端支付SDK只支持支付宝。*


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
    
## 错误码列表

 |错误码 |含义        |
 |------|:---------:|
 | 10077 | 重复支付，当上一个支付操作未完成时就调用下一次支付操作会引发该错误|
 | 30000 | 价格为空|
 | 3003 | appScheme为空|
 | 3004 | 订单号为空|
 | 20002| 网络连接失败|
 | 20010| 未知错误|






