## BaiduAI_Car
第十九届智能车模型组
## 电机控制(motor.c)

- 电机减速比（Reduction Ratio）是指电机输出轴的转速与输入轴（通常是电机的轴）转速之比。它表示了电机的输出转速相对于输入转速的减少倍数。

通常情况下，减速比用于调节电机的输出转速和输出扭矩，以满足特定的应用需求。较高的减速比会减少输出转速，增加输出扭矩，适用于需要更大驱动力的场景，例如需要提高扭矩的情况下。而较低的减速比则会增加输出转速，降低输出扭矩，适用于需要更高转速的场景，比如需要更高速度的情况下。

- 轮子直径对于电机模型中的一些参数计算是很重要的。通常情况下，电机的速度与轮子的直径成反比关系。一般来说，可以使用以下公式计算电机模型中的轮子直径：

$$ \text{DiameterWheel} = \frac{{\text{EncoderLine} \times \text{ReductionRatio} \times \text{Time} \times \text{Speed}}}{{\text{EncoderValue} \times 4 \times \pi}} $$ 其中：

   - EncoderLine 是编码器线数，即光栅数乘以 16 和 4 的乘积；
   - ReductionRatio 是电机的减速比；
   - EncoderValue 是编码器的实时速度；
   - Speed 是电机的实际转速；
   - $ \pi $ 是圆周率。
   - $ \text{Time} $ 为控制周期

这个公式用来计算轮子的直径，其中编码器的线数、减速比和实时速度都会影响到轮子的实际直径。
## 串口通讯(uart.hpp)
通讯协议共有10bytes，第一个是帧头0x42，第二个是地址，用来区分不同功能，第三个是数据长度，用来接受完整的数据，最大是5个字节，最后一位是校验位，用来校验整个数据包是否完整。
接受协议大概如下：

1. 接受到帧头
2. 初始化帧的长度
3. 逐个接受每一个字节数据
4. 累加数据进行校验
5. 数据转换，并进行相关的功能实现

发送协议则将数据包保存与Uniton联合体之中，将其中的buff数组一个个发送出去，数据格式和接受一样。
## 透视变换(mapping.cpp)

- 类 `Mapping` 定义了图像透视变换的方法和属性。
- 构造函数 `Mapping` 初始化了类的一些成员变量，包括原始图像大小 (`m_origSize`)、目标图像大小 (`m_dstSize`)、原始域的四个关键点 (`m_origPoints`) 和矫正域的四个关键点 (`m_dstPoints`)。同时计算了透视变换矩阵 `m_H` 和其逆矩阵 `m_H_inv`，并调用 `createMaps` 方法创建了变换后的图像坐标映射。
- `homographyInv` 方法用于将原始域的图像或坐标进行逆透视变换，其中调用了 `remap` 函数来实现图像的重映射。
- `homography` 方法用于将原始域的坐标进行透视变换。
- `drawBorder` 方法用于在图像上绘制掩膜外框，以便在可视化中显示变换区域。
- `createMaps` 方法用于创建图像坐标映射，通过遍历目标图像的每个像素，利用透视变换矩阵将目标图像的坐标映射到原始图像上，并生成映射矩阵 `m_mapX` 和 `m_mapY`。
## 预处理(preprocess.cpp)
**preprocess**类
方法:

- 通过从 XML 文件中读取来初始化相机标定参数
- `binaryzation` 方法：将输入的彩色图像转换为灰度图像，然后使用 Otsu 的阈值法进行二值化处理。
- `correction` 方法：根据相机标定参数对输入的图像进行畸变矫正，如果标定参数可用的话。它首先检查是否启用了标定。如果启用了，它会初始化图像矫正所需的矩阵，然后进行重映射
## 识别元素(recognition)
### 赛道线识别(tracking.cpp)

- 这段代码定义了一个名为`Tracking`的类，用于识别和追踪图像中的赛道线。赛道线识别对于自动驾驶车辆和机器人导航系统等领域非常重要。这个类使用OpenCV库来处理图像，从而识别出赛道的左右边缘，以及处理其他相关的任务如岔路识别和车库标识识别等。以下是代码中各个部分的详细解释：
### 类成员变量

   - `vector<POINT> pointsEdgeLeft;`和`vector<POINT> pointsEdgeRight;`：分别存储赛道左边缘和右边缘的点集。
   - `vector<POINT> widthBlock;`：存储赛道宽度信息，每个元素表示一行中赛道的起始和终止点。
   - `vector<POINT> spurroad;`：存储岔路信息。
   - `double stdevLeft;`和`double stdevRight;`：分别存储左右边缘斜率的方差，用于评估赛道线的直线性。
   - `int validRowsLeft;`和`int validRowsRight;`：分别记录左右边缘有效的行数。
   - `POINT garageEnable;`：标志位，用于识别车库入口，x=1表示已识别，y存储识别行。
   - `uint16_t rowCutUp;`和`uint16_t rowCutBottom;`：图像顶部和底部切行的数量，用于裁剪图像以减少处理区域。
   - `Mat imagePath;`：存储待识别的图像。
   - `enum ImageType`：定义图像类型（二值化或RGB）。
### 主要方法

   - `void trackRecognition(bool isResearch, uint16_t rowStart);`：核心方法，执行赛道线识别逻辑。它支持两种模式：完全搜索模式和从指定行开始的重复搜索模式。
   - `void trackRecognition(Mat &imageBinary);`：重载的方法，接受二值化图像为参数并调用`trackRecognition`方法。
   - `void drawImage(Mat &trackImage);`：在给定图像上绘制赛道边缘和岔路信息。
   - `double stdevEdgeCal(vector<POINT> &v_edge, int img_height);`：计算边缘斜率的标准偏差。
   - `void slopeCal(vector<POINT> &edge, int index);`：计算边缘点斜率。
   - `void validRowsCal(void);`：计算左右边缘的有效行数。
   - `int getMiddleValue(vector<int> vec);`：使用冒泡排序法求取集合的中值。
### 识别流程简述

   1. **初始化和预处理**：初始化类变量，清理之前的结果，设置图像类型（二值化或RGB），并根据是否为重复搜索模式调整搜索起始行。
   2. **行遍历**：从图像底部向顶部遍历每一行，寻找赛道边缘。这一过程包括色块识别（赛道区域）和边缘点提取。
   3. **色块处理**：对于每一行，识别出所有色块的起始和终止列，处理车库标志识别，岔路信息提取，以及赛道宽度计算。
   4. **边缘点处理**：根据色块信息提取赛道的左右边缘点，并计算其斜率和方差，评估赛道线的直线性和稳定性。
   5. **结果绘制**：在指定图像上绘制识别的赛道边缘和其他信息（如岔路）。
### 十字路口(crossroad.cpp)
寻找四个角点，直行并进行相应的补偿
### 环岛识别(ring.cpp)
环岛识别比较复杂，可以分为很多阶段进行，但第一步是得先能够准确识别到环岛，环岛最基础的特征主要是一边与长直道相同，另一边斜率变化会比较大，根据这个特点会比较容易且准确的识别到环岛，然后分为入环，环内和出环进行相应的修正（原理有点没看懂）
## 2024/2/29
已经初步将硬件主板和驱动板设计完成完成
## 2024/3/5
config edgeboard ssh and git
## 2024/3/16
已完成主板和驱动板的焊接，均已通过主板测试和驱动测试，基本实现相应功能

> 来自: [JinHeMu/BaiduAI_Car: 第十九届智能车模型组](https://github.com/JinHeMu/BaiduAI_Car)

