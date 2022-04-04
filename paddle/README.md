# 修改Paddle模型输入Shape

以OCR模型为例，[点击下载](https://paddleocr.bj.bcebos.com/PP-OCRv2/chinese/ch_PP-OCRv2_det_infer.tar)OCR的检测模型，通过Netron可视化这个模型，可以看到其输入名为`x`，形状为`[-1, 3, 960, 960]`(?即为-1)
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
