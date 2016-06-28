Q: 手机中安装两个包含bmob push sdk的app，那么这时另一个包含bmobpush sdk的app会报错。
A:解决方法：
删除androidMainfest.xml中
```<permission android:protectionLevel="normal" android:name="cn.bmob.permission.push"></permission>```
这一句。

其实这一句完全可以不加，也可以正常使用，亲测2个app推送正常，且不报错，关于原因请百度android permission相关知识（如果找不到再找客服吧~）

---

Q:消息推送后点击消息进入不同的fragment页面
A:这个是需要自己去定义的，在点击进入时应该有一个地方可以控制页面的行为的，具体的谷歌百度会有很多资料

---

Q:用PushManager.pushMessage(text)推送的消息能设置过期时间吗？默认的过期时间是多久？
A:暂时没有该功能。

---

Q:消息推送里要设置的包名是指什么包
A:消息推送里要设置的包名是你应用的包名（Androidmanifest文件中的package）

