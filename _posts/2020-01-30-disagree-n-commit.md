---
title: disagree and commit
date: 2020-01-30 19:17
---

我司有一条企业文化叫做"disagree and commit". 

然而日常生活中比较多见的是以下三种行为模式：

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

[场景1: 会议室里]

(经过1个小时的激烈讨论)

developer 1: You need to send me a request payload that looks like this:<br>
             (在白板上写下来)

             {
             	"name": ...
             	"address": ...
             	"occupation": ...
             }


developer 2: that works!

developer 3: sounds good.

[场景2: 公司聊天群里]

developer 2: Does this json blob work for you?<br>
             
             {
             	"humanId": "mozart"
             }


developer 1: what is "humanId"?

developer 2: an identifier of the entity being represented, it can be used to uniquely identify a human object, given a universe

developer 1: are we sticking to this contract?<br>
             (附上刚才拍的白板照片)

developer 2: that's the same, other than that I'm calling it "humanId" instead of "name", right? I can rename it, doesn't matter

developer 3: I am fine either way.

developer 1: "address" is optional?

developer 2: wait, what's address again?

developer 1: and "occupation"?

developer 2: I don't need to pass you the occupation -- you ARE mozart and you know your occupation 🙂


> agree and subotage

[场景1: 办公室里]

developer 1: I agree with everything you said.<br>
        (一边说一边把电脑撕成碎片)



