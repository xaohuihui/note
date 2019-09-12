## 爬虫
### crawl sn

https://github.com/Iwty  #神人


```python
import requests
import csv


# 数据存储本地csv
def save(list):
print(list)
csvFile = open(fr'{ky}.csv', 'a', newline='', encoding='utf-8-sig') # 设置newline，否则两行之间会空一行
writer = csv.writer(csvFile)
writer.writerow(list)
csvFile.close()


def spider(ky):
url_list = [
f'https://fast.suning.com/search/emall/mobile/clientSearch.jsonp?set=5&ps=10&channelId=MOBILE&keyword={ky}&st=0&ci=&cf=&sc=&cp={page}&iv=-1&ct=-1&sp=&spf=&prune=1&sg=1&operate=0&istongma=1&sesab=ABAABABB&nonfirst=1&v=1.27&snyp=0&jzq=17708&lx='
for page in range(601)]
# 遍历全部商品
for url in url_list:
r = requests.get(url, verify=False)
print(url)
break
if len(r.text) < 1000:
break
else:
data_list = r.json()['goods']
for data in data_list:
try:
# 获取商品信息字段
title = data["catentdesc"]
price = data["price"]
shop_id = data["salesCode"]
shop_name = data["salesName"]
goods_id = data["catentryId"]
detail_url = f'https://product.suning.com/{shop_id}/{goods_id}.html'
praiseRate = data["praiseRate"]
image = 'https:' + data["dynamicImg"]
comment = data["extenalFileds"]["commentShow"]

info = [title, price, shop_name, shop_id, goods_id, praiseRate, comment, image, detail_url]
save(info)
except:
pass


if __name__ == '__main__':
ky = input('请输入需要爬取的商品名:')
#save(['标题', '价格', '店铺名', '店铺id', '商品id', '好评率', '评价数', '商品展示', '详细页面'])
spider(ky)

```
