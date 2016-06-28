Q:我看了官网即时聊天的demo，同样都是继承BombObject的javabean类（数据表）为什么有的是创建在云端，有的保存在本地数据库？这是怎么设置的
A:自己控制的呀，想保存到后台，那你就调用save方法来创建数据，保存到本地就自己创建数据库

---
Q:BmobIM2.0 发送自定义消息失败

```
SendCommentMessage commentMessage=new SendCommentMessage();
            User currentUser = BmobUser.getCurrentUser(DynamicDetailActivity.this,User.class);
            Map<String,Object> map =new HashMap<>();
            map.put("name", currentUser.getUsername());//发送者姓名，这里只是举个例子，其实可以不需要传发送者的信息过去
            map.put("avatar",currentUser.getAvatar());//发送者的头像
            map.put("dynamicId",dynamicid);//发送者的uid
            //启动一个会话，如果isTransient设置为true,则不会创建在本地会话表中创建记录，
            //设置isTransient设置为false,则会在本地数据库的会话列表中先创建（如果没有）与该用户的会话信息，且将用户信息存储到本地的用户表中
            //构造聊天方的用户信息:传入用户id、用户名和用户头像三个参数
            BmobIMUserInfo info;
            if (atUser!=null){
                info = new BmobIMUserInfo(atUser.getObjectId(),atUser.getUsername(),atUser.getAvatar());
            }else{
                info = new BmobIMUserInfo(dynamic.getAuthor().getObjectId(),dynamic.getAuthor().getUsername(),dynamic.getAuthor().getAvatar());
            }
            BmobIMConversation c = BmobIM.getInstance().startPrivateConversation(info, true,null);
            c.sendMessage(commentMessage, new MessageSendListener() {
                @Override
                public void done(BmobIMMessage bmobIMMessage, BmobException e) {
                    if (bmobIMMessage!=null){

                    }
                }
            });

```
A:还少了一个步骤，使用obtain方法创建一个用于控制发送消息的会话实例

---

Q:BmobIMUserInfo info为null
BmobIMUserInfo info = BmobIM.getInstance().getUserInfo((String) BmobUser.getObjectByKey(mContext, "objectId"));
info为null是为什么

A:用户信息需要开发者自己调用sdk提供的更新本地用户信息的方法（BmobIM.getInstance().updateUserInfo(BmobIMUserInfo info)）来存储的，然后才能通过getUserInfo方法获取的，因为IMSDK内部维护了一个用户表。

---

Q:怎么查询别人要请求添加我为好友的信息？就是想加我好友的人的列表。
android，即时通讯，我用另外一个小号发送了请求加我大号为好友的请求，但是我怎么查询出谁发送了这个请求，查询出来后我才能点击同意啊。文档上只说加好友和同意加好友，没说怎么查询列举出添加好友的请求的信息。

A:目前的demo里面的好友添加请求是查询本地数据库的（可查看
NewFriendActivity
中的query方法，NewFriendManager.getInstance(this).getAllNewFriend()方法查询好友添加请求），查询出来的就是谁添加我为好友的请求

---

Q:Android Im拖动未阅读的会话数量控件调用conversation.updateLocalCache()不能将会话标记为已经阅读
A:conversation.updateLocalCache();这个方法需要先开启私聊并创建会话后拿到的新会话实例才可以调用的，具体可参考ChatActivity中的用法。

---

Q:Android使用Bmob IM聊天时有离线消息就会闪退
A:android.content.res.Resources$NotFoundException: String resource ID #0x7f060015
你这个错误是因为没有导入bmob_im_notification_strings.xml这个文件造成的。

请查看官方IM文档中关于下载NewIMSDK及官方Demo的描述：
values(bmob_im_notification_strings.xml)-用于通知栏显示

--- 

Q:想用聊天组件，连接服务器出错误
A:看Android开发文档中SDK导入部分的解释：

每个版本的im依赖特定版本的bmob-sdk：

bmob-im:1.1.8--->bmob-sdk:3.3.5
bmob-im:1.1.9--->bmob-sdk:3.4.3
bmob-im:2.0.1--->bmob-sdk:3.4.6-0304
bmob-im:2.0.2--->bmob-sdk:3.4.6-0304
bmob-im:2.0.3--->bmob-sdk:3.4.6

---

Q:发送消息的时候添加的额外参数在接收的时候怎么拿到
A:getExtra返回的是一个字符串，再自行解析。

---

Q:用android studio打开后，报不是gradle工程，是啥原因？
A:使用as 是需要依赖gradle来下载各种jar包的，因为需要翻墙下载

