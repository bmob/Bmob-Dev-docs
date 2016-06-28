Q:iOS 新安装的短信SDK 和之前安装的BmobSDK有冲突
A:短信SDK是在不需要使用BmobSDK时才使用的，BmobSDK里本身包含的短信SDK的所有内容，所以你将短信SDK移除就可以了

---

Q:手机验证码注册不成功
如果注册前不验证验证码是否正确，直接发送注册请求，就可以注册成功，如果先验证，就会报错。提示：code error 207 ，输入的验证码是正确的。请问如何解决。

```
- (IBAction)registerNewUser:(id)sender {
// 验证注册码是否正确
[BmobSMS verifySMSCodeInBackgroundWithPhoneNumber:self.phoneNumber.text andSMSCode:self.smsNumber.text resultBlock:^(BOOL isSuccessful, NSError *error) {
if (isSuccessful) {
// 发送注册请求
BmobUser *buser = [[BmobUser alloc]init];
[buser setUsername:self.phoneNumber.text];
[buser setPassword:self.password.text];
[buser setMobilePhoneNumber:self.phoneNumber.text];

[buser signUpOrLoginInbackgroundWithSMSCode:self.smsNumber.text block:^(BOOL isSuccessful, NSError *error) {

if (isSuccessful) {
NSLog(@"注册成功");
}else{
NSLog(@"注册失败%@",error);
}
}];

}else{

NSLog(@"输入的验证码不正确");
}

}];

}
```

A:验证码注册只需要在注册的时候输入即可，不需要先进行一次验证的，verifySMSCodeInBackgroundWithPhoneNumber方法是用于注册以后的验证功能

---

Q:注册时需要短信验证， 改怎么实现
A:注册时让用户填写手机号码，再进行验证即可，有手机注册验证接口


