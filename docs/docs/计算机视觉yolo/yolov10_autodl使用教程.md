
```
pytorch =2.00 ubuntu20.04 cuda 11.8 python3.8
```
```
git clone https://github.com/THU-MIG/yolov10.git

conda create -n yolov10 python=3.9
conda init
#新开一个终端或者重启一个终端
conda activate yolov10
cd yolov10
pip install -r requirements.txt
pip install -e . -i https://pypi.tuna.tsinghua.edu.cn/simple

cd ultralytics
mkdir datasets
cd datasets
#创建数据集文件夹
mkdir dianchi 
cd dianchi
#上传数据集，解压
unzip xxx.zip
#下载权重
wget https://github.com/THU-MIG/yolov10/releases/download/v1.1/yolov10n.pt

#训练模型
yolo task=detect mode=train model=datasets/yolov10n.pt epochs=10 batch=1 data=datasets/data.yaml




```
