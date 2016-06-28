Q:按照设制好了IOS推送，推送后显示状态为“发送至APNS”，但前面写着“推送0条”，此时，手机也未接收到信息，是为什么？
A:1、看看Installation表是否有设备信息；
2、Bmob后台中是否把推送证书添加上去（不能加密）；
3、你先尝试推送给所有的真实手机。

---

Q:如何用 BmobPush发送原始apns报文？
A:要发送原始信息的可以使用-(void)setData:(NSDictionary *)data;方法

---

Q:在iOS中 在代码中如何创建一个空表 只包含各列的属性 而不创建具体的一条数据。
A:参考代码

```
BmobInstallation *ins = [BmobInstallation currentInstallation];
[ins saveInBackgroundWithResultBlock:^(BOOL isSuccessful, NSError *error) {
    if (error) {
        NSLog(@"%@",error);
    } else {
        NSLog(@"success");
        NSLog(@"%@",ins.objectId);
    }
}];
```

---

Q:消息推送的条件查询（根据特定的查询条件进行推送）能在自己创建的表格中进行查询条件推送
A:只能使用自带的installation表来查询。

---

Q:在installation 表中创立新的列无法添加进去数据
```
BmobInstallation *currentIntallation = [BmobInstallation currentInstallation];
[currentIntallation setObject:@"123" forKey:@"classes"];

[currentIntallation saveInBackground];
```
A:确实是无法直接这么加的，建议使用channel来实现业务需求

---

Q:消息推送的条件查询（根据特定的查询条件进行推送）能在自己创建的表格中进行查询条件推送吗！！！
A:不可以，只能使用自带的installation表来查询

