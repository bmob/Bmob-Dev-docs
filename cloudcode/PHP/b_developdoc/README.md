## 云端代码

相关云端代码的编写方式，请参考[云端代码开发文档](http://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html)

## 运行云端代码

在REST API中可以调用云端代码。例如，想调用云端代码的方法getMsgCode:

```
$cloudCode = new BmobCloudCode('getMsgCode'); //调用名字为getMsgCode的云端代码
$res = $cloudCode->get(array("name"=>"bmob")); //传入参数name，其值为bmob
```

