
云函数的调用方法非常简单，如下为调用执行云函数`test`的实现代码：

```java
	Bmob.Endpoint<Hashtable>("test", (resp, exception) => 
	{
		if (exception != null)
		{
			print("调用失败, 失败原因为： " + exception.Message);
			return;
		}

		print("返回对象为： " + resp);
	});
```
相关云函数的编写方式，请参考[云函数开发文档](https://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html)。

