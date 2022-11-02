---
title: Calculate the area of intersection of two rectangles  
tags: python
---


```python
import numpy as np
from typing import List


'''
Function: Calculate the area of intersection of two rectangles.
Arguments: 
  rec1: One of the rectangles, of type list, with the list format[xmin,yminx,xmax,ymax].
  rec2: Another list.
Return:
  intersection_area: The area of intersection of two rectangles.
'''


def calculate_intersection_area(rec1: List, rec2: List) -> int:
    x1min, y1min, x1max, y1max = rec1[0], rec1[1], rec1[2], rec1[3]
    x2min, y2min, x2max, y2max = rec2[0], rec2[1], rec2[2], rec2[3]

    '''Coordinates of the intersecting rectangle.'''
    xmin = np.maximum(x1min, x2min)
    ymin = np.maximum(y1min, y2min)
    xmax = np.minimum(x1max, x2max)
    ymax = np.minimum(y1max, y2max)

    inter_h = np.maximum(ymax - ymin + 1., 0.)
    inter_w = np.maximum(xmax - xmin + 1., 0.)
    intersection_area = inter_h * inter_w

    return intersection_area
```