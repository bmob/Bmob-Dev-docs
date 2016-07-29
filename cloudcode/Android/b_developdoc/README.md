云端代码的API集成在BmobSDK中，因此不熟悉的朋友在使用前先可以了解一下BmobSDK的集成[Android 快速入门](http://docs.bmob.cn/data/Android/a_faststart/doc/index.html)

很多时候，单纯的前端代码是不能完成全部事情的，一些重要和复杂的业务逻辑还是希望能够在服务端中执行。比如：对比较大量的比赛数据进行排序，对某个网站进行资料采集和处理，获取用户的IP信息，等等。Bmob不仅提供了云端存储，还开放了云端的业务逻辑代码功能，也就是云端逻辑。

相关云端逻辑的使用，大家可以参考开发文档： [http://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html](http://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html)

云端逻辑的执行有多种方法：

- 定时任务（直接在Web管理后台中设定）； 
- RestApi调用（参考开发文档：[http://docs.bmob.cn/data/Restful/b_developdoc/doc/index.html#0%06Mn](http://docs.bmob.cn/data/Restful/b_developdoc/doc/index.html#0%06Mn)）； 
- SDK调用。

其中，在SDK中调用云端逻辑的方法如下：
```java
AsyncCustomEndpoints ace = new AsyncCustomEndpoints();
//第一个参数是上下文对象，第二个参数是云端逻辑的方法名称，第三个参数是上传到云端逻辑的参数列表（JSONObject cloudCodeParams），第四个参数是回调类
ace.callEndpoint(context, cloudCodeName, params, new CloudCodeListener() {
	@Override
	public void onSuccess(Object object) {
		// TODO Auto-generated method stub
		toast("云端usertest方法返回:" + object.toString());
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("访问云端usertest方法失败:" + msg);
	}
});
```

