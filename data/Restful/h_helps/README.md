## RestApi相关问题

### 如何在线测试RestApi

使用Chrome浏览器的Postman插件就可以进行调试了。[点击链接Postman下载地址](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?utm_source=chrome-ntp-icon)。

界面效果[点击这里查看](http://docs.bmob.cn/restful/faststart/index.html?menukey=fast_start&key=start_restful#index_RestAPI调试工具)。

### Postman发起数据请求没有反应

首先先检查本地网络，通常是因为本地网络或者Postman没有成功发出数据请求，其次可以打开[https://api.bmob.cn/](https://api.bmob.cn/)查看是否能打开进行测试。

### 其他语言用RestApi开发遇到请求security的错误

请查找相关语言访问HTTPS的配置问题。
如PHP用CURL开发时，需要添加如下脚本：

```java
curl_setopt($c, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($c, CURLOPT_SSL_VERIFYHOST, FALSE);
```


### 能提供Java调用RestApi的示例代码吗

点击下面的链接可以看到Java调用RestApi的示例代码：

[http://wenda.bmob.cn//?/question/51](http://wenda.bmob.cn//?/question/51)

[http://wenda.bmob.cn/?/question/859](http://wenda.bmob.cn/?/question/859)

### 关于where条件的问题

有开发者提出用PostMan请求的时候没有问题，但是用Java请求构造了where查询条件的时候有错（请求的格式大致如， `https://api.bmob.cn/1/classes/Footballer?limit=20&where={"location": {"$nearSphere": {"__type": "GeoPoint","latitude": 32.31735060,"longitude": 118.32457035 }}}` ）。

这个问题是因为特殊字符导致的问题，大家可以参考以下的解决方案：

[http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function](http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function)

顺便说一句，PHP中的话，可以直接用`http_build_query`方法构造请求参数。

### Restapi有IM吗？

restapi可以发送推送信息，也可以通过get的方式获取聊天内容，但没有开放长连接服务，也就是说用restapi可以实现im功能，但方法只能通过定时器+get数据的方式。

### 哪里可以看到错误码

[点击这里](http://docs.bmob.cn/data/Restful/g_errorcode/doc/index.html)可以查看RestApi的错误码列表。


