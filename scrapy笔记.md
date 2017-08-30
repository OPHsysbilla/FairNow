# scrapy笔记

**手机网络**

- [手机网易]http://3g.163.com
- [手机qq]http://portal.3g.qq.com 
- [UC热点]https://news.uc.cn/c_redian/

---

事实上手机版由于加载更多的存在并不好直接爬取，所以转换方式选择 [网易新闻](http://news.163.com/) 
## Srart
1. 从排行`http://news.163.com/rank/` 中匹配`更多`的地址,`class="more"`可以完美匹配所符合的` response.css('.more>a::attr(href)').extract()`，除了最后一个 `rank_m` 会导向到移动端
3. class = area areabg1，选择/html/body/div[4]
2. 正则匹配更多里的所有地址，保留 `href="http://news.163.com/....`，里面有帖子id
3. 每篇新闻先检测帖子id是否进去过
4. 没进过，选择#endText中<p>标签内所有中文，抛弃图片<img>，删掉<strong> 等  
> 没有#endText，则说明这是一篇图集，直接读取`<textarea name="gallery-data" style="display:none;">`内的内容，是json格式的
> 同时图集的帖子id不是点进去的地址，是需要在网页中搜索"docId"，才是真正的帖子id
```	
 var config = {
		"productKey" : "a2869674571f77b5a0867c3d71db5856",
		"docId" :  "PHOT25AKN000100A",
 }
```



## 网易新闻
[网易新闻](http://news.163.com/)，抓包观察发现其中的`cm_yaowen.js`会返回json数据：
> http://temp.163.com/special/00804KVA/cm_yaowen_04.js?callback=data_callback

只需要改为：
- ...cm_yaowen.js...
- ...cm_yaowen_01.js...
- ...cm_yaowen_02.js...
- ...cm_yaowen_04.js...

1. cm_yaowen_01 (00是没有的)

> guoji

> guonei

> shehui

> war 

* http://house.163.com/special/00078GU7/shanghai2_xw_news_v1.js?callback=data_callback

* http://temp.163.com/special/00804KVA/cm_dujia.js?callback=data_callback(独家一般是[槽值])


<!> 已知 docId 得到 url

> http://temp.163.com/special/00804KV1/post1603_api_all.js?callback=callback

#### 不同种类的新闻



1.匹配.163.com/\d{2}/\d{4}/\d{2}/疑似16个\w.html
  (图集photoview另算)

2.寻找docId

3.寻找content或者endText，找不到则说明还有其他正文id，放弃即可

4.拼接得新commentApi，每次调取offset写新的

- [直播] http://v.163.com/paike/VCHRH7PA5/VJR8LCOM5.html
    - 属于others，只需要评论没有正文
    - docId是 docId:xx 的形式而不是 "docId" : "xxx"的json格式
    - 评论比较没营养
- [专题 网易号]http://dy.163.com/v2/article/detail/CSBR18FG0515GFLV.html    
    - id = 'content'
    - 普通方式在html中搜索docId，拼接CommentApi             


另一种sdk-api要求很严格：[接口](http://sdk.comment.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CSBR18FG0515GFLV/comments/newList?offset=0&limit=2&showLevelThreshold=70&headLimit=1&tailLimit=2&ibc=jssdk&callback=tool1009860388490201624_1503377054054&_=1503377054055
)



1. 网易彩票：http://cai.163.com/article/17/0821/14/CSCATAAP00052DT2.html

和普通新闻一样的


2. 房产图集：http://gz.house.163.com/photonew/5N620087/72861.html#p=CQP6K0JD5N620087NOS

  - 和普通图集一样
  - 不用正文直接评论即可
  
3.槽值：http://caozhi.news.163.com/17/0821/12/CSC5PTA2000181TI.html

  - id = 'endText' 
  - 普通方式在html中搜索docId，拼接CommentApi       
  
  
4.图集：获得图集的唯一方式恐怕是爬取链接

  - http://new(种类).163.com/photoview/00AO0001/2272018.html#p=CSECHHJ300AO0001NOS   

  - 主要是 photoview
  
  -  (暂时不包括图集内容)
  
  - name="gallery-data"  来自于 <textarea name="gallery-data" style="display:none;">
  
  - textarea是唯一的，gallery-data也是唯一的
  
  - 匹配其后的json就可以获得数据
  
  - 普通方式在html中搜索docId，拼接CommentApi           
  
  - 如下：
  
		```
		{"info":{ 
		    setname,lmodify,source
		    有prev和next的图集链接，匹配xx.163.com/photoview/本图集数字加减可得链接
		    }
		 "list":[
		    {匹配list中每一项的note是文字}             
		 ]
		}
		```
		
 > 图集链接中的p不是真正的commentApi所需要的docId
      
      


### 最新评论
> http://comment.news.163.com/api/v1/products/a2869674571f77b5a0867c3d71db5856/threads/CR67N2K50001899N/comments/newList?offset=0&limit=40&showLevelThreshold=72&headLimit=1&tailLimit=2&callback=getData&ibc=newspc&_=1502083131717

####
1. 一般只需要改 `offset=0`，`_=时间戳`(时间戳会多出小数点后几位，并且服务器时间戳要慢几秒)，`a2869674571f77b5a0867c3d71db5856`是productKey，似乎是不变的？可能跟IP有关，但是我换VPN也没有变，`CR67N2K50001899N`是帖子id
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

