---
title: Use openvino inference ppyolo and ppyoloe
tags: 'python,openvino'
---

# Step1: Build the environment
## Install the package
[Openvino document](https://docs.openvino.ai/latest/openvino_docs_install_guides_install_dev_tools.html)
```
pip install openvino-dev[extras]
```
Where the extras parameter specifies one or more deep learning frameworks via these values: caffe, kaldi, mxnet, onnx, pytorch, tensorflow, tensorflow2, paddle. Make sure that you install the corresponding frameworks for your models.

## Verify the installation
```
mo -h
```
Notes: mo express model optimizer
You will see the help message for Model Optimizer if installation finished successfully.
# Optional: Convert to ir
Notes: Ir express intermediate representation format.
The OpenVINO IR can be additionally optimized for inference by Post-training optimization that applies post-training quantization methods.
 ## Converting a paddlepaddle model
 For ppyolo:
 ```
mo --output_dir <PATH_TO_OUTPUT_DIR> \
--reverse_input_channels \
--input_shape=[1,3,320,320],[1,2],[1,2] \
--input=image,im_shape,scale_factor
--input_model= < PATH_TO_MODEL_DIR\model.pdmodel>
 ```
 
 For ppyoloe: compare with ppyolo, command not have im_shape node
 ```
 mo --output_dir <PATH_TO_OUTPUT_DIR> \
--reverse_input_channels \
--input_shape=[1,3,640,640],[1,2] \
--input=imagescale_factor
--input_model= < PATH_TO_MODEL_DIR\model.pdmodel>
 ```
 If you not sure model properties, you can use Netron tools to see.
 [Netron client](https://netron.app/)
 ## 0penvino inference engine for ir inference(ppyolo)
```
import numpy as np
import cv2
from openvino.inference_engine import IECore
import time
import yaml
from yaml.loader import SafeLoader
from typing import Dict, List

'''
Function: Preprocess image.
Arguments:
  input_image: Image's ndarray data.
  size: Resize.
Return:
  img.astype(np.float32): Resized image's ndarray data of dtype is np.float32.
'''
def image_preprocess(input_image: np.ndarray, size: int) -> np.ndarray:
    img = cv2.resize(input_image, (size, size))
    img = np.transpose(img, [2, 0, 1]) / 255
    img = np.expand_dims(img, 0)
    '''NormalizeImage: {mean: [0.485, 0.456, 0.406], std: [0.229, 0.224, 0.225], is_scale: True}
       Configuration in yaml file, here is a example.
    '''
    img_mean = np.array([0.485, 0.456, 0.406]).reshape((3, 1, 1))
    img_std = np.array([0.229, 0.224, 0.225]).reshape((3, 1, 1))
    img -= img_mean
    img /= img_std
    return img.astype(np.float32)

'''
Function: Draw a rectangular box.
Arguments:
  img: Image's ndarray data.
  results: Inference results.
  label_list: List of labels.
  scale_x: Width's scale.
  scale_y: Height's scale.
Return:
  img: Image's ndarray of drew a rectangular box.
'''
def draw_box(img: np.ndarray, results: Dict, label_list: List, scale_x: float, scale_y: float) -> np.ndarray:
    for i in range(len(results)):
        bbox = results[i, 2:]
        label_id = int(results[i, 0])
        score = results[i, 1]
        if (score > 0.20):
            xmin, ymin, xmax, ymax = [int(bbox[0] * scale_x), int(bbox[1] * scale_y),
                                      int(bbox[2] * scale_x), int(bbox[3] * scale_y)]
            cv2.rectangle(img, (xmin, ymin), (xmax, ymax), (0, 255, 0), 3)
            font = cv2.FONT_HERSHEY_SIMPLEX
            label_text = label_list[label_id];
            cv2.rectangle(img, (xmin, ymin), (xmax, ymax), (0, 255, 0), 2)
            cv2.putText(img, label_text, (xmin, ymin), font, 1.5, (255, 255, 255), 2, cv2.LINE_AA)
    return img

device = 'CPU'
ie = IECore()
ppyolo_path = r"D:\Python\PaddleDetection\deploy\models\ppyolo_mbv3_large_voc_infer\ir\inference.xml"
net = ie.read_network(ppyolo_path)

net.reshape({'image': [1, 3, 320, 320], 'im_shape': [
            1, 2], 'scale_factor': [1, 2]})

exec_net = ie.load_network(net, device)

label_list = []
pdmodel_config = r"D:\Python\PaddleDetection\deploy\models\ppyolo_mbv3_large_voc_infer\infer_cfg.yml"
with open(pdmodel_config) as f:
    data = yaml.load(f, Loader=SafeLoader)
label_list = data['label_list']

input_image = cv2.imread(r"D:\Python\PaddleDetection\deploy\images\1663742739.6263561-3.jpg")
test_image = image_preprocess(input_image, 320)
test_im_shape = np.array([[320, 320]]).astype('float32')
test_scale_factor = np.array([[1, 2]]).astype('float32')

inputs_dict = {'image': test_image, "im_shape": test_im_shape,
               "scale_factor": test_scale_factor}

start_time = time.time()
output = exec_net.infer(inputs_dict)
end_time = time.time()
print("time: %.2f ms" % (1000 * (end_time - start_time)))

result_ie = list(output.values())
result_image = draw_box(input_image, result_ie[0], label_list, input_image.shape[1] / 320 * 2,
                        input_image.shape[0] / 320)

cv2.imwrite("test.png", result_image)
```
If occur error: 
ImportError: DLL load failed while importing ie_api
If you use python3.8 or higher version, the error resolution as follows:
Enter file: D:\Anaconda\envs\testopenvino\Lib\site-packages\openvino\__init__.py
Find lines 25 and 26:
```
if (3, 8) <= sys.version_info:
	os.add_dll_directory(os.path.abspath(lib_path))
```
Add after line 26:
>os.environ['PATH'] = os.path.abspath(lib_path) + ';' + os.environ['PATH']

Sample yaml file:
```
mode: paddle
draw_threshold: 0.5
metric: VOC
use_dynamic_shape: false
arch: YOLO
min_subgraph_size: 3
Preprocess:
  - interp: 2
    keep_ratio: false
    target_size:
      - 320
      - 320
    type: Resize
  - is_scale: true
    mean:
      - 0.485
      - 0.456
      - 0.406
    std:
      - 0.229
      - 0.224
      - 0.225
    type: NormalizeImage
  - type: Permute
label_list:
  - face
```
For ppyoloe, inputs not have im_shape.
## 0penvino inference engine for model inference(ppyolo)
Compare with ir inference, change the read xml file to a pdmodel file.