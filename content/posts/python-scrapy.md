---
share: "true"
title: python-scrapy
date: 2018-01-28 22:52:21
tags:
  - python
  - 爬虫
---
# scrapy爬虫框架
## 入门
* 安装
* 开始项目以及创建爬虫(参照命令行工具)

		scrapy startproject myproject  //初始化项目
		cd myproject
		scrapy genspider mydomain mydomain.com  //创建spider
<!--more-->
* 项目的文件目录以及功能
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/20686924.jpg)
	* item.py: 数据格式的定义
	* (爬虫名称).py: 具体的爬虫逻辑
	* pipelines.py: 对爬虫得到的数据(item进行处理)
	* settings.py: 对爬虫的一些定义(比如指定pipelines等)
* 除此之外,比较实用的命令还有
	* 以给定的URL(如果给出)或者空(没有给出URL)启动Scrapy shell,运行之后你可以在命令行中使用response选择器,这样就可以进行网页页面的分析而不用每次都启动spider

			scrapy shell "url"
			....略过执行代码
			response.xpath("//h1[@class='post-title']/a") //选择器

	* 运行指定的spider


			scrapy crawl <spider>  -o 输出的文件

	* 更多的命令可以查看[http://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/commands.html](http://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/commands.html)

* 自定义爬虫item(items.py)
	
		import scrapy
		class TutorialItem(scrapy.Item):
		    # define the fields for your item here like:
		    # name = scrapy.Field()
		    title = scrapy.Field()
		    link = scrapy.Field()
    		# pass
* 定义爬虫逻辑(爬虫名称.py)

		class WuweizhaoSpider(scrapy.Spider):
		    name = 'wuweizhao'
		    allowed_domains = ['wuweizhao.com']
		    start_urls = ['http://wuweizhao.com/']
		
		    def parse(self, response):
		        list = response.xpath("//h1[@class='post-title']/a")
		        url = "http://wuweizhao.com/"
		        for a in list:
		            yield scrapy.Request(url + a.xpath("./@href")[0].extract(), callback=self.parse_contents)
		        next_page = response.xpath("//nav/a[@class='extend next']/@href").extract_first()
		        if next_page is not None:
		            next_page = url + next_page
		            print(next_page)
		            next_page = response.urljoin(next_page)
		            yield scrapy.Request(next_page, callback=self.parse)
		    def parse_contents(self, response):
		        item = TutorialItem()
		        item["title"] = response.xpath("//h1[@class='post-title']/text()").extract_first().replace("\n", "").strip()
		        item["link"] = response.request.url
		        yield item

	* start_urls爬虫启动时访问的url
	* yield关键字:
		* 新的Request:会将url加入到访问队列当中
		* item(继承scrapy.Item的类):输出到命令行或者相应的文件当中(根据启动参数而定)

* 自定义item处理(如果有需要的话)
	* 修改settings.py中的选项ITEM_PIPELINES
	* 修改pipelines.py

			class TutorialPipeline(object):
			    def __init__(self):
			        pass
			    def process_item(self, item, spider):
			        return item

## 参考
[http://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/overview.html](http://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/overview.html)
[http://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/selectors.html](http://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/selectors.html)

