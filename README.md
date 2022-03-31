# Deep Learning Framework ToolBox

本目录提供一些个人在模型部署时，常用到的一些工具脚本

- 1. 修改Paddle模型输入Shape

## 1. 修改Paddle模型输入Shape

- 经常在部署推理时，特别是转ONNX后，我们需要模型的输入Shape是固定的，例如OpenVINO、华为Ascend以及其它的推理引擎，但Paddle模型在保存时可能是动态的，例如`[-1, 3, -1, -1]`表示输入的batch维，以及图像高和宽都是动态变化的。
- 也有一些场景是，原本Paddle模型导出来是固定Shape的，例如是`[-1, 3, 640, 640]`，表示高和宽已经固定，但可能我们又会想要让它支持动态。

不过，凡是涉及到修改模型的Shape，一定需要注意模型对输入Shape允许的合法值范围内。例如yolov3, ppocr中，固定shape时，往往需要高和宽为32的倍数，否>则模型推理会出错，这都是与模型的结构设计有关，例如可能在内部有多层下采样操作等等，具体需要根据模型情况而定。

### 开始修改

这里以OCR模型为例，[点击下载](https://paddleocr.bj.bcebos.com/PP-OCRv2/chinese/ch_PP-OCRv2_det_infer.tar)OCR的检测模型，通过Netron可视化这个模型，可以看到其输入名为`x`，形状为`[-1, 3, 960, 960]`(?即为-1)
![image-20220331162225040](imgs/origin.png)  

事实上，这个模型的结构是可以支持动态形态输入的，我们使用`paddle_infer_shape.py`脚本进行重新导出模型，再通过Netron查看模型，可以看到新模型的输入输出都已经更新了
```
python paddle_infer_shape.py --model_dir ch_PP-OCRv2_det_infer/ \
                             --model_filename inference.pdmodel \
                             --params_filename inference.pdiparams \
                             --save_dir new_model \
                             --input_shape_dict="{'x':[-1,3,-1,-1]}" 
```
![image-20220331165925526](imgs/new.png)
