---
layout: post
title:  "Remote Notification Payload"
categories: iOS 
---

```json
{
  "aps" : {
    "mutable-content" : 1,
    "alert" : {
      "title" : "测试",
      "body" : "Your message Here"
    },
    "badge" : 1,
    "sound" : "default"
  },
  "ext" : {
    "media-attachment" : "http://img1.gtimg.com/sports/pics/hv1/194/44/2136/138904814.jpg",
    "act" : "http://xxxxxx?cAct=xxx",
    "infoAct" : "http://xxxxxx?cAct=xxx",
    "stat" : {
      "type" : 1
    }
  }
}
```