---
title: 'Requests simulate the client to upload files and submit forms, and flask receives them as the background'
tags: requests,flask
---


## flask文件上传操作
```
# gevent
from gevent import monkey
from gevent.pywsgi import WSGIServer
import time

# 下面这句不加也能启动服务，但是你会发现Flask还是单线程，在一个请求未返回时，其他请求也会阻塞，所以请添加这句
# monkey.patch_all()
# gevent end
from flask import Flask, request, jsonify
import base64
import cv2
import numpy as np
import json
from os.path import join

app = Flask(__name__)

@app.route("/upload", methods=["post"])
def upload():
    if request.files:
        # 通过文件的键，获取传来的文件内容
        file = request.files["file"]
        file.save(join("./templates", file.filename))
    return ""

if __name__ == '__main__':
    # app.run(port=5000,host="127.0.0.1")
    http_server = WSGIServer(('127.0.0.1', int(5000)), app)
    http_server.serve_forever()
```
## requests模拟客户端上传文件
```
import cv2
import numpy as np
import requests

# url注意换成自己请求的应用对应的路径
url = 'http://127.0.0.1:5000/upload'
header = {'user-agent': 'Mozilla/5.0'}
# 文件注意换成自己的对应的文件
files = {'file': open('test.txt', 'rb')}
res = requests.post(url=url, headers=header, files=files)
```
## 	requests模拟浏览器提交表单
```
import requests

url = 'http://127.0.0.1:5000/form'
data = {
    "user": "w"
}
res = requests.post(url, data=data)
print(res.status_code)
```
## flask获取表单数据
```
from flask import Flask, request, jsonify
import base64
import cv2
import numpy as np
import json
from os.path import join

app = Flask(__name__)

@app.route("/form", methods=["post"])
def form():
    if request.form:
        user = request.form["user"]
        print(user)
    return ""

if __name__ == '__main__':
    http_server = WSGIServer(('127.0.0.1', int(5000)), app)
    http_server.serve_forever()
```