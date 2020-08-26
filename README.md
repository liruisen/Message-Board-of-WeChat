# 微信小程序--留言板功能开发（免服务器）
## 前言

- 实现了留言板块的开发，可以与公众号绑定，提供公众号一个留言板块

- 使用腾讯云开发实现数据库等的读取，不再需要租借服务器，只要注册相应的小程序即可

- 大家可以在这里互相交流，如果需要可以留言创个群互相交流呢

## 1.代码添加与小程序绑定

### 代码相关注意事项

1. 小程序注册[[1](https://mp.weixin.qq.com/)]与开发者工具下载网站[[2](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)]
2. 提交的代码都是自己写的代码，但是在项目初始化时，开发者工具会自动提供部分参考代码。请各位在使用时酌情筛选，另外可能在上传过程中漏掉了部分代码导致程序无法正常运行，请告诉我呢谢谢
3. 创建项目时需要勾选'云开发 QuickStart 项目'，见[官方模版]([https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/quickstart.html#_1-%E6%96%B0%E5%BB%BA%E4%BA%91%E5%BC%80%E5%8F%91%E6%A8%A1%E6%9D%BF](https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/quickstart.html#_1-新建云开发模板))
4. 设计到云开发保存于[cloudfunctions](https://github.com/igo312/Message-Board-of-WeChat/tree/master/cloudfunctions)，具体的云开发函数部署见[[3]([https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/quickstart.html#_1-%E6%96%B0%E5%BB%BA%E4%BA%91%E5%BC%80%E5%8F%91%E6%A8%A1%E6%9D%BF](https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/quickstart.html#_1-新建云开发模板))]；若有意向进行本地调试，需进行`npm install`的初始化，位置应在相应的云函数文件夹下，具体见[[4](https://blog.csdn.net/baidu_38607919/article/details/104048661)]


### 小程序绑定

1.需创建公众号并在`小程序-小程序管理`进行相应的与小程序绑定

2.目前本人公众号发布的都是图文消息，在创建过程中可加入小程序

3.公众号提供的展示方式有`文字`、`图片`、`小程序卡片`、`小程序码`，本人建议使用文字注释为`写留言`即可。图片为`小程序卡片`展示方式

4.在绑定过程中会提示添加小程序页面路径，请使用`pages/post/comment/comment?idx=(输入你自己的编号)`进行绑定

![image](https://636f-comment-cloud-3r8vc-1302746278.tcb.qcloud.la/%E5%89%AA%E8%BE%91.png?sign=591a4dfefc0390de8a343abc4dbb1d05&t=1598450036)




## 2.代码模块解释
```
  |- miniprogram

       |---- data:在使用云函数前的数据提供端（现可删除）

       |---- images:提供了相应的图标与图片

       |---- item:提供了操作评论内容的接口代码如1.获取缓存内容，2.更新评论缓存，3.调用云函数更新云数据库等

       |---- pages:除`post`文件夹以外其余为系统自己初始化的内容

       |---- post:设定了欢迎界面`welcome`以及留言板界面`comment`，剩余两个文件夹未使用

              |---- welcome:欢迎界面，目前只起欢迎作用，放置公众号的二维码图片

              |___ comment: 留言板界面（主要功能区域）

       |---- utils:	提供了时间转换，时间比较(评论顺序的排列)，获取用户信息等功能

  |_ cloudfunctions

       |---- getComment: 在数据库存在相应的集合读取相应的集合数据

       |---- initComment: 在数据库不存在时进行初始化集合，初始化会自动添加一条评论并置顶，评论内容可在`miniprogram/item/item-		`
                         `post.js/commentFromCloud`中进行修改
       |---- updateComment: 实现本地评论增加后对数据库的更新。

       |---- updateUp:实现本地点赞后对数据库的更新

       |___ login:获取用户的openID功能
```

## 3.Q&A(自问自答)

1. Q：未来还会更新哪些功能呢？

   A：会考虑在某条评论长按触发如选择、复制、删除自己评论的功能

2. Q：目前留言板还存在什么明显的问题呢？

   A：目前留言板还有许多问题存在，如留言的排序顺序还是要斟酌的；在测试过程中可以发现有时候加载留言的速度较慢会让用户觉得没有人留言，这是一个	  很大的问题

3. Q：能简单介绍整个程序的实现流程么？

   A：主要说说留言板的整个实现功能，用户进入小程序之后，程序会去云数据库寻找是否存在相应的集合，若存在则读取，不存在则进行创建并初始化一条评	  论；评论以发布时间从最新到最迟进行排序（初始化评论的时间被设定为2021年，因此目前永远置顶）。用户在发布留言后会直接调用云函数将新发布的	  内容与云数据库同步，但点赞的同步将在用户退出小程序后进行

## 4.申明

这是我第一个web相关的项目，很多都是依靠网上资料实现的，所以感谢所有帮助过我的人，而我也希望我能为需要的人尽绵薄致力，任何问题只要是我能帮到我都会尽力帮助你呢，欢迎在这里提出issues或者联系邮箱syren0925@163.com,谢谢！

**访问者可将本网站提供的内容或服务用于个人学习、研究或欣赏，以及其他非商业性或非盈利性用途，但同时应遵守著作权法及其他相关法律的规定，不得侵犯本网站及相关权利人的合法权利。除此以外，将本网站任何内容或服务用于其他用途时，须征得本网站及相关权利人的书面许可，并支付报酬。**











