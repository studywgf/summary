---
title: Opencv basic operation
tags: opencv
---
```
"""
OpenCV图片操作
"""
import cv2

# 图片路径
img_path = "temp.jpg"
# 读取图片,返回类型为ndarray
img = cv2.imread(img_path)
# 画矩形
img = cv2.rectangle(img, [0, 0], [100, 100], (0, 255, 0), 3)
# 图片展示
cv2.imshow("img", img)
# 0：等待任意按键关闭窗口
# 其他数字，如10：窗口在10ms内任意按键关闭窗口，不然10ms后自动关闭窗口
cv2.waitKey(10)
# 图片存储 img.size > 0 可以避免报_img.empty()错误
if img.size > 0:
    cv2.imwrite("demo.jpg", img)
```
```
"""
OpenCV视频操作
"""
import cv2 as cv

# 创建视频采集对象
video = cv.VideoCapture()
# 视频路径
video_path = "20220214_20220214161709_20220214163127_161544.mp4_20220216_111756.mkv"
# 创建视频流
video.open(video_path)
# 判断是否成功创建视频流
while video.isOpened():
    # ret判断视频是否结束，frame获取到视频流的帧，即图片
    ret, frame = video.read()
    if ret is True:
        cv.imshow('video', frame)
        # FPS帧率
        cv.waitKey(int(1000 / video.get(cv.CAP_PROP_FPS)))
    else:
        break
# 输出相关参数信息
# 宽
print('视频中的图像宽度{}'.format(video.get(cv.CAP_PROP_FRAME_WIDTH)))
# 高
print('视频中的图像高度{}'.format(video.get(cv.CAP_PROP_FRAME_HEIGHT)))
# 帧数
print('视频帧数{}'.format(video.get(cv.CAP_PROP_FRAME_COUNT)))
# 释放资源并关闭窗口
video.release()
cv.destroyAllWindows()
```
```
"""
实现使用OpenCV写中文
"""
import cv2
from PIL import Image, ImageDraw, ImageFont
import numpy as np


def cv2AddChineseText(img, text, position, textColor=(0, 255, 0), textSize=30):
    # 判断是否OpenCV图片类型
    if (isinstance(img, np.ndarray)):
        img = Image.fromarray(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
    # 创建一个可以在给定图像上绘图的对象
    draw = ImageDraw.Draw(img)
    # 字体的格式
    fontStyle = ImageFont.truetype(
        "msyh.ttc", textSize, encoding="utf-8")
    # 绘制文本
    draw.text(position, text, textColor, font=fontStyle)
    # 转换回OpenCV格式
    return cv2.cvtColor(np.asarray(img), cv2.COLOR_RGB2BGR)
```
其中对于系统字体文件的位置如下查找：
![enter description here](https://img-blog.csdnimg.cn/69193477d97c4708be6d3200a3efd962.png)
