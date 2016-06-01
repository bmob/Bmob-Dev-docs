我们提供了BmobCloud类来调用云端逻辑的功能，有两种方法

```
//同步调用云端逻辑，fuction指的用函数名 parameters为函数需要的参数，同步的方法情在子线程中使用，不然会卡住主线程
+(id)callFunction:(NSString *)function withParameters:(NSDictionary *)parameters;
```

例子:

```
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    	//sayhello 为云端逻辑的函数名，
    	//num 为参数名，@1为 参数值
    	
        id result = [BmobCloud callFunction:@"sayhello" withParameters:@{@"num":@1}];
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"cloudFunction %@",result);
        });
    });
```


```
//异步调用云端逻辑，fuction指的用函数名 parameters为函数需要的参数
+ (void)callFunctionInBackground:(NSString *)function withParameters:(NSDictionary *)parameters block:(BmobIdResultBlock)block;
```

例如，在应用中添加了sayhello的云端逻辑，功能是打印出hello，可以在SDK里这样调用

```
[BmobCloud callFunctionInBackground:@"sayhello" withParameters:nil block:^(id object, NSError *error) {
      if (error) {
          NSLog(@"error %@",[error description]);
      }
     NSLog(@"object      %@",object);
}] ;
```
注意，为了确保体验，建议使用异步调用的方法。

关于云端逻辑的编写，详细参考  [云端逻辑开发文档](http://docs.bmob.cn/cloudcode/WEB/a_faststart/doc/index.html)





