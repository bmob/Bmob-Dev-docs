相关云函数的编写方式，请参考[云函数开发文档](https://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html)

在REST API中可以调用云函数。例如，想调用云函数的方法hello:


**请求**

- url ：https://api2.bmob.cn/1/functions/funcName

- method ：POST

- header:

```
X-Bmob-Application-Id: Your Application ID
X-Bmob-REST-API-Key: Your REST API Key
Content-Type: application/json
```

- body:

```
{
  key1 : value1,
  key2 : value2,
  ...
}
```

**成功时响应**

- status: 200 OK

- body: 对应云函数返回的格式。

**例子**

如调用名为hello的云函数可使用以下请求。

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"name":1337.23,"playerName":"Sean Plott","cheatMode":false}' \
  https://api2.bmob.cn/1/functions/hello
```

如果运行的云函数不需要传入参数，请参考下面的例子。
**注意，"{}"是不能缺的**

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{}' \
  https://api2.bmob.cn/1/functions/test
```


