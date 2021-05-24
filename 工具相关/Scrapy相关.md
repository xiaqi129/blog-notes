# Scrapy相关

## 创建Scrapy项目指令

```shell
scrapy startproject articleSpider
```

## 创建爬取网站链接指令

```shell
scrapy genspider example example.com
```

### Robotstxt obey设置为false

```python
# settings.py

ROBOTSTXT_OBEY = False
```

## 命令直接调试url

```shell
scrapy shell http://www.jobbole.com/caijing/gsyw/165745/

>>>title = response.xpath('//div[@class="ship_wrap"]/h2/text()').extract()[0]
>>>create_time = response.xpath('//div[@class="meta"]/span/text()').extract()[0]
```

