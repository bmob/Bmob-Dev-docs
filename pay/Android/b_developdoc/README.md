## 简介

Android支付SDK接口是Bmob为广大开发人员提供的统一、正规的收费手段，让没有企业认证的个人开发者，也能通过支付宝和微信向用户收费。该文档可以让您快速为自己的应用接入Bmob的支付功能。下图为使用支付的流程。

![](image/flow.png)

## 使用需知

使用Bmob支付组件前，请认真阅读[Bmob服务协议](http://www.bmob.cn/service)。

对于以下两种情况，开发者需要特别注意：

1.违反服务协议，特别是含有以下内容的应用：

**1）封建迷信和/或淫秽、色情、下流的信息或教唆犯罪的信息；**
**2）博彩有奖、赌博游戏、“私服”、“外挂”等非法互联网出版活动；**
Bmob平台有权进行独立判断并采取技术手段予以删除、屏蔽或断开链接。同时，本平台有权视用户的行为性质，采取包括但不限于暂停或终止服务，限制、冻结或终止本平台网站账号的使用，追究法律责任等措施。
2.应用遇到过多的用户投诉，如应用的使用者支付了相关款项，但是该应用却没有提供相应的服务。本平台有权限制或冻结该应用支付收入的所有款项，并保留追究法律责任的权利。

##  打款需知

1.打款前请先在控制台填写以下信息

![](image/14579272227324.jpg)



2.每月的1、2、16、17号为申请打款时间，15号、月尾日为打款时间，确保用户有半个月的追诉期。Bmob将收取5%手续费。

## 支持平台

目前安卓的支付支持支付宝和微信支付。

## 启用须知

 - 在Bmob财务管理平台进行身份认证，以保证资金安全
 
 - 建立的支付界面必须包含类似于以下界面的声明，即”收款方为第三方支付服务商，(内容自定，如付款后您的vip将在当天开通等)。如有问题，请联系（你产品的客服联系方式）“，这里的收款方必须注明为（第三方支付服务商）"，并且必须有留下开发者的客服联系方式。针对每一款支付应用我们都会进行审核，如果不满足要求，Bmob有权停用支付功能。

![](image/C200B86F2D3E.png)

 - 如有任何疑问或者建议，欢迎加入Bmob支付的技术支持QQ群：521591577

## 支付接口快速入门

- 将名为libs的文件夹放在您项目根目录下（里面有 BmobPay_版本号.jar文件）
- 在您项目的AndroidManifest.xml中添加以下权限:

		    <uses-permission android:name="android.permission.INTERNET" />
		    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
		    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
		    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
		    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
		    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
     
- 在AndroidManifest.xml的Application标签下添加以下内容：

        <activity
            android:name="c.b.a.A"
            android:configChanges="orientation|keyboardHidden|navigation"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" />
        <activity
            android:name="c.b.a.B"
            android:screenOrientation="portrait"
            android:theme="@android:style/Theme.Translucent" />

- 在您的应用程序主Activity的onCreate中调用如下方法：<p>
  （Application ID在后台应用管理的 数据浏览->应用信息->应用密钥->Application ID）

    		BP.init(context,"你的Application ID");
**注意：新版的支付sdk不能被数据服务sdk的初始化方法取代了，无论您是否使用了Bmob数据服务SDK，都要进行支付SDK的初始化**

- 发起支付调用，请使用如下方法：
	
		/**
     	 * 第5个参数为true时调用支付宝支付，为false时调用微信支付
     	 */
		BP.pay(MainActivity.this, "商品名称", "商品描述", 0.02, true, new Plistener(){...});
		
- 在需要调用订单查询的地方，调用如下方法(微信订单和支付宝订单通用)：

		BP.query(MainActivity.this, "订单id", new QListener(){...});

## 类库说明

**c.b.BP**

- BP.pay(Activity activity, String title, String descript, double money, boolean aliOrWetchat, Plistener listener)

|类型|名称|说明|
|---|---|---|
|Activity|activity|调用支付时的Activity|
|String|title|商品的名称,请注意不要有违禁字眼,可以为空<p>只允许中文、数字、英文和下划线、英文破折号，否则过滤|
|String|descript|商品的详情描述,请注意不要有违禁字眼,可以为空<p>只允许中文、数字、英文和下划线、英文破折号，否则过滤|
|double|price|商品的价格,建议测试用0.02|
|boolean|aliOrWetchat|支付方式：true为支付宝支付，false为微信支付|
|Plistener|listener|支付结果监听类c.b.PListener<p>有成功、失败、未知结果、返回订单号等方法|

**注意**

**支付时返回的信息都未必可靠，一切以查询订单返回的信息为准**

	调用微信支付，要安装插件(如果没有安装,会监听器的fail方法会返回-3错误码)，插件在sdk文档的plugin文件夹下，demo有通过assets安装的示例

- BP.query(Activity activity, String orderId, QListener listener)

|类型|名称|说明|
|---|---|---|
|Activity|activity|调用查询订单时的Activity|
|String|orderId|支付订单号,不可为空|
|OrderQueryListener|listener|查询结果监听类c.b.QListener<p>有成功、失败等方法|

- ForceFree()

	当上一次支付操作尚未完成时,如果BmobPay对象发起再次请求,PayListener会回调fail方法返回并10777错误码,以免生成多个订单<p>
	如果使用过程中出现了阻塞(比如异常强制关闭支付插件页面,会导致一直不能再发起请求，这是小概率事件),则调用此方法进行BmobPay的重置<p>
	仅对下一次请求生效,而不是永久消除限制

**c.b.PListener**

- orderId(String orderId)
	
	无论支付成功与否,只要成功产生了请求,就返回订单号,请自行保存以便以后查询

- succeed()

	支付成功,保险起见请调用查询方法确认结果

- fail(int code, String reason)

	支付失败,有可能是用户中断支付,也有可能是网络问题<p>返回10777时说明上次操作尚未完成,拒绝多次请求以免生成多个订单(可用BP.ForceFree()方法强制取消一次限制)<p>支付宝支付时6001为用户主动中断支付操作</p>微信支付返回-2时为用户主动中断操作,返回-3为未安装Bmob支付插件.apk</p>(如果多次出现异常请向Bmob工作人员反馈)

- unknow()
	
	因为网络等问题,不能确认是否支付成功,请稍后手动查询(小概率事件)



**c.b.QListener**

- succeed(String status)

	**查询成功(并不是说支付成功),返回的status有NOTPAY和SUCCESS两种可能**

- fail(int code, String reason)

	查询失败,有可能是网络问题,也有可能是订单号错误

## 错误码列表
| 错误码 | 原因 |
|------|------|
|-1|微信返回的错误码，可能是未安装微信，也可能是微信没获得网络权限等|
|-2|微信支付用户中断操作|
|-3|未安装微信支付插件|
|102|设置了安全验证，但是签名或IP不对|
|6001|支付宝支付用户中断操作|
|4000|支付宝支付出错，可能是参数有问题|
|1111|解析服务器返回的数据出错，可能是提交参数有问题|
|2222|服务器端返回参数出错，可能是提交的参数有问题（如查询的订单号不存在）|
|3333|解析服务器数据出错，可能是提交参数有问题|
|5277|查询订单号时未输入订单号|
|7777|微信客户端未安装|
|8888|微信客户端版本不支持微信支付|
|9010|网络异常，可能是没有给应用网络权限|
|10003|商品名或详情不符合微信/支付宝的规定（如微信商品名不可以超过42个中文）|
|10777|上次发起的请求还未处理完成，禁止下次请求，可用BP.ForceFree()解除|

平时比较需要主动处理到的是-3、7777、8888、10777

## 支付服务
关于如何使用支付功能，请查看 [Android支付SDK](/androidpay/index.html?menukey=fast_start&key=start_android_pay "Android支付SDK") 或 [iOS支付SDK](/iospay/index.html?menukey=fast_start&key=start_ios_pay "iOS支付SDK")。RestAPI目前只提供了查询订单的功能。

## 查询订单

    curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/pay/Bmob系统生成的订单号

成功返回以下JSON, 失败时返回请看 [支付功能相关错误码](/errorcode/index.html?menukey=otherdoc&key=errorcode#index_支付功能相关错误码 "支付功能相关错误码")

    {
    "name": "商品",
    "body": "商品详情",
    "create_time": "2015-03-24 11:14:58",
    "out_trade_no": "9f392618f449a71c6fcfdee38d2b29e4",
    "transaction_id": "2015061100001000330057820379"
    "pay_type": "WECHATPAY",
    "total_fee": 0.01,
    "trade_state": "NOTPAY"
    }

返回的信息简单描述如下：

- name           : 订单或商品名称 
- body-商品详情
- create_time    : 调起支付的时间
- out_trade_no   : Bmob系统的订单号
- transaction_id : 微信或支付宝的系统订单号
- pay_type       : WECHATPAY（微信支付）或ALIPAY（支付宝支付）
- total_fee      : 订单总金额
- trade_state    : NOTPAY（未支付）或 SUCCESS（支付成功）

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

## 其他
- 请不要混淆sdk中的任何类和方法名(c.b.*和c.b.a.*)
- 在[Bmob财务管理平台](http://www.bmob.cn/finance/info "Bmob财务管理平台")订单管理处，金额从小数点后第三位开始不显示，比如支付了0.01元实收0.00，其实是0.0095
- 如果用户的手机有“应用锁”功能（即点击应用后跳出系统设定的解锁界面，如小米、360、腾讯管家都可能有该功能），则可能会导致支付中断（支付宝返回6001，微信返回-2），这是微信和支付宝sdk出于安全考虑设置的，请建议用户出现该问题时先关掉支付宝钱包或微信的应用锁
- 由于微信Sdk的限制，无法判断微信是否已登陆用户，**如果未登陆用户，监听器的fail方法可能不被调用**，请开发者们提醒自己的用户确保微信已登陆
- 如果支付宝已经选定了支付用的账户（或银行卡），但是支付失败，用户的支付宝账号会保留该订单，有可能从支付宝官网、支付宝钱包APP再次发起支付，在开发过程中请注意这种事情的处理情况
- 如果请求支付的页面为横屏，微信支付页面可能出现抽风现象（卡顿甚至重启），是因为微信出现莫名Bug不停开启新支付页面导致内存爆满，解决方法：在AndroidManifest.xml中将com.bmob.pay.tool.PayActivity设为强制竖屏（上方有示例），并尽可能将自己调用的Activity也设置为竖屏
- 请再次注意：**查询监听器的succeed(String status)被调用时并不意味着该订单已经成功支付**，而是应该判断status是否为SUCCESS
- **注意：支付时返回的信息都未必可靠，一切以查询订单返回的信息为准**





