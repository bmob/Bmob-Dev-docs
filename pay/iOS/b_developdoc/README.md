## 简介

iOS支付SDK接口是Bmob为广大开发人员提供的统一、正规的收费手段，让没有企业认证的个人开发者，也能通过支付宝和微信向用户收费。该文档可以让您快速为自己的应用接入Bmob的支付功能。下图为使用支付的流程。

![](http://i.imgur.com/pP4Cki6.png)

## 使用需知

使用Bmob支付组件前，请认真阅读[Bmob服务协议](http://www.bmob.cn/service)。

对于以下两种情况，开发者需要特别注意：

1.违反服务协议，特别是含有以下内容的应用：

**1）封建迷信和/或淫秽、色情、下流的信息或教唆犯罪的信息；**
**2）博彩有奖、赌博游戏、“私服”、“外挂”等非法互联网出版活动；**

Bmob平台有权进行独立判断并采取技术手段予以删除、屏蔽或断开链接。同时，本平台有权视用户的行为性质，采取包括但不限于暂停或终止服务，限制、冻结或终止本平台网站账号的使用，追究法律责任等措施。

2.应用遇到过多的用户投诉，如应用的使用者支付了相关款项，但是该应用却没有提供相应的服务。本平台有权限制或冻结该应用支付收入的所有款项，并保留追究法律责任的权利。

3.**Bmob将在每笔交易中收取8%服务费。**

##  打款需知

1.打款前请先在控制台填写以下信息

![](image/IMG_0262.jpg)


2.每月的1、2、16、17号为申请打款时间，15号、月尾日为打款时间，确保用户有半个月的追诉期。

## 支持平台

目前 iOS 端仅提供支付宝支付支持。

## 启用须知

 - 在Bmob财务管理平台进行身份认证，以保证资金安全
 - 提交申请时，开发者的联系方式至少要有两种(邮箱，电话，QQ)，方便后续支付弹出的订单页面展示，如下：

![](image/IMG_0262.png)

## SDK 接入

- 1. 将 `BmobPaySDK.framework` 拖入项目中，并选取 `copy items if needed`

![](image/1C61E5DA-F4BE-4800-AC54-A3D011D1FC8C.png)

- 2. `TARGETS` -> `General` -> `Embedded Binaries` 点击 `+` 号 选择 `BmobPaySDK.framework`，如下所示：

![](image/3A9B2ADA-9B4E-4C8C-8663-0E1A91D17ADC.png)

- 3. 在 info.plist 添加以下两个字端：  
A. `View controller-based status bar appearance`，类型为 `BOOL`，值为 `NO`  
B. `LSApplicationQueriesSchemes`，类型为 `Array`，添加一个字符串元素 `alipays` 

![](image/D4381805-2C25-49B8-B7AA-A6DBC085C9B5.png)

```
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
<key>LSApplicationQueriesSchemes</key>
<array>
 	<string>alipays</string>
</array>
```

## 支付调用

```
#import <BmobPaySDK/Bmob.h>
```

在 AppDelegaet 注册应用：

```
[Bmob registerWithAppKey:@"xxxxxxxx"]; 

```

在对应页面：

```
#import <BmobPaySDK/BmobPay.h>
```

然后进行关键函数调用：

> 注意，支付类型应该填写 `BmobAlipay `，类型预留。

```
[BmobPay payWithPayType:BmobAlipay //支付类型选择，暂时只支持支付宝
                  price:@888 //订单价格，0 - 5000
              orderName:@"订单名称" //不为空
               describe:@"订单描述" //不为空
                 result:nil]; //应用内支付回调
```

## 订单查询

> 注意事项：查询操作自动从内存中获取订单号，应该等待支付操作回调执行时或执行后才进行查询接口调用。

接口如下：

```
+ (void)queryWithResult:(BmobPayResultBlock)result;
```

成功查询会返回以下类似数据：

```
{
"name": "商品",    //商品名称 
"body": "商品详情",
"create_time": "2015-03-24 11:14:58",   //调起支付的时间
"out_trade_no": "9f392618f449a71c6fcfdee38d2b29e4",  //Bmob系统的订单号
"transaction_id": "2015061100001000330057820379"  //微信或支付宝的系统订单号
"pay_type": "WECHATPAY",  //WECHATPAY（微信支付）或ALIPAY（支付宝支付）
"total_fee": 0.01,  //订单总金额
"trade_state": "NOTPAY"  //NOTPAY（未支付）或 SUCCESS（支付成功） 
}
``` 

## 应用外支付回调

如图，可以在支付-配置信息处填入通知url。

![](http://i.imgur.com/40aAkKh.png)


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