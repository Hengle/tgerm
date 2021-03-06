---
layout: post
comments: 
excerpt:  
tag: []
title: 制作人是什么鬼
---

<span style="color: #ff6600;"><strong>原创博文，转载请声明</strong></span>

### 标题是唬人的

标题是唬人的。为了写这个，所以今天没有技术文(unity3d)了。

这一到年底就喜欢瞎总结。总结避免不了回忆一下过去，认真回忆了一下却发现最想写的却是“南骏梦”(南骏梦，北游族，加班够狠而来的封号)时的一段经历。这段经历中最想写的是孟老板（没错，就是我们项目的制作人）。



### 初见

会议室走进来一小个子的光头，如果非要准确描述下：好像光头强啊。寒暄两句，坐下开始抽烟：“我出生在军人家庭，在军属大院长大，做事要求很严格！”，我心里嘀咕着：艾玛，好屌啊！咦，我腿怎么在抖*&……&*……*#￥%？不过很快我就控制住了我的腿，怎么说也是有很多面试候选人经历的，怎么可能被他一句话给吓倒。陪他抽了大概一个小时的二手烟后，我回家吃饭了。



### 再见

大概三个月后，我们又见了。这次有头发了，原来他不是年纪大了没头发。

“为什么又找我来？”

“你为什么又来了？”

“家就在河对岸，闲着也是闲着。"

"我们要做3D页游，要不要来？“

”项目准备做多久？“

”四个月！“

”别闹，认真点…“

”嗯，认真的。“

“。。。。。。”

又陪他抽了两个小时的二手烟后，在一个阳光明媚的周六，我正式入职了。



### 开大会

在一个安静的角落，公司给我配了一台非常“牛逼”的开发机器。一开机就蓝屏！孟老板觉得有点尴尬，就把几十号人拉到会议室讲了半天目前正运营项目的情况，然后介绍了接下来要做的3D项目。当然，也介绍了我。介绍我的时候，我都不敢抬头。（艾玛，从没和这么多美女一起开过会。难道，孟老板找人有特殊癖好？）

以上都不是重点。散会后，我们来到一间小会议室里。孟老板在他的小破笔记本上戳了几下，打开了一张计划表。好几屏的功能列表，看的我脚心都出汗了：

“我要时间搭底层。”

“嗯，要多久？”

“我现在列个清单给你，然后再跟你说时间会比较好接受点。”

大脑高速运转后：

1. 渲染（Away3D）
2. 地形追踪
3. NavMesh寻路（自动生成寻路网格）
4. 相关3DMax导出插件
5. 场景编辑器
6. 特效编辑器
7. UI编辑器
8. 通讯模块
9. 等等

”在运营项目有几个程序可以过来？“

”一个。”

“那你的四个月是怎么安排的？”

“四个月是我说着玩的。”

“。。。。。。”

一个月后，我们游戏有了第一个可运行的场景。



### 并非血泪史

朋友知道我去骏梦后，从此第一句问候便是：昨天加班到几点？每次我说没怎么加班八点就到家了呢。他们都选择不相信，后来我干脆说：我们加班非常的猛，早十点晚十点，周六还要上班。苦逼的要死。我都心律不齐了（这是真的）。实际情况是，孟老板极少要求程序加班，到后来迫于其他项目玩命加班（勾搭妹子玩游戏吹牛看视频 —— 每日加班必做四件事）的压力，才要求九点下班。我大胆猜想孟老板不要求程序加班的理由有以下几条：

1. 要求程序加班，会导致开发效率不高。
2. 目前看程序上班时间基本都是满负荷工作，没有勾搭妹子，玩游戏，吹牛，看视频等，开发进度正常。
3. 傻缺主程很固执，不愿意加班。认为加班是在拖慢项目进度。
4. 让程序加班就是要他们：勾搭妹子，玩游戏，吹牛，看视频。

**孟老板是明白人**。



### 制作人之策划篇

我跟孟老板沟通的时候，会这样胡说八道：你们策划应该先把功能想清楚再把需求提给程序。。。我内心一直觉得制作人的工作应该是偏产品的。实际情况是策划要是敢糊弄着写文档，提需求。首先过不去的就是孟老板这关，故程序拿到的文档基本上是不需要和策划怎么撕，就可以进入开发阶段的。这很大程度上节约了时间，不用搞什么文档评审。当然，也没策划敢跟孟老板说一个文档需要写三天这样的事。

所以搞什么评审，撕什么逼，好好写文档，策划程序本就应该相亲相爱。

**孟老板靠谱**。



### 制作人之美术篇

游戏美术分很多种：原画，动作，场景，UI，特效等。在3D游戏中有一个非常重要的美术分工：技术美术。孟老板就扮演了这样的角色，场景划分，模型面数控制，DrawCall优化统统搞定。

对于程序来讲，最重要的就是美术资源。美术资源是否按照规则制作，是否能按时提供都是非常重要的。这方面孟老板是有求必应，答应的时间点很少会延期。

**不懂美术的程序不是好制作人**。



### 制作人之程序篇

这里我只讲两件事：

1. 孟老板能听懂我在说什么，所以我不敢乱定工期。且他充分信任我。
2. 孟老板帮忙解决了场景运动模糊后，缓冲区颜色值错误问题。

**孟老板，我们能成为好基友吗**?



### 总结篇：制作人是什么鬼

制作人是什么鬼？制作人就是像孟老板这样能力好，颜值高(此条纯属虚构)，综合实力超强，对待兄弟真诚（这条可从项目解散时的种种论证得来）。

总结为：

1. 对于团队来讲，的确需要一个领头羊，跟大家一起拼搏。负更多的责任，当然也理应赢得更多的利益。而不是出问题只懂问责，而不去思考解决方法。指手画脚谁不会呢？
2. 拥有话语权也许你可以随心所欲，但你无法赢得真正的尊重。
3. 以身作则也很重要，项目进度紧张的时候孟老板熬到凌晨是常有的事，我虽然不是完全认同他当时的做法，但起码我尊重他的工作成果。要求我加班加点，我欣然接受。
4. 最后，尊重他人。帮助他人即帮助自己。

### 广而告之

你没看错，我写这么多。最后的广告才是重点，孟老板已是怒豆科技的大Boss。已有一款项目成功上线，现正招兵买马。有意者可联系我，或访问公司网站：http://www.furybeans.com

孟老板，这大过年的你是不是应该发个红包给我啊。