```
cd work/PaddleDetection-2.4.0 && pip install -r requirements.txt && python setup.py install

cd ~
unzip /home/aistudio/data/data256231/Car2024.zip -d /home/aistudio/work/PaddleDetection-2.4.0/dataset/voc/

#修改文件
#训练
export CUDA_VISIBLE_DEVICES=0
cd ~
cd work/PaddleDetection-2.4.0 && python tools/train.py -c configs/yolov3/yolov3_mobilenet_v1_ssld_270e_voc.yml --eval

#推理
cd ~
cd  work/PaddleDetection-2.4.0 && python tools/infer.py \-c configs/yolov3/yolov3_mobilenet_v1_ssld_270e_voc.yml \-o weights=output/yolov3_mobilenet_v1_ssld_270e_voc/best_model.pdparams \--infer_dir=output/images \--output_dir=output/test
#导出
cd ~
cd work/PaddleDetection-2.4.0 && python tools/export_model.py -c configs/yolov3/yolov3_mobilenet_v1_ssld_270e_voc.yml -o weights=output/yolov3_mobilenet_v1_ssld_270e_voc/best_model.pdparams --output_dir=output_inference

#打包模型
zip -r model.zip yolov3_mobilenet_v1_ssld_270e_voc 
```
