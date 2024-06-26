```shell
主要步骤如下，电池数据集在https://universe.roboflow.com/elevator/elevator-nmvwp/dataset/4/download，
已经上传到GitHub小号上了，可直接git clone，几个关键的步骤点如下，记录一下，下次用比较快
具体的准备步骤太多了，就不写了，
```


```
艹，阿里云dws总是抽风，conda创建显示404
工单解决方法：
您好，可以cd /root/下，vim .condarc进入，里面的内容都删掉，保存下，然后再试下呢。
按i 编辑，删掉全部，按esc，在打  :wq! 退出即可

```
```
conda create -n yolov9 python=3.8

```
```
忘记说了，git 仓库一般不在conda环境下克隆，因为我试过，克隆了但是文件不显示
```
```
git clone https://github.com/WongKinYiu/yolov9.git
```
```
pip换源：
-i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple --trusted-host=https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

pip install -r requirements.txt
```
```
cd yolov9
cd data

git clone https://github.com/hututusb/dianchi.git

#把电池数据集移到data根目录
unzip yolov9-dianchi2.v1i.yolov9.zip
#下载权重weight
cd ..
mkdir weights
cd weights
wget https://hub.gitmirror.com/github.com/WongKinYiu/yolov9/releases/download/v0.1/yolov9-c-converted.pt
wget https://hub.gitmirror.com/github.com/WongKinYiu/yolov9/releases/download/v0.1/yolov9-e-converted.pt



```
```
#修改detect.py
weights ="weights\\yolov9-c-converted.pt" or weights =ROOT /"weights/yolov9-c-converted.pt"
data =ROOT /'data/coco.yaml'

```
```

conda info --envs
```
```shell
训练部分下次再写
```



 

