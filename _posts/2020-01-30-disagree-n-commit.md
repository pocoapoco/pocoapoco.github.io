---
title: disagree and commit
date: 2020-01-30 19:17
---

我司有一条企业文化叫做"disagree and commit". 

除此以外日常生活中还存在以下三种行为模式：

> agree and do nothing

[会议室里]

tech lead 1: We should be aware of the latency involved in the processing layer.

manager 1: That's a good point. This will help define the SLA regarding data availability for downstream applications and end users.

tech lead 2: We're building solutions with near real-time synchronization of data. We do not enforce consistency or transactional capability.

manager 2: These must be handled at the application level. As tech lead 1 pointed out, latency will be introduced in the processing layer.

tech lead 1: That's correct.

director: 顾的。散会！

（然后开完会啥也没发生）


> agree and rollback

[公司聊天群里]

(经过1个小时的激烈讨论)

developer 1: 那么根据刚才的讨论，request payload必须遵守如下格式:<br>
             {
             	"name": ...
             	"address": ...
             	"occupation": ...
             }


developer 2: 没问题！棒！

developer 2: 我创建了一个新的payload<br>
             
             {
             	"humanId": "mozart"
             }


developer 1: "humanId"是什么?

developer 2: 这是代表某个具体entity的身份符号，在一个给定的宇宙中，可用作识别某个人类个体对象的唯一识别码

developer 1: 刚才规定的协议里没有"humanId"

developer 2: 这个和协议里的并没有区别，我只不过用了一个看起来不一样的名字，对吧？没关系，我可以改一下的。如果你在意的话

developer 1: "address"变成可选的了？

developer 2: 等一下，address是什么东西？

developer 1: 还有"occupation"呢？

developer 2: 我不需要把occupation发给你-- 你**就是**mozart，你知道你的occupation是什么🙂


> agree and subotage

[办公室里]

developer 1: I agree with everything you said.<br>
        (一边说一边把电脑撕成碎片)



