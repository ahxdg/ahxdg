---
title: '[python实例] - 获取国内城市区域划分数据，写cityPicker插件'
date: 2018-03-09 10:58:11
tags:
  - python
---

> 因工作需要，写一个城市选择插件，故在国家统计局的网站上面找了16年的城乡划分数据，以保证数据的及时性和可扩展性。

通过访问链接http://www.stats.gov.cn/tjsj/tjbz/tjyqhdmhcxhfdm/2016/index.html 可以看到这个页面

![统计局截图](http://p5azbprn0.bkt.clouddn.com/blog-1.png)

点开对应链接，则可以看到相应级别的区域划分信息

![统计局截图](http://p5azbprn0.bkt.clouddn.com/blog-2.png)

根据页面的dom节点信息，得到我们相应的数据。

代码如下：

```python
import re
import requests
import json

data = {}
pattern =re.compile(u"[\u4e00-\u9fa5]+")

# 写数据
def writeJson(jso):
  jsObj = json.dumps(jso)
  fileObject = open('city.json', 'w') 
  fileObject.write(jsObj)  
  fileObject.close()  

# 获取city数据
class GetCityData:
  def __init__(self, url):
    self.url = url
    self.openUrl()

  # 打开根页面
  def openUrl(self):
    txt = self.getTxt(self.url)
    res = re.findall(r'provincetr\'><td>(.*)</td></tr>', txt)
    res2 = re.findall(r'\d{2}', res[0])
    res3 = re.findall(pattern, res[0])
    for (x1, x2) in zip(res2, res3):
      data[x1] = {'prov': x2,'child':{}}
      print(data[x1])
      self.twoLevel(x1)
    # print(data)
    writeJson(data)

  def twoLevel(self, u):
    u = 'http://www.stats.gov.cn/tjsj/tjbz/tjyqhdmhcxhfdm/2016/'+ u +'.html'
    txt = self.getTxt(u)
    res = re.findall(r'citytr\'><td>(.*?)</td></tr>', txt)
    for x in res:
      href = re.findall(r'\d*\/\d*', x)
      ss = href[0].split('/')
      data[ss[0]]['child'][ss[1]] = {'muni': re.findall(pattern, x)[0], 'child': {}}
      print(data[ss[0]]['child'][ss[1]])
      self.threeLevel(ss)
  def threeLevel(self, us):
    txt = self.getTxt('http://www.stats.gov.cn/tjsj/tjbz/tjyqhdmhcxhfdm/2016/' + us[0] + '/' + us[1] + '.html')
    res = re.findall(r'countytr\'><td>(.*?)</td></tr>', txt)
    for x in res:
      href = re.findall(r'\d*\/\d*', x)
      ss = href[0].split('/')
      data[us[0]]['child'][us[1]]['child'][ss[1]] = {'county': re.findall(pattern, x)[0]}
      print(data[us[0]]['child'][us[1]]['child'][ss[1]])

  def getTxt(self, u):
    headers = {'user-agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.167 Safari/537.36'}
    m_res = requests.get(u, headers=headers)
    m_res.encoding = 'gb2312'
    return m_res.text

GetCityData('http://www.stats.gov.cn/tjsj/tjbz/tjyqhdmhcxhfdm/2016/index.html')
```