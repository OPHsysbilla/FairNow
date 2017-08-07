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
