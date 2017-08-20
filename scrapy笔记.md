# scrapy笔记

**手机网络**

- [手机网易]http://3g.163.com
- [手机qq]http://portal.3g.qq.com 
- [UC热点]https://news.uc.cn/c_redian/

---

事实上手机版由于加载更多的存在并不好直接爬取，所以转换方式选择 [网易新闻](http://news.163.com/) 

# 网易新闻
[网易新闻](http://news.163.com/)，抓包观察发现其中的`cm_yaowen.js`会返回json数据：
> http://temp.163.com/special/00804KVA/cm_yaowen_04.js?callback=data_callback

只需要改为：
- ...cm_yaowen.js...
- ...cm_yaowen_01.js...
- ...cm_yaowen_02.js...
- ...cm_yaowen_04.js...



### 最新评论
> http://comment.news.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CR67N2K50001899N/comments/newList?offset=0&limit=40&showLevelThreshold=72&headLimit=1&tailLimit=2&callback=getData&ibc=newspc&_=1502083131717
####
1. 一般只需要改 `offset=0`，`_=时间戳`，`a2869674571f77b5a0867c3d71db5856`是productKey，似乎是不变的？可能跟IP有关，但是我换VPN也没有变，`CR67N2K50001899N`是帖子id
2. 当参数错误时：
> ```
> getData(
> {"code":"42212","message":"Illegal pagination parameters"});
> ```
当`offset`超过上限会变成下面这样：( 其中`newListSize`是上限 )
```
getData(
{"commentIds":[],"comments":{},"newListSize":9037});
```

---

```
"148774978":{
    "against":0 # **反对数**
    "vote":4 # **赞同数**
    "content":"犯中华者，虽远必诛！...其中的诛和口诛笔伐的诛一样，所以，抗议，谴责也算是诛过了！",  **内容**
    "createTime":"2017-08-07 12:56:53", **时间**
    "productKey":"a2869674571f77b5a0867c3d71db5856", # 是网址中products后的数字串
    "user":{
        "id":"OTk3NjI3NzEwQHFxLmNvbQ==",  
        "location":"湖南省株洲市攸县",  **地址**
        "nickname":"做人糊涂好",  **名字**
        "redNameInfo":[],
        "userId":55412370
      },
    "ip":"113.245.*.*",
    "postId":"CR67N2K50001899N_148774978", **格式是[帖子id_发言用户id]**
    "anonymous":false
    "buildLevel":3,  #更了多少层
    "commentId":148774978,  
    "source":"wb", # wb是web, ph是phone
    "unionState":false, # 总是false
    "siteName":"网易", 
    "isDel":false,
    "shareCount":0,
    "favCount":0,
}
```

### 热评
> http://comment.news.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CR67N2K50001899N/comments/hotList?offset=0&limit=40&showLevelThreshold=72&headLimit=1&tailLimit=2&callback=getData&ibc=newspc

和上面所需信息一样

### 正文
直接正则内容

# 澎湃
[澎湃新闻](http://www.thepaper.cn/)，XHR格式抓包：
> http://www.thepaper.cn/load_chosen.jsp?nodeids=25949&topCids=1754916,1752511,1755796,1755438,&pageidx=3&lastTime=1502177429969

对比采集到的多条jsp：

>>* http://www.thepaper.cn/load_chosen.jsp?nodeids=25949&topCids=1754916,1752511,1755796,1755438,&pageidx=12&lastTime=1502123413588
>>* http://www.thepaper.cn/load_chosen.jsp?nodeids=25949&topCids=1754916,1752511,1755796,1755438,&pageidx=11&lastTime=1502150956482
>>* http://www.thepaper.cn/load_chosen.jsp?nodeids=25949&topCids=1754916,1752511,1755796,1755438,&pageidx=9&lastTime=1502156924558
>>* http://www.thepaper.cn/load_chosen.jsp?nodeids=25949&topCids=1754916,1752511,1755796,1755438,&pageidx=9&lastTime=1502156924558

所以我们只要改变`pageidx=3`和`lastTime=(10位当前系统时间)`就可以了，10位当前系统时间在`Java`下直接用`Systemillions()`就可以，`Python`需要再取整

## 推荐阅读内容
> [python爬虫的最佳实践](http://www.jianshu.com/u/d2170a042ddb)
包含mongodb、动态加载、selenium、抓包在内的内容

