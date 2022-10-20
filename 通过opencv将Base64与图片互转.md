---
title: 通过opencv将Base64与图片互转
tags: python，opencv，base64
---
```python
import base64
import cv2
import numpy as np
from typing import AnyStr

'''
Function: Convert image's ndarray data to base64 string.
Arguments:
  img_ndarray: Image's ndarray data.
  extension: Image's extension.
Return:
  Image's base64 string.
'''
def ndarray_to_base64_str(img_ndarray: np.ndarray, extension: str) -> AnyStr:
    array = cv2.imencode(extension, img_ndarray)[1]
    base64_str = str(base64.b64encode(array), encoding="utf-8")

    return base64_str
	
'''
Function: Convert base64 string to image's ndarray data.
Arguments:
  base64_str: Image's base64 string.
Return:
  Image's ndarray data.
'''
def base64_str_to_ndarray(base64_str: str) -> np.ndarray:
    img_bytes = base64.b64decode(base64_str)
    array = np.frombuffer(img_bytes, np.uint8)
    img_ndarray = cv2.imdecode(array, cv2.IMREAD_COLOR)

    return img_ndarray
```