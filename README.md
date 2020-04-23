## 1、地理/逆地理
* 地理编码/逆地理编码API是通过HTTP/HTTPS协议访问远程服务的接口，提供结构化地址与经纬度之间的相互转化的能力。

```
import requests # 用于api http request
# api使用请求参数
key = 'ddbf84e30516af49ba00bd1afa61a233'
def geo(address:str,city=None)-> dict:
    parameters = {'key':key,
                 'city':city,
                 'citylimit': True,
                 'address':address
                 }
    r = requests.get ("https://restapi.amap.com/v3/geocode/geo?parameters", params=parameters)
    data = r.json()['geocodes'][0]['location']
    return data
```

```
geo("广东省广州市从化区中山大学南方学院","广州")
```

```
geo("广东省广州市从化区温泉镇政府","广州")
```
## 2、路径规划
* 路径规划API是一套以HTTP形式提供的步行、公交、驾车查询及行驶距离就散接口、返回JSON或XML格式的查询数据，用于实现路径规划功能的开发。

```
def bicycling(origin,destination,sig=None)->dict:
    url = 'https://restapi.amap.com/v3/direction/walking?parameters'
    params = {
        'key':key,
        'origin':origin,
        'destination':destination,
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data

```

```
bicycling(geo("广东省广州市从化区中山大学南方学院","广州"),geo ("广东省广州市从化区温泉镇政府","广州"))
```
## 3、行政区域查询
* 行政区域查询是一类简单的HTTP接口，根据用户输入的搜索条件可以帮助用户快速的查找特定的行政区域信息。

```
def district(keywords,subdistrict=None,page=None,offset=None,extensions='base',filter=None)->dict:
    url = 'https://restapi.amap.com/v3/config/district?parameters'
    params = {
        'key':key,
        'keywords':keywords,
        'subdistrict':subdistrict,
        'page':page,
        'offset':offset,
        'extensions':extensions,
        'filter':filter,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data
```

```
district ('广东')
```
## 4、搜索POI
* 搜索服务API是一类简单的HTTP接口，提供多种查询POI信息的能力，其中包括关键字搜索、多变形搜索、ID查询四种筛选机制。

```
def poi(keywords:str,city:str)-> dict:
    parameters = {
        'key':key,
        'city':city,
        'keywords':keywords,
        'output':'json'
    }
    r = requests.get ("https://restapi.amap.com/v3/place/text?parameters", params=parameters)
    data = r.json()
    pois = []
    pois.extend(data['pois'])
    import pandas as pd
    df_input = pd.DataFrame(pois)
    return df_input
```

```
data = poi("麦当劳","广东")
data
```
## 5、IP定位
* IP定位是一个简单是HTTP接口，根据用户输入的IP地址，能够快速的帮用户定位IP的所在位置

```
def ip(ip=None,sig=None)->dict:
    url = 'https://restapi.amap.com/v3/ip?parameters'
    params = {
        'key':key,
        'ip':ip,
        'sig':sig,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data
```

```
ip ('116.26.153.153')
```
## 6、静态地图
* 静态地图服务通过返回一张地图图片响应HTTP请求，使用户能够将高德地图以图片形式嵌入自己的网页中。

```
from PIL import Image
from io import BytesIO
def map(location,zoom,size=None,scale=1,markers=None,labels=None,paths=None,traffic=0,page=None,sig=None)-> dict:
    url = 'https://restapi.amap.com/v3/staticmap?parameters'
    params={
        'key':key,
        'location':location,
        'zoom':zoom,
        'size':size,
        'scale':scale,
        'markers':markers,
        'labels':labels,
        'paths':paths,
        'traffic':traffic,
        'sig':sig,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = Image.open(BytesIO(response.content))
    return data
```

```
# 汕头位置地图
map(location='116.141172,23.0223',zoom=7)
```
## 7、坐标转换
* 坐标转换是一类简单的HTTP接口，能够将用户输入的非高德坐标（GPS坐标、mapbar坐标、baidu坐标）转换成高德坐标。

```
# 将汕头的经纬度转换为GPS坐标
def cs(locations,coordsys=None,sig=None,)-> dict:
    url = 'https://restapi.amap.com/v3/assistant/coordinate/convert?parameters'
    params={
        'key':key,
        'locations':locations,
        'coordsys':coordsys,
        'sig':sig,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data
```

```
cs(locations='116.141172,23.0223',coordsys='gps')
```
## 8、天气查询
* 天气查询是一个简单的HTTP接口，根据用户输入的adcode，查询目标区域当前/未来的天气情况。

```
def tq(city,extensions='base')-> dict:
    url = 'https://restapi.amap.com/v3/weather/weatherInfo?parameters'
    params={
        'key':key,
        'city':city,
        'extensions':extensions,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data
```

```
tq(city='440514')
```
## 9、输入提示
* 输入提示是一类简单的HTTP接口，提供根据用户输入的关键词查询返回建议列表。

```
def sr(keywords,type="分类代码",location=None,city=None,citylimit='false',datatype='all',sig=None)-> dict:
    url = 'https://restapi.amap.com/v3/assistant/inputtips?parameters'
    params={
        'key':key,
        'type':type,
        'keywords':keywords,
        'location':location,
        'city':city,
        'citylimit':citylimit,
        'datatype':datatype,
        'sig':sig,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data
```

```
sr(keywords="肯德基",city="440514")
```
## 10、交通态势
* 交通态势是一类简单的HTTP接口，提供根据用户输入的内容能够返回希望查询的交通态势情况。

```
def jt(location,level=None,extensions='base',sig=None,radius=None)-> dict:
    url = 'https://restapi.amap.com/v3/traffic/status/circle?parameters'
    params={
        'key':key,
        'location':location,
        'level':level,
        'location':location,
        'extensions':extensions,
        'radius':radius,
        'sig':sig,
        'output':'json'
    }
    response = requests.get(url,params=params)
    data = response.json()
    return data

```

```
jt(location='116.3057764,39.98641364',radius='1500')
```
## 11、地理围栏
* 地理围栏服务是一类HTTP接口，提供在服务端，增删改查地理围栏的功能，同时支持对于设备与围栏关系进行监控。

```
# 创建地理围栏
def meta(name=None,center=None,radius="2000",enable="True",valid_time="2020-05-19",repeat="Mon,Tues,Wed,Thur,Fri,Sat,Sun",time="00:00,11:59;13:00,20:59",desc="测试围栏描述",alert_condition="enter;leave")->dict:
    url = 'https://restapi.amap.com/v4/geofence/meta?key=ddbf84e30516af49ba00bd1afa61a233'
    params={
        'key':key,
    }
    data = {
        "name":name,
        "center":center,
        "radius":radius,
        "enable":enable,
        "valid_time":valid_time,
        "repeat":repeat,
        "time":time,
        "desc":desc,
        "alert_condition":alert_condition,
    }
    response = requests.post(url,params=params,json=data)
    data = response.json()
    return data
```

```
qu = meta(name='名称',center='116.481498,39.990375')
qu
```
## 12、轨迹纠偏
* 根据坐标点抓取道路，即根据给定的坐标点、车辆的方位角以及行驶速度，将用户的轨迹纠偏到路上，从而返回用户实际驾车经过的道路坐标。

```
def grasproad()-> dict:
    url = 'https://restapi.amap.com/v4/grasproad/driving'
    params={
        'key':key,
    }
    data = [{
"x": 116.449429,
"y": 40.014844,
"sp": 4,
"ag": 110,
"tm": 1478831753
}, {
"x": 116.449639,
"y": 40.014776,
"sp": 3,
"ag": 110,
"tm": 23
}, {
"x": 116.449859,
"y": 40.014716,
"sp": 3,
"ag": 111,
"tm": 33
}, {
"x": 116.450074,
"y": 40.014658,
"sp": 3,
"ag": 110,
"tm": 31
}, {
"x": 116.450273,
"y": 40.014598,
"sp": 3,
"ag": 111,
"tm": 20
}]
    response = requests.post(url,params=params,json=data)
#response.status_code
#response.content
    result = response.json()
    return result
```

```
result
```
## 13、批量接口
* 批量接口通过用户传入合并后的请求，同时返回多个请求的顺序集合，目前最多支持20个子请求。

```
def batch()->dict:
    url = "https://restapi.amap.com/v3/batch?key=ddbf84e30516af49ba00bd1afa61a233"
    headers = {
    'Content-Type':'application/json',
    }
    data = {
    "ops": [
        {
            "url": "/v3/place/around?offset=10&page=1&key=<您的key>&location=116.50394379585519,39.278209477408794&output=json&radius=100000&types=080000"
        },
        {
            "url": "/v3/place/around?offset=10&page=1&key=<您的key>&location=118.50394379585519,39.278209477408794&output=json&radius=100000&types=080000"
        }
    ]
}
    params = {
        "keyt":key,
    }
    response = requests.post(url,headers=headers,params=params,json=data)
    response.status_code
    #response.content
    #results = response.json()
    return results
```
`results`

