# Android支付SDK



## 简介

Android支付SDK接口是Bmob为广大开发人员提供的统一、正规的收费手段，让没有企业认证的个人开发者，也能通过支付宝和微信向用户收费。该文档可以让您快速为自己的应用接入Bmob的支付功能

## 启用须知

正式上线时请在[Bmob管理平台](http://www.bmob.cn/app/list "Bmob管理平台")开启安全认证（数据浏览->应用信息->安全认证）<p>
在[Bmob财务管理平台](http://www.bmob.cn/finance/info "Bmob财务管理平台")进行身份认证，以保证资金安全<p>
如有任何疑问或者建议，欢迎加入Bmob支付的技术支持QQ群：521591577 

## 支付接口快速入门

- 将名为libs的文件夹放在您项目根目录下（里面有 BmobPay_版本号.jar 和支付宝的jar一共两个jar文件）
- 在您项目的AndroidManifest.xml中添加以下权限:

	    <!-- alipay sdk permission begin -->
		    <uses-permission android:name="android.permission.INTERNET" />
		    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
		    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
		    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
		    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
		    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <!-- alipay sdk permission end -->
     
- 在AndroidManifest.xml的Application标签下添加以下内容：

		<!-- bmob pay sdk activity begin ,please put below code into application tag -->
        <activity
            android:name="com.alipay.sdk.app.H5PayActivity"
            android:configChanges="orientation|keyboardHidden|navigation"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" >
        </activity>
        <activity
            android:name="com.bmob.pay.tool.PayActivity"
            android:screenOrientation="portrait"
            android:theme="@android:style/Theme.Translucent" />
        <!-- bmob pay sdk activity end -->

- 在您的应用程序主Activity的onCreate中调用如下方法：<p>
  （Application ID在后台应用管理的 数据浏览->应用信息->应用密钥->Application ID）

    		BmobPay.init(context,"你的Application ID");
**注意：如果您的应用使用了Bmob数据服务SDK，则只要初始化其中一个即可**

- 在需要调用支付宝支付的地方，调用如下方法：
	
		new BmobPay(MainActivity.this).pay(0.02,"某商品",new PayListener(){...});

- 在需要调用微信支付的地方，调用如下方法：
	
		new BmobPay(MainActivity.this).payByWX(0.02,"某商品",new PayListener(){...});
		
- 在需要调用订单查询的地方，调用如下方法(微信订单和支付宝订单通用)：

		new BmobPay(MainActivity.this).query("订单id",new OrderQueryListener(){...});

## 类库说明

**com.bmob.pay.tool.BmobPay**


- pay(double price, String name, PayListener listener)
- pay(double price, String name, String body, PayListener listener)


<table>
    <tr>
        <th>类型</th>
        <th>名称</th>
		<th>说明</th>
    </tr>
    <tr>
        <td>double</td>
        <td>price</td>
        <td>商品的价格,建议测试用0.02</td>
    </tr>
    <tr>
        <td>String</td>
        <td>name</td>
        <td>商品的名称,请注意不要有违禁字眼,可以为空<p>只允许中文、数字、英文和下划线、英文破折号，否则过滤</td>
    </tr>
    <tr>
        <td>String</td>
        <td>body</td>
        <td>商品的详情描述,请注意不要有违禁字眼,可以为空<p>只允许中文、数字、英文和下划线、英文破折号，否则过滤</td>
    </tr>
    <tr>
        <td>PayListener</td>
        <td>listener</td>
        <td>支付结果监听类com.bmob.pay.tool.PayListener<p>有成功、失败、未知结果、返回订单号等方法</td>
    </tr>

</table>

- payByWX(double price, String name, PayListener listener)
- payByWX(double price, String name, String body, PayListener listener)

	调用微信支付，要安装插件(如果没有安装,会监听器的fail方法会返回-3错误码)，插件在sdk文档的plugin文件夹下，demo有通过assets安装的示例

- query(String orderId, OrderQueryListener listener)

<table>
    <tr>
        <th>类型</th>
        <th>名称</th>
		<th>说明</th>
    </tr>
    <tr>
        <td>String</td>
        <td>orderId</td>
        <td>支付订单号,不可为空</td>
    </tr>
    <tr>
        <td>OrderQueryListener</td>
        <td>listener</td>
        <td>查询结果监听类com.bmob.pay.tool.OrderQueryListener<p>有成功、失败等方法</td>
    </tr>
</table>

- ForceFree()

	当上一次支付操作尚未完成时,如果BmobPay对象发起再次请求,PayListener会回调fail方法返回并10077错误码,以免生成多个订单<p>
	如果使用过程中出现了阻塞(比如异常强制关闭支付插件页面,会导致一直不能再发起请求，这是小概率事件),则调用此方法进行BmobPay的重置<p>
	仅对下一次请求生效,而不是永久消除限制

**com.bmob.pay.tool.PayListener**

- orderId(String orderId)
	
	无论支付成功与否,只要成功产生了请求,就返回订单号,请自行保存以便以后查询

- succeed()

	支付成功,保险起见请调用查询方法确认结果

- fail(int code, String reason)

	支付失败,有可能是用户中断支付,也有可能是网络问题<p>返回10077时说明上次操作尚未完成,拒绝多次请求以免生成多个订单(可用Bmob.ForceFree()方法强制取消一次限制)<p>支付宝支付时6001为用户主动中断支付操作</p>微信支付返回-2时为用户主动中断操作,返回-3为未安装Bmob支付插件.apk</p>(如果多次出现异常请向Bmob工作人员反馈)

- unknow()
	
	因为网络等问题,不能确认是否支付成功,请稍后手动查询(小概率事件)



**com.bmob.pay.tool.OrderQueryListener**

- succeed(String status)

	**查询成功(并不是说支付成功),返回的status有NOTPAY和SUCCESS两种可能**

- fail(int code, String reason)

	查询失败,有可能是网络问题,也有可能是订单号错误

## 错误码列表
<table>
    <tr>
        <th>错误码</th>
        <th>原因</th>
    </tr>
    <tr>
        <td>-1</td>
        <td>微信返回的错误码，可能是未安装微信，也可能是微信没获得网络权限等</td>
    </tr>
    <tr>
        <td>-2</td>
        <td>微信支付用户中断操作</td>
    </tr>
    <tr>
        <td>-3</td>
        <td>未安装微信支付插件</td>
    </tr>
    <tr>
        <td>102</td>
        <td>设置了安全验证，但是签名或IP不对</td>
    </tr>
    <tr>
        <td>6001</td>
        <td>支付宝支付用户中断操作</td>
    </tr>
    <tr>
        <td>4000</td>
        <td>支付宝支付出错，可能是参数有问题</td>
    </tr>
    <tr>
        <td>1111</td>
        <td>解析服务器返回的数据出错，可能是提交参数有问题</td>
    </tr>
    <tr>
        <td>2222</td>
        <td>服务器端返回参数出错，可能是提交的参数有问题（如查询的订单号不存在）</td>
    </tr>
    <tr>
        <td>3333</td>
        <td>解析服务器数据出错，可能是提交参数有问题</td>
    </tr>
    <tr>
        <td>5277</td>
        <td>查询订单号时未输入订单号</td>
    </tr>
    <tr>
        <td>7777</td>
        <td>微信客户端未安装</td>
    </tr>
    <tr>
        <td>8888</td>
        <td>微信客户端版本不支持微信支付</td>
    </tr>
    <tr>
        <td>9010</td>
        <td>网络异常，可能是没有给应用网络权限</td>
    </tr>
    <tr>
        <td>10003</td>
        <td>商品名或详情不符合微信/支付宝的规定（如微信商品名不可以超过42个中文）</td>
    </tr>
    <tr>
        <td>10777</td>
        <td>上次发起的请求还未处理完成，禁止下次请求，可用BmobPay.ForceFree()解除</td>
    </tr>
</table>

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

## 其他
- 请不要混淆所有sdk的jar文件
- 在[Bmob财务管理平台](http://www.bmob.cn/finance/info "Bmob财务管理平台")订单管理处，金额从小数点后第三位开始不显示，比如支付了0.01元实收0.00，其实是0.0095
- 如果用户的手机有“应用锁”功能（即点击应用后跳出系统设定的解锁界面，如小米、360、腾讯管家都可能有该功能），则可能会导致支付中断（支付宝返回6001，微信返回-2），这是微信和支付宝sdk出于安全考虑设置的，请建议用户出现该问题时先关掉支付宝钱包或微信的应用锁
- 由于微信Sdk的限制，无法判断微信是否已登陆用户，**如果未登陆用户，监听器的fail方法可能不被调用**，请开发者们提醒自己的用户确保微信已登陆
- 如果支付宝已经选定了支付用的账户（或银行卡），但是支付失败，用户的支付宝账号会保留该订单，有可能从支付宝官网、支付宝钱包APP再次发起支付，在开发过程中请注意这种事情的处理情况
- 如果请求支付的页面为横屏，微信支付页面可能出现抽风现象（卡顿甚至重启），是因为微信出现莫名Bug不停开启新支付页面导致内存爆满，解决方法：在AndroidManifest.xml中将com.bmob.pay.tool.PayActivity设为强制竖屏（上方有示例），并尽可能将自己调用的Activity也设置为竖屏
- 请再次注意：**查询监听器的succeed(String status)被调用时并不意味着该订单已经成功支付**，而是应该判断status是否为SUCCESS
- **注意：支付时返回的信息都未必可靠，一切以查询订单返回的信息为准**