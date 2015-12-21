## 简介

Bmob即时通讯服务（iOS端）包含了IM聊天所需要的基本核心功能：

- 五种类型的消息任你发：`纯文本、聊天表情、图片、位置`;
- 完善的用户管理功能：`注册、登录`;
- 完善的好友管理功能：`添加好友、删除好友、获取好友列表`;
- 会话的本地化存储。

## UI效果

## 快速入门

### 1、下载并安装BmobIMSDK
1. 下载[IMSDK最新版](http://www.bmob.cn/sdk/BmobIM_1.0.2_Beta.zip):包含了IMSDK，BmobSDK和Demo。
2. 新建项目，并把BmobIMSDK和BmobSDK一起复制到新建的工程

**注：**

**1. iOS的BmobIM是基于推送服务的，所以需要开发者自己在bmob后端添加证书。具体操作见:http://docs.bmob.cn/iospush/index.html?menukey=otherdoc&key=iospush**

**2. 因为是使用Apple的推送的，所以添加好友，发送请求那些会把消息发送到APNS，由APNS推到客户端，有时收不到信息，有可能是因为APNS方面的问题**

**3. 由于模拟器接收不了推送，所以需要真机测试**

**4. 请确保手机已允许接受推送**

### 2、配置相关服务
`BmobIM`用到了推送服务，所以需要在管理后台上传对应Bundle ID的p12文件，请勿加密，
详细详细请参照：http://docs.bmob.cn/iospush/index.html?menukey=otherdoc&key=iospush

### 3、引入相关的库文件
有两种方法可以引入相关系统库：

1. 在你的XCode工程中Project -> Edit Active Target -> Build Settings->Link Binary With Libraries引入CoreLocation.framework、Security.framework、CoreGraphics.framework、MobileCoreServices.framework、CFNetwork.framework、CoreTelephony.framework、SystemConfiguration.framework、libz.1.2.5.dylib、libicucore.dylib、libsqlite3.dylib、AVFoundation.framework、MediaPlayer.framework

2. 使用CocoaPods安装BmobSDK，可查看我们提供的文档: https://github.com/bmob/bmob-ios-sdk/blob/master/InstallCocoaPods.md 

### 4、初始化
在你的XCode工程中的AppDelegate.m文件中创建应用Key，填入申请的授权Key（SDK使用的是应用密钥里的Application ID），示例如下：

在main.m文件中，引入头文件 `#import <BmobSDK/Bmob.h>`



```
int main(int argc, char * argv[])
{
    
    @autoreleasepool {
    	 NSString *appKey = @"申请的Application ID";
   		 [BmobChat registerAppWithAppKey: appKey]
    
       return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate     				class]));
	}
}
```

也可以在

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
   [BmobChat registerAppWithAppKey: @"申请的Application ID"]
	return YES;
}
```

### 5、注册推送

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
   if (IS_iOS8) {
        //iOS8推送
        UIMutableUserNotificationCategory*categorys = [[UIMutableUserNotificationCategory alloc]init];
        categorys.identifier=@"BmobIMDemo";
        UIUserNotificationSettings*userNotifiSetting = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeAlert|UIUserNotificationTypeBadge|UIUserNotificationTypeSound)
                                                                                         categories:[NSSet setWithObjects:categorys,nil]];
        [[UIApplication sharedApplication]registerUserNotificationSettings:userNotifiSetting];
        [[UIApplication sharedApplication]registerForRemoteNotifications];
    }else{
        [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    }
	return YES;
}
```

## 核心功能

### 用户管理

#### 注册

如果用户还未注册，IM也提供了简单地注册样例，

```
 [UserService registerWithUsernameInBackground:@"zhangsan"
                                         password:@"123456"
                                  locateSucessful:NO
                                            block:^(BOOL isSuccessful, NSError *error) {
}];
```

也可以根据BmobSDK来进行注册

```
BmobUser *bUser = [[BmobUser alloc] init];
bUser.userName=@"小明";
bUser.password:@"123456";
[bUser setObject:@18 forKey:@"age"];
[bUser signUpInBackgroundWithBlock:^ (BOOL isSuccessful, NSError *error){
    if (isSuccessful){
        NSLog(@"Sign up successfully");
    } else {
        NSLog(@"%@",error);
    }
}];
```
### 登录

用户已经在Bmob上注册了，可以使用提供的方法进行登录

```
    UITextField *tmpTextField = (UITextField *)[self.view viewWithTag:102];
    UITextField *tmpNcTextField = (UITextField *)[self.view viewWithTag:101];

    //登陆
    [UserService logInWithUsernameInBackground:tmpNcTextField.text
                                      password:tmpTextField.text
                                         block:^(BmobUser *user, NSError *error) {
                                             if (error) {
                                                 dispatch_async(dispatch_get_main_queue(), ^{
                                                     hud.labelText = [[error userInfo] objectForKey:@"error"];
                                                     hud.mode = MBProgressHUDModeText;
                                                     [hud show:YES];
                                                     [hud hide:YES afterDelay:0.7f];
                                                 });
                                                 
                                             }else{
                                                 dispatch_async(dispatch_get_main_queue(), ^{
                                                     [hud hide:YES];
                                                     [self dismissSelf];
                                                 });
                                                 //查找用户的好友
                                                 [UserService saveFriendsList];
                                             }
                                         }];
```

### 退出登录

当需要退出登录的时候，可以调用退出接口，并清空缓存数据

```
	[BmobUser logout];
    [[BmobDB currentDatabase] clearAllDBCache];

```

### 好友管理

#### 获取好友列表

```
//获取好友列表，并保存下来
[UserService saveFriendsList];
```

#### 添加好友

```
//发送TAG_ADD_CONTACT请求
[[BmobChatManager currentInstance] sendMessageWithTag:TAG_ADD_CONTACT targetId:obj.objectId block:^(BOOL isSuccessful, NSError *error) {
        if (isSuccessful) {
            hud.mode = MBProgressHUDModeText;
            hud.labelText = @"添加请求已发送";
            [hud hide:YES afterDelay:0.7f];
        }else{
            hud.mode = MBProgressHUDModeText;
            hud.labelText =[[error userInfo] objectForKey:@"error"];
            [hud hide:YES afterDelay:0.7f];
        }
    }];

```

#### 删除好友

```
	//删除本地的数据
	[[BmobDB currentDatabase] deleteContactWithUid:user.objectId];
	[[BmobDB currentDatabase] deleteMessagesWithUid:user.objectId];
	[[BmobDB currentDatabase] deleteRecentWithUid:user.objectId];
    //删除服务器上的数据
    [[BmobUserManager currentUserManager] deleteContactWithUid:user.objectId block:^(BOOL isSuccessful, NSError *error) {
        
    }];
```


