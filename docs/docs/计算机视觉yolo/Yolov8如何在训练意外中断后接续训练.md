```
官方文档：
https://docs.ultralytics.com/modes/train/#resuming-interrupted-trainings
```
```
#在ultralytics文件夹下创建一个py文件，修改一下pt路径，需要加转义r
from ultralytics import YOLO

# Load a model
model = YOLO(r'D:\dianchi-pyside6\ultralytics-main\ultralytics\runs\detect\train4\weights\best.pt')  # load a partially trained model

# Resume training
results = model.train(resume=True)
```
```
#运行
python <name>.py
```
