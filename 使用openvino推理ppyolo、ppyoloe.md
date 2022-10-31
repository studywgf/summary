---
title: Use openvino inference ppyolo and ppyoloe
tags: 'python,openvino'
---


## Install the package
[openvino document](https://docs.openvino.ai/latest/openvino_docs_install_guides_install_dev_tools.html)
```
pip install openvino-dev[extras]
```
where the extras parameter specifies one or more deep learning frameworks via these values: caffe, kaldi, mxnet, onnx, pytorch, tensorflow, tensorflow2, paddle. Make sure that you install the corresponding frameworks for your models.

## Verify the installation
```
mo -h
```
You will see the help message for Model Optimizer if installation finished successfully.
 ## Converting a paddlepaddle model
 