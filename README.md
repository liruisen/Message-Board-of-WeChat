# 微信小程序--留言板功能开发（免服务器）
![image](https://github.com/igo312/Message-Board-of-WeChat/blob/master/演示图.jpg)
## 前言

- 实现了留言板块的开发，可以与公众号绑定，提供公众号一个留言板块
- 使用腾讯云开发实现数据库等的读取，不再需要租借服务器，只要注册相应的小程序即可
- 大家可以在这里互相交流，如果需要可以留言创个群互相交流呢

## 2020/9/2 更新

- 实现当留言被评论时对某个留言的所有用户(不包括自己)进行通知

### 详细说明

利用`wx.requestSubscribeMessage`实现订阅消息的授权(调试必须在真机、tap事件中调试)

利用`subscribeMessage.send`在云开发端实现消息给用户的推送

具体逻辑在`item-post.js/push2client`函数中实现

## 2020/8/28 更新

### 更新简介

- 添加了对留言的评论功能与点赞功能的完善
- 添加了对留言、留言评论删除功能的实现
- 完善了数据加载逻辑，保证了先获取数据再进行渲染
- 对组件高宽进行了重新设定
- 添加了更多功能选项，将评论、点赞、删除进行归纳

### 详细说明

#### 1.对留言评论的逻辑实现

   利用变量控制调用的函数，在记录中添加一个列表（`leaveMessage`）保存评论记录，无论是在主留言评论还是对某一留言评论都会添加到留言记录中，时间排序为在列表末尾添加即从早到晚展现。在数据更新上，无论是添加评论还是在点赞上，都是直接利用`update`更新整个数组

#### 2.组件高宽的问题

 组件高宽的问题主要为留言板在真机调试中比例不合适的问题=>

 问题在于: `rpx`作为基本单位，其定义是根据屏幕宽度实现自适应。这导致了在高度上其实不能做到真正的自适应，不同机型的长宽比例不同，`dpr`只实现了宽度  的自适应。在问题的解决上，调用`wx.systemInfoSync.windowHeight`来获取可使用屏幕高度，乘以一定系数来设定留言板的高度。

#### 3. js控制wxss

  `js`不能直接控制样式，但能通过设置文本`wxml`，利用数据绑定`{{}}`实现控制样式如下面的代码


```
# javascript 的代码
var commentStyle:"height: 0px; bottom:-1200px;"

# wxml处的代码
<view id="" class="" style="{{commentStyle}}" animation="">
```

### 接下来的目标

- 实现被评论后用户会得到被回复的消息
- 加速小程序的加载

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

![image](https://github.com/igo312/Message-Board-of-WeChat/blob/master/剪辑.png)


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

       |---- login:获取用户的openID功能
       
       |---- deleteComment:用于删除评论和回复
       
       |____ leaveComment:用于对留言进行评论，评论完直接更新
       
       
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











