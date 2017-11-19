**注意：目前只支持微信和qq扫码支付，最少支付金额2.2元**


## 微信支付

```

	$bmobPay = new BmobPay();
	
	//webpay()各参数的含义，请查看 https://github.com/bmob/bmob-php-sdk/blob/master/lib/BmobPay.class.php 这个文件
	$res = $bmobPay->webPay(2.2, "充值", "给应用充值2.2元", 2);

```

[微信支付demo](https://github.com/bmob/bmob-php-sdk/blob/master/pay/pay_weixin.php)

## qq支付

```

	$bmobPay = new BmobPay();
	
	//webpay()各参数的含义，请查看 https://github.com/bmob/bmob-php-sdk/blob/master/lib/BmobPay.class.php 这个文件
	$res = $bmobPay->webPay(2.2, "充值", "给应用充值2.2元", 3);

```

[qq支付demo](https://github.com/bmob/bmob-php-sdk/blob/master/pay/pay_qq.php)

## 查询订单



```

$bmobPay = new BmobPay();
$res = $bmobPay->getOrder($tradeNo);

```

$res中各个变量的含义如下:

```
{
	name:订单或商品名称 
	body:商品详情  
	create_time:调起支付的时间  
	out_trade_no:Bmob系统的订单号  
	transaction_id:微信或支付宝的系统订单号
	pay_type:WECHATPAY（微信支付）或ALIPAY（支付宝支付） 或QQPAY（QQ支付） 
	total_fee:订单总金额  
	trade_state:NOTPAY（未支付）或 SUCCESS（支付成功）
}
```

[查询订单demo](https://github.com/bmob/bmob-php-sdk/blob/master/pay/check_order.php)
