## iOS相关问题

### 支持什么编译器

Bmob完全支持iOS 64bit/32bit的真机和模拟器调试。

### 请问有对应的swift，开发方法吗？
Swift项目中使用BmobSDK可以看这个文档：[Swift项目中使用BmobSDK](http://docs.bmob.cn/swift/index.html?menukey=otherdoc&key=swift)

### 哪里可以看到错误码

请查看iOS开发文档中的[错误代码列表](http://docs.bmob.cn/ios/developdoc/index.html?menukey=develop_doc&key=develop_ios#index_错误码列表)。

### 注册和登录的流程是怎样开发的

见Android相关部分。

### 打开了邮箱验证功能，注册成功后未验证也能登录成功？

Bmob SDK中，邮箱的验证和用户的注册登录是异步的关系，也就是说，即使用户没有点击邮箱验证功能，也是一样可以登录成功的。如果需要限制用户的登录或者只能查看到登录后的部分功能，可以使用`[[[BmobUser getCurrentUser] objectForKey:@"emailVerified"] boolValue]`方法。

### Bmob单张图片如何上传到bmob数据库？

用`BmobProFile`类的上传方法上传之后，在`onSuccess`中你会得到`BmobFile`或者这个文件的url，再把这个`BmobFile`或者url插入到`BmobObject`中，保存就可以了。

### 怎么获取relation中的数据? 

假设你有一个帖子(Post)类和一个系统默认的用户(User)类, 而每一个帖子(Post)都可以被不同的用户(User)所喜欢。 如果帖子(Post)类下面有一个Key名为likes，且是 Relation 类型, 存储了喜欢这个帖子(Post)的用户(User)。那么你可以找到喜欢过同一个指定的帖子(Post)的所有用户：

	BmobQuery *bquery = [BmobQuery queryForUser];
	[bquery orderByDescending:@"updatedAt"];
	BmobObject *obj = [BmobObject objectWithoutDatatWithClassName:@"Post" objectId:@"a1419df47a"];
	[bquery whereObjectKey:@"likes" relatedTo:obj];
	[bquery findObjectsInBackgroundWithBlock:^(NSArray *array, NSError *error) {
	}];

### 如果每个用户都有写入权限，安全应该怎么做？ 

可以设置ACL，详情请查看文档[ACL和角色](http://docs.bmob.cn/ios/developdoc/index.html?menukey=develop_doc&key=develop_ios#index_ACL%E5%92%8C%E8%A7%92%E8%89%B2)。

### 注册的时候如何给User表自定义的字段插值？
 
有个`BmobUser`类用来操作用户相关的数据  
	BmobUser *bUser = [[BmobUser alloc] init];  
	[bUser setUserName:@"小明"];  
	[bUser setPassword:@"123456"];  
	//age 为自定义  
	[bUser setObject:@18 forKey:@"age"];  
	[bUser signUpInBackground];

### 有iOS 点赞功能的demo吗？

有的，[Bmob点赞案例](https://github.com/limaofuyuanzhang/BmobLikeDemo) 实现的用户注册、用户登录、发贴、显示所有帖子资料以及对帖子进行点赞的功能 。

