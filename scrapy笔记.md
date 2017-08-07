# scrapy笔记

**手机网络**

- [手机网易]http://3g.163.com
- [手机qq]http://portal.3g.qq.com 
- [UC热点]https://news.uc.cn/c_redian/

---

事实上手机版由于加载更多的存在并不好直接爬取，所以转换方式选择 [网易新闻](http://news.163.com/) ，抓包观察发现其中的`cm_yaowen.js`会返回json数据

> http://temp.163.com/special/00804KVA/cm_yaowen_04.js?callback=data_callback

格式为：
- ...cm_yaowen.js...
- ...cm_yaowen_01.js...
- ...cm_yaowen_02.js...
- ...cm_yaowen_04.js...



####最新评论
> http://comment.news.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CR67N2K50001899N/comments/newList?offset=0&limit=40&showLevelThreshold=72&headLimit=1&tailLimit=2&callback=getData&ibc=newspc&_=1502083131717

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
    "postId":"CR67N2K50001899N_148774978",
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


####热评
> http://comment.news.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CR67N2K50001899N/comments/hotList?offset=0&limit=40&showLevelThreshold=72&headLimit=1&tailLimit=2&callback=getData&ibc=newspc

和上面所需信息一样
