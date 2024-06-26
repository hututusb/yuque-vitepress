```
/**
 ********************************************************************************************************
 *                                               示例代码
 *                                             EXAMPLE  CODE
 *
 *                      (c) Copyright 2024; SaiShu.Lcc.; Leo;
 *https://bjsstech.com 版权所属[SASU-北京赛曙科技有限公司]
 *
 *            The code is for internal use only, not for commercial
 *transactions(开源学习,请勿商用). The code ADAPTS the corresponding hardware
 *circuit board(代码适配百度Edgeboard-智能汽车赛事版), The specific details
 *consult the professional(欢迎联系我们,代码持续更正，敬请关注相关开源渠道).
 *********************************************************************************************************
 * @file icar.cpp
 * @author Leo
 * @brief 智能汽车-顶层框架（TOP）
 * @version 0.1
 * @date 2023-12-25
 * @copyright Copyright (c) 2024
 *
 */
#include "../include/common.hpp"     //公共类方法文件
#include "../include/detection.hpp"  //百度Paddle框架移动端部署
#include "../include/uart.hpp"       //串口通信驱动
#include "controlcenter.cpp"         //控制中心计算类
#include "detection/bridge.cpp"      //AI检测：坡道区
#include "detection/danger.cpp"      //AI检测：危险区
#include "detection/parking.cpp"     //AI检测：停车区
#include "detection/racing.cpp"      //AI检测：追逐区
#include "detection/rescue.cpp"      //AI检测：救援区
#include "preprocess.cpp"            //图像预处理类
#include "recognition/crossroad.cpp" //十字道路识别与路径规划类
#include "recognition/ring.cpp"      //环岛道路识别与路径规划类
#include "recognition/tracking.cpp"  //赛道识别基础类
#include <iostream>
#include <opencv2/highgui.hpp> //OpenCV终端部署
#include <opencv2/opencv.hpp>  //OpenCV终端部署
#include <signal.h>
#include <unistd.h>
#include <thread.h>
#include <mutex>
#include <condition_variable>

using namespace std;
using namespace cv;

Mat ai_image;
bool start_ai = false;
std::mutex mut;
std::thread ai_thread;
std::condition_variable cond_var;
std::shared_ptr<Detection> detection_ptr;


void thread_ai() {
    while (true) {
        unique_lock<mutex> lock(mut);
        cond_var.wait(lock, [] { return start_ai; });
        if (ai_image.empty()) {
            continue; // 取不到图，继续下一帧取图
        }
        Mat image_copy = ai_image.clone();
        start_ai = false;
        lock.unlock();
        detection_ptr->inference(image_copy);   //推理
    }
}


int num;
string Path = "/root/workspace/";
uint8 Line[60][80];
uint8 Threshold_detach = 200;                      // 分割阈值
ImageStatustypedef ImageStatus;                    // 图像状态
ImageDealDatatypedef ImageDeal[60]; // 某一行数据处理
          // 利用此变量处理某一列的表示方式为ImageDeal[行].LeftBorder/RightBorder
uint8 break_point1=0,break_point2=0;
X_Y Corner_Left_Down_XY;
X_Y Corner_Right_Down_XY;
X_Y Corner_Left_Up_XY;
X_Y Corner_Right_Up_XY;
X_Y Corner_Right_Mid_XY;
X_Y Corner_Left_Mid_XY;
X_Y you;
X_Y zuo;
Func_t func_left;  // 补线函数,Func_t定义的是float的k,b
Func_t func_right; // 补线函数
uint8 first_break_r=0,first_break_l=0;

int y_new[22],x_new[22];
int x_new_left[22],x_new_right[22];
int y_left[22],x_left[22];
int y_right[22],x_right[22];

uint Fill_Top=0;
static int ytemp = 0;
static int TFSite = 0;
static int FTSite = 0;
static uint8 camera[60][80];//
unsigned char small_width = 80, small_height = 60; // 图像处理大小
Mat dst(small_height, small_width, CV_8U); // 按照已定大小获取图像
static float DetR = 0, DetL = 0; //左侧偏差,右侧偏差
int Left_Deviation[60];         // 左偏离，一般是指行之间；不靠谱
int Right_Deviation[60];        // 右偏离
static uint8 *PicTemp;  
static int Ysite = 0, Xsite = 0;   // Y坐标，X坐标
float left_circle_value =0;  // 左边界曲率值
float right_circle_value =0; // 右边界曲率值
int Left_Circle_progress;
int Task_Left_progress=0;
int Task_Right_progress=0;
int Right_Circle_progress;
uint8 BLACK_blocks_r = 0;
uint8 BLACK_blocks_l=0;
int L_L_Line = 0;             // 左边丢边
int L_R_Line = 0;             // 右边丢边
int right_deviation = 0;
int left_deviation = 0;
int l_start = 0; // 算左边界曲率的条件1
int l_end = 0;   // 同上的条件2
int r_end = 0;   // 算右边界曲率的条件1
int r_start = 0; // 同上
int center_start=0;
int center_end =0;
int cross_out_flag=0;
static int IntervalLow = 0, IntervalHigh = 0; // 低间隔，高间隔
static uint8 ImageScanInterval = 5;           // 图像扫描间隔是5
static uint8 ImageScanInterval_Cross = 2;     // 十字的图像扫描间隔是2
int m=0;
//斑马线
 uint8 c=0;
 uint8 flag_zebra_line = 0;
uint8 zebra_times = 0;
uint8 zebra_top = 0;
uint8 all_zebra_times = 0;
uint8 black_blocks = 0;
clock_t startTime, endTime;
float Weighting[10] = {0.96, 0.92, 0.88, 0.83, 0.77,0.71, 0.65, 0.59, 0.53, 0.47};
//图像坐标系h:60~0 w:0~80
void read_param() // 读1.json参数
{
    string jsonPath = Path  + "1.json";
    std::ifstream config_is(jsonPath); // 读取地址
    if (!config_is.good()) {
    std::cout << "Error: Params file path:[" << jsonPath << "] not find .\n";
    exit(-1);
    }
    nlohmann::json js_value;
    config_is >> js_value; // json头文件声明，
    try {
    params1 = js_value.get<qianzhan>();
    } catch (const nlohmann::detail::exception &e) {
    std::cerr << "Json Params Parse failed :" << e.what() << '\n';
    exit(-1);
    }
    // printf("normal:%d\n", params1.qianzhan_normal);

    std::cout << "camera Path :" << params1.uart_num << std::endl;
}

double  spendtime=0,zhenlv=0;

int main(int argc, char const *argv[]) {
     Preprocess preprocess;    // 图像预处理类
     Display display(2);       // 初始化UI显示窗口
     VideoCapture capture;     // Opencv相机类
    //shared_ptr<Detection> detection = make_shared<Detection>("../res/model/yolov3_mobilenet_v1");      //看起来上面是输入模型地址的，从edgeboard当中输入
    //shared_ptr<Detection> detection_ptr = make_shared<Detection>("../res/model/yolov3_mobilenet_v1");
    //ai_thread = std::thread(thread_ai, detection);             // 在这里初始化 ai_thread 对象  
    detection_ptr = make_shared<Detection>("../res/model/yolov3_mobilenet_v1");    //看起来上面是输入模型地址的，从edgeboard当中输入
    //detection->score =0.3;  
    detection_ptr->score = 0.3;                                   //置信度别给太高！！！不然检测不到。
    thread ai_thread(thread_ai); // 启动子线程
    capture = VideoCapture("/dev/video0");                     // 初始化相机串口，上面那个
    if (!capture.isOpened()) {
        printf("can not open video device!!!\n");
        return 0;
    }

    capture.set(CAP_PROP_FRAME_WIDTH, COLSIMAGE);       // 设置图像分辨率
    capture.set(CAP_PROP_FRAME_HEIGHT, ROWSIMAGE);      // 设置图像分辨率
    double rate = capture.get(CAP_PROP_FPS);            // 获取图像分辨率
    double width = capture.get(CAP_PROP_FRAME_WIDTH);   // 获取图像宽度
    double height = capture.get(CAP_PROP_FRAME_HEIGHT); // 获取图像高度
    uart_init();
    std::string window_name = "usbname"; // 显示图像的窗口的名称
    namedWindow("dst", WINDOW_AUTOSIZE);
    Mat frame,frame_model;
    Left_Circle_progress=0;
    Right_Circle_progress=0;
    ImageStatus.Road_type= Straight;
    ImageStatus.Circle_progress_01=0;
    ImageStatus.Last_Road_type=0;
    ImageStatus.Barn_Flag=0;

  while (ture){
    capture >> frame; //取图
    if (frame.empty()){
      printf("frame is empty!!!\n");
      continue;
    }
    {
        lock_guard<mutex> lock(mut);   //加锁来保护共享资源
        ai_image = frame.clone();     //更新共享的图像
        start_ai = true;              //启动 AI 推理
    }
        cond_var.notify_one(); // 通知子线程

        // 开窗口
        imshow("dst", frame);
        if (waitKey(5) >= 0)
            break; // 按任意键退出循环
  }
    ai_thread.join(); // 等待子线程结束
    return 0;

}





int get_edgeboard_image(void)
{
   
    ImageStatus.OFFLine=20;
    int num;//发送给下位机的数
    num_init();
    drawlines();
    draw_lines_all();
    image_basic();
    Corner_Get();      
    element_test();
   
    printf("ImageStatus.Roadtype=%d",ImageStatus.Road_type);
    zebra(50,59,1);
//    if(black_blocks>=4)
//    {
//     printf("hello111\n");
//    }
   
    // printf("OFFlINE=%d\n",ImageStatus.OFFLine);
    // ImageStatus.Road_type=RightCirque;
    // Right_Cir_Progress_01();
    //  Circle_Left_Down(50, 10);
    // Circle_Right_Up(10,50); 
    // printf("ImageStatus.WhiteLine%d\n",ImageStatus.WhiteLine);
    //  printf("ImageStatus.left_WhiteLine_40%d\n",ImageStatus.left_WhiteLine_40);
    printf("Right_Circle_progress=%d\n",Right_Circle_progress);
    printf("Left__Circle_progress=%d\n",Left_Circle_progress);
    // printf("right_circle=%f\n",right_circle_value);
    // printf("left_circle_value=%f\n",left_circle_value);
    // printf("ImageStatus.right_WhiteLine_40 =%d\n",ImageStatus.right_WhiteLine_40);
    // printf("ImageStatus.left_WhiteLine_40=%d\n",ImageStatus.left_WhiteLine_40);
    // printf("ImageStatus.L_R_Line=%d\n",ImageStatus.L_R_Line);
    // printf("Right_Circle_progress=%d\n",Right_Circle_progress);
    // printf("r_start=%d, r_start - r_end=%d\n",r_start, r_start - r_end);
    // printf("r_end=%d\n",r_end);
    // printf("L_END=%d,l_start=%d\n",l_end,l_start);
    // printf("ImageStatus.WHITE%d\n",ImageStatus.WhiteLine);
    // printf("ImageStatus.L_L_Line%d\n",ImageStatus.L_L_Line);
    // printf("ImageStatus.right_WhiteLine_30=%d\n",ImageStatus.right_WhiteLine_30);
    // printf("ImageStatus.L_R_Line%d\n",ImageStatus.L_R_Line);
    // printf("ImageStatus.TowPoint =%d\n",ImageStatus.TowPoint);
    // printf("l_start%d\n",l_start);
    // printf("left_C%f\n",left_C);
// //    c=Straight_Test();
//     printf("c=%d\n",c);
//    printf("LL%f,RR%f\n",left_circle_value,right_circle_value);

    // Corner_Left_Up(10,50,1);
    // Corner_Left_Middle(20,55);
    // Cross_test();

   
    //Cirque_Judge_01();
    // printf("zuoxia=%d,%d\n",Corner_Left_Down_XY.X,Corner_Left_Down_XY.Y);

    // printf("zuoshang=%d,%d\n",Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y);
    // Straight_Thru_01(Corner_Left_Down_XY.X,Corner_Left_Down_XY.Y,Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y,0,
    //                     Corner_Left_Up_XY.Y-1,Corner_Left_Down_XY.Y+1);
    // Straight_Thru_01(Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y,Corner_Right_Up_XY.X,Corner_Right_Up_XY.Y,2,
    //                     Corner_Right_Up_XY.Y-1,Corner_Right_Down_XY.Y);
    RouteFilter(); // 70us
    GetDet();


    num = ImageStatus.Det_True - 39; // num作为中线偏差，
  
    if(black_blocks>6)
   {
    num=0;
   }
    printf("num middle=%d\n",num);
    if (num >= 0) {
    num += 100;
    num = num * 1000 + ImageStatus.Road_type * 10 + ImageStatus.Circle_progress; // 变五位
    } else if (num < 0) {
    num = -num;
    num = num + 200;
    num = num * 1000 + ImageStatus.Road_type * 10 + ImageStatus.Circle_progress;
    }
    return num;
}
void num_init(void)
{
    uint8 i,j;
    for(i=0;i<60;i++)
    {
        for(j=0;j<80;j++)
        {
            ImageDeal[Ysite].IsLeftFind = 'W'; //w 第87行
            ImageDeal[Ysite].IsRightFind = 'W'; //w 第87行
            ImageDeal[Ysite].LeftBorder = 0;  //左侧列0
            ImageDeal[Ysite].RightBorder = 79; //右底部80  图像为80×60
            ImageDeal[Ysite].LeftTemp = 0;
            ImageDeal[Ysite].RightTemp = 79;
            ImageDeal[Ysite].Black_Wide_L = 39; //从40行开始处理 从下往上依次为0到39
            ImageDeal[Ysite].Black_Wide_R = 39;
            ImageDeal[Ysite].BlackWide = 0;
                             //边界与标志位初始化
    }
}
            ImageStatus.left_WhiteLine_40=0;//40行以下左丢线数
            ImageStatus.left_WhiteLine_30=0;//30行以下左丢线数
            ImageStatus.left_WhiteLine_20=0; //20行以下左丢线数
            ImageStatus.left_WhiteLine_10=0; //10行以下右丢线数
            ImageStatus.right_WhiteLine_40=0; //40行以下右丢线数
            ImageStatus.right_WhiteLine_30=0; //30行以下右丢线数
            ImageStatus.right_WhiteLine_20=0;;//20行以下右丢线数
            ImageStatus.right_WhiteLine_10=0;//10行以下右丢线数
            ImageStatus.all_WhiteLine=0; //15行以下左右丢线数
            ImageStatus.Cornerleft_down=0; //左下标志位
            ImageStatus.Cornerleft_up=0; //左上标志位
            ImageStatus.Cornerright_down=0; //右下标志位
            ImageStatus.Cornerright_up=0;  //右上标志位
            ImageStatus.Cornerright_Mid=0; //右中标志位
            ImageStatus.Cornerleft_Mid=0; //左中标志位
            
            Corner_Right_Up_XY.Y=0;
            Corner_Right_Up_XY.X=0;
            Corner_Left_Up_XY.X=0;
            Corner_Left_Up_XY.Y=0;
            Corner_Left_Mid_XY.X=0;
            Corner_Left_Mid_XY.Y=0;
            Corner_Right_Mid_XY.X=0;
            Corner_Right_Mid_XY.Y=0;
            left_circle_value = 0;           //左圆环
            right_circle_value = 0;         //右圆环
            l_start = 0;//丢行           
            l_end = 0;
            r_end = 0;
            r_start = 0;
            center_start = 0;
            center_end = 0;
            ImageStatus.OFFLine = 20;   //对图像的最顶行加以限制  
            ImageStatus.WhiteLine = 0;  //丢线
            ImageStatus.TowPoint = params1.qianzhan_zongti; //初始比较点
            ImageStatus.Det_all_k = 0.7;  //待定，斜率
            ImageStatus.MiddleLine = 39;
            ImageStatus.Det_True = 0;//真实偏差
            ImageStatus.straight_acc = 0;  //直道加速标志位  
            ImageStatus.Circle_progress_01=0;
            first_break_l=0;
            first_break_r=0;
            counterR=0;
            counterL=0;
}
void drawlines()  // 记录了底部五行的数据 左右寻线找边界
{
    PicTemp = camera[59]; //黑色
    if (*(PicTemp + ImageSensorMid) == BLACK)                 //如果底边图像中点为黑，异常情况
    {
        for (Xsite = 0; Xsite < ImageSensorMid; Xsite++)    //找左右边线
        {
            if (*(PicTemp + ImageSensorMid - Xsite) != BLACK)     //一旦找到左或右赛道到中心距离，就break
                break;                                          //并且记录Xsite
            if (*(PicTemp + ImageSensorMid + Xsite) != BLACK)  //此时已经找到左右边界
                break;
        }

        if (*(PicTemp + ImageSensorMid - Xsite) != BLACK)       //赛道如果在左边的话
        {
            ImageStatus.BottomBorderRight = ImageSensorMid - Xsite + 1;   // 59行右边线有啦
            for (Xsite = ImageStatus.BottomBorderRight; Xsite > 0; Xsite--)  //开始找59行左边线
            {
                if (*(PicTemp + Xsite) == BLACK &&
                    *(PicTemp + Xsite - 1) == BLACK)                //连续两个黑点，滤波 0到255 由黑到白
                {
                ImageStatus.BottomBorderLeft = Xsite;                     //左边线找到
                break;
                } else if (Xsite == 1) {
                ImageStatus.BottomBorderLeft = 0;                         //搜索到最后了，看不到左边线，左边线认为是0
                break;
                }
            }
            } else if (*(PicTemp + ImageSensorMid + Xsite) != 0)  //赛道如果在右边的话
            {
                ImageStatus.BottomBorderLeft = ImageSensorMid + Xsite - 1;    // 59行左边线有啦
                for (Xsite = ImageStatus.BottomBorderLeft; Xsite < 79; Xsite++)  //开始找59行左边线
                {
                    if (  *(PicTemp + Xsite) == BLACK
                        &&*(PicTemp + Xsite + 1) == BLACK)              //连续两个黑点，滤波
                    {
                    ImageStatus.BottomBorderRight = Xsite;                    //右边线找到
                    break;
                    } else if (Xsite == 78) {
                    ImageStatus.BottomBorderRight = 79;                       //搜索到最后了，看不到右边线，左边线认为是79
                    break;
                    }
                }
            }
  }
  else{
    for(Xsite = ImageSensorMid; Xsite <= 79 ; Xsite++) //   
    {
        if(Xsite == 79)
        {
            ImageStatus.BottomBorderRight = 79;
            ImageDeal[59].IsRightFind = 'W';//没找到右边界，自动将右侧作为边线      
            break;
        }
        else if(*(PicTemp + Xsite) == BLACK && *(PicTemp + Xsite +1) == BLACK)   //找到右边界     ?    
        {
            ImageStatus.BottomBorderRight = Xsite; //      赋值
            ImageDeal[59].IsRightFind = 'T'; //找到右边界
            break;
        }
    }
    for(Xsite = ImageSensorMid;Xsite >= 0;Xsite--)   //         从中间向左找
    {
        if(Xsite == 0)
        {
            ImageStatus.BottomBorderRight = 0;
            ImageDeal[59].IsLeftFind = 'W';//   没找到自动补    
            break;
        }
        else if(*(PicTemp + Xsite) == BLACK && *(PicTemp + Xsite -1) == BLACK)   //?    找到了左边界
        {
            ImageStatus.BottomBorderLeft = Xsite; // 
            ImageDeal[59].IsLeftFind = 'T';
            break;
        }
    }
    ImageStatus.BottomCenter = (ImageStatus.BottomBorderRight + ImageStatus.BottomBorderLeft) / 2; //59   得到中线 底边中线
    ImageDeal[59].Center = ImageStatus.BottomCenter;    //在数组中记录第一行数据
    ImageDeal[59].LeftBorder = ImageStatus.BottomBorderLeft;
    ImageDeal[59].RightBorder = ImageStatus.BottomBorderRight;
    ImageDeal[59].Wide = ImageStatus.BottomBorderRight - ImageStatus.BottomBorderLeft; //线宽
    for(Ysite = 58; Ysite > 0; Ysite--)     // 由中间向两边确定底边五行    图像自下往上为59到0    
    {
        PicTemp = camera[Ysite];
        for(Xsite = ImageDeal[Ysite + 1].Center; Xsite <= 79; Xsite++) //  从59行的中心点依次向右找点  
        {
            if(Xsite == 79) //到最右边还没找到则默认图像最右边为边线
            {
                ImageDeal[Ysite].RightBorder = 79;
                ImageDeal[Ysite].IsRightFind = 'W'; //找到右边（假）
                break;
            }
            else if(*(PicTemp + Xsite) == BLACK && *(PicTemp + Xsite + 1) == BLACK) //找到右边
            {
                ImageDeal[Ysite].RightBorder = Xsite;
                ImageDeal[Ysite].IsRightFind = 'T'; //是真的找到右边界啦
                break;
            }
        }
        for(Xsite = ImageDeal[Ysite + 1].Center; Xsite >= 0;Xsite--) //  从59行的中心点依次向左找点
        {
            if(Xsite == 0)
            {
                ImageDeal[Ysite].LeftBorder = 0;
                ImageDeal[Ysite].IsLeftFind = 'W'; //找到左边（假）
                break;
            }
            else if(*(PicTemp + Xsite) == BLACK  && *(PicTemp + Xsite - 1) == BLACK)
            {
                ImageDeal[Ysite].LeftBorder = Xsite;
                ImageDeal[Ysite].IsLeftFind = 'T'; //是真的找到左边界啦
                break;
            }
        }
        //   记录下底部五行的线宽与中点      
        ImageDeal[Ysite].Center = (ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder)/2;  // 图像中线
        ImageDeal[Ysite].Wide = ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder;   // 图像宽度
    }
  }
}
void Massage_Get()
{
    ImageStatus.WhiteLine = 0;
    ImageStatus.L_L_Line = 0; //左边丢边数
    ImageStatus.L_R_Line = 0;//右边丢边数
      for(Ysite = 54; Ysite >= ImageStatus.OFFLine; Ysite--)   //对剩下的行进行处理，顶行由于不稳定不进行处理
    {
     if(ImageDeal[Ysite].IsLeftFind == 'W' && ImageDeal[Ysite].IsRightFind == 'W')
            ImageStatus.WhiteLine++;       //如果左右都是无边则丢边数加一
        else if(ImageDeal[Ysite].IsLeftFind == 'W' && ImageDeal[Ysite].IsRightFind != 'W')
            ImageStatus.L_L_Line++; //左侧丢边数
        else if(ImageDeal[Ysite].IsLeftFind != 'W' && ImageDeal[Ysite].IsRightFind == 'W')
            ImageStatus.L_R_Line++;//右侧丢边数

        LimitL(ImageDeal[Ysite].LeftBorder);  //限幅
        LimitH(ImageDeal[Ysite].LeftBorder);
        LimitL(ImageDeal[Ysite].RightBorder);
        LimitH(ImageDeal[Ysite].RightBorder);

        ImageDeal[Ysite].Wide = ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder;

        if(ImageDeal[Ysite].Wide <= 7)   //重新确定可视距离
        {
            ImageStatus.OFFLine = Ysite + 1; //继续向下看
            break;
        }
        else if(ImageDeal[Ysite].RightBorder <= 10 || ImageDeal[Ysite].LeftBorder >= 70)   //当图像宽度达到一定限制时终止巡边，丢边了
        {
            ImageStatus.OFFLine = Ysite + 1;
            break;
        }
    }
}
void draw_lines_all()      // 找余下所有（54-0）的边线         
{
    int first_L_L;            //首次左丢边行
    int first_L_R;            //首次右丢边行
    ImageStatus.WhiteLine = 0;
    ImageStatus.L_L_Line = 0; //左边丢边数
    ImageStatus.L_R_Line = 0;//右边丢边数
    for(Ysite = 54; Ysite >= ImageStatus.OFFLine; Ysite--)   //对剩下的行进行处理，顶行由于不稳定不进行处理
    {
        PicTemp = camera[Ysite];//从左侧开始处理 左下角
        JumpPointtypedef JumpPoint[2];  //用于记录跳变位置以及类型（0代表左线，1代表右线）

        IntervalLow = ImageDeal[Ysite + 1].RightBorder - ImageScanInterval;    //从上一行右边线的低限制位（右边界的左侧）开始扫点 扫描区间
        IntervalHigh = ImageDeal[Ysite + 1].RightBorder + ImageScanInterval;    //从上一行右边线高限制位（右边界的右边）开始扫点
        //上述为在边线两侧找跳变点
        LimitL(IntervalLow);    //确定左扫描边并加以限制 （左右偏移量最大为1）
        LimitH(IntervalHigh);   //确定右扫描边并加以限制
        GetJumpPointFromDet(PicTemp,'R',IntervalLow,IntervalHigh,&JumpPoint[1]);  //对右边线进行扫描，寻找跳变点，从低到高寻找跳变点

        IntervalLow = ImageDeal[Ysite + 1].LeftBorder - ImageScanInterval;    //从上一行的左边线的低限制位开始扫点
        IntervalHigh = ImageDeal[Ysite + 1].LeftBorder + ImageScanInterval;    //从上一行的左边线高限制位开始扫点

        LimitL(IntervalLow);    //确定左扫描边并加以限制，最大偏移量为1
        LimitH(IntervalHigh);   //确定右扫描边并加以限制
        GetJumpPointFromDet(PicTemp,'L',IntervalLow,IntervalHigh,&JumpPoint[0]);  //对左边线进行扫描
        
       if (JumpPoint[0].type =='W')                                                    //如果本行左边线不正常跳变，即这10个点都是白的
       {
           if (first_break_l == 0)
           {
               first_break_l = Ysite;
               ImageDeal[Ysite].LeftBorder = (ImageDeal[Ysite - 1].LeftBorder + 5) <= 79 ?
                       (ImageDeal[Ysite - 1].LeftBorder + 5) : 79 ;
           }
           else
               ImageDeal[Ysite].LeftBorder =ImageDeal[Ysite - 1].LeftBorder;           //本行左边线用上一行的数值
       } else                                                                          //左边线正常
       {
         ImageDeal[Ysite].LeftBorder = (uint8)JumpPoint[0].point;                             //记录下来啦
       }
       if (JumpPoint[1].type == 'W')                                                   //如果本行右边线不正常跳变
       {
           if (first_break_r == 0)
           {
              first_break_r = Ysite;
              ImageDeal[Ysite].RightBorder = (ImageDeal[Ysite - 1].RightBorder - 5) >= 1 ?
                      (ImageDeal[Ysite - 1].RightBorder - 5) : 1;
           }
           else
               ImageDeal[Ysite].RightBorder =ImageDeal[Ysite - 1].RightBorder;               //本行右边线用上一行的数值
       } else                                                                          //右边线正常
       {
         ImageDeal[Ysite].RightBorder = (uint8)JumpPoint[1].point;                            //记录下来啦
       }
        // if(Jump[0].type == 'W')        //若非正常跳变（即无边），左侧无边
        // {
        //     ImageDeal[Ysite].LeftBorder = ImageDeal[Ysite + 1].LeftBorder;   //此行暂时沿用上一行的边线界定，上一行有两种情况（有点，无点）
        // }
        // else{ 
        //     ImageDeal[Ysite].LeftBorder = Jump[0].point;    //对边点进行记录
        // }
        // if(Jump[1].type == 'W') //右侧无边
        // {
        //     ImageDeal[Ysite].RightBorder = ImageDeal[Ysite + 1].RightBorder;  //此行暂时沿用上一行的边线界定，上一行有两种情况（有点，无点）
        // }
        // else
        // {
        //     ImageDeal[Ysite].RightBorder =Jump[1].point;
        // }

        ImageDeal[Ysite].IsLeftFind = JumpPoint[0].type;    //记录本行是否找到边线
        ImageDeal[Ysite].IsRightFind = JumpPoint[1].type;

        //再次对大跳变的边缘进行确定
        if((ImageDeal[Ysite].IsLeftFind == 'H') || (ImageDeal[Ysite].IsRightFind == 'H')) //左或右有边但跳变点
        {
            if(ImageDeal[Ysite].IsLeftFind == 'H')
            {
                for(Xsite = (ImageDeal[Ysite].LeftBorder + 1); Xsite <=(ImageDeal[Ysite].RightBorder - 1); Xsite++)  //对于边线之间进行扫描 两个跳变点之间扫描
                {
                    if((*(PicTemp + Xsite) == BLACK) && (*(PicTemp + Xsite + 1) != BLACK))  //如果上一行左线右边有黑白跳变点则直接跳出并修改边值
                    {
                        ImageDeal[Ysite].LeftBorder = Xsite; 
                        ImageDeal[Ysite].IsLeftFind = 'T';
                        break;
                    }
                    else if(*(PicTemp + Xsite) != BLACK)
                    {
                        ImageDeal[Ysite].IsLeftFind = 'T';
                        break;
                    }
                    else if(Xsite == (ImageDeal[Ysite].RightBorder - 1))
                    {
                        ImageDeal[Ysite].IsLeftFind = 'T';
                        break;
                    }
                }
            }
            if(ImageDeal[Ysite].IsRightFind == 'H') //右侧有跳变点
            {
                for(Xsite = ImageDeal[Ysite].RightBorder - 1 ; Xsite >= (ImageDeal[Ysite].LeftBorder + 1);Xsite--)  //对于边线之间进行扫描 两个跳变点之间扫描
                {
                    if(*(PicTemp + Xsite) == BLACK && *(PicTemp + Xsite -1) != BLACK)  //如果上一行右线左边有黑白跳变点则直接跳出并修改边值
                    {
                        ImageDeal[Ysite].RightBorder = Xsite;
                        ImageDeal[Ysite].IsRightFind = 'T';
                        break;
                    }
                    else if(*(PicTemp + Xsite) != BLACK)
                    {
                        ImageDeal[Ysite].IsRightFind = 'T';
                        break;
                    }
                    else if(Xsite == (ImageDeal[Ysite].LeftBorder + 1))
                    {
                        ImageDeal[Ysite].RightBorder = Xsite;
                        ImageDeal[Ysite].IsRightFind = 'T';
                    }
                }
            }
            if((ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder) <=  7)     //图像宽度限定
            {
                ImageStatus.OFFLine = Ysite + 1;        //对限高进行处理，向下一行处理
                break;
            }
        }
        if(ImageDeal[Ysite].IsLeftFind == 'W' && ImageDeal[Ysite].IsRightFind == 'W')
            ImageStatus.WhiteLine++;       //如果左右都是无边则丢边数加一
        else if(ImageDeal[Ysite].IsLeftFind == 'W')
            ImageStatus.L_L_Line++; //左侧丢边数
        else if(ImageDeal[Ysite].IsRightFind == 'W')
            ImageStatus.L_R_Line++;//右侧丢边数

        LimitL(ImageDeal[Ysite].LeftBorder);  //限幅
        LimitH(ImageDeal[Ysite].LeftBorder);
        LimitL(ImageDeal[Ysite].RightBorder);
        LimitH(ImageDeal[Ysite].RightBorder);

        ImageDeal[Ysite].Wide = ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder;

        if(ImageDeal[Ysite].Wide <= 7)   //重新确定可视距离
        {
            ImageStatus.OFFLine = Ysite + 1; //继续向下看
            break;
        }
        else if(ImageDeal[Ysite].RightBorder <= 10 || ImageDeal[Ysite].LeftBorder >= 70)   //当图像宽度达到一定限制时终止巡边，丢边了
        {
            ImageStatus.OFFLine = Ysite + 1;
            break;
        }
    }
}
void GetJumpPointFromDet(uint8 *p, uint8 type, int L, int H,JumpPointtypedef *Q) // 检测获得跳跃点；自下面一行的点往上加一行继续找
{
    int i = 0;
    if (type == 'L') // 如果是左边线
    {
    for (i = H; i > L; i--) // i的值在高低间隔之间
    {
        if (*(p + i) == 0xff &&
            *(p + i - 1) != 0xff) // p是该行的值，如果该行连续两个坐标不都是白的
        {
        Q->point = i;  // 确定了i的值，即x坐标
        Q->type = 'T'; // 找到了边界线
        break;
        } else if (i ==
                    (L + 1)) // 如果都是白色的，则扫描到L点位的右边一格进行判断
        {
        if (*(p + (L + H) / 2) != 0) // 如果中心点不是黑色，是白色
        {
            Q->point = (L + H) / 2; // 坐标赋给中心点
            Q->type = 'W';          // 并且标记未找到突变点
            break;
        } else // 如果中心点是黑色
        {
            Q->point = H;  // 坐标赋给H点
            Q->type = 'H'; // 并给出标志位H
            break;
        }
        }
    }
    } else if (type == 'R') // 如果是右边
    {
    for (i = L; i <= H; i++) // 从左到右进行判断
    {
        if (*(p + i) == 0xff && *(p + i + 1) != 0xff) // 如果两点不都是白色
        {
        Q->point = i;  // 找到突变点了
        Q->type = 'T'; // 给出找到的标志位
        break;
        } else if (i == (H - 1)) // 在最右边左边一格都没找到
        {
        if (*(p + (L + H) / 2) != 0) // 且中心还是白色的
        {
            Q->point = (L + H) / 2; // 把中心点坐标赋给
            Q->type = 'W';          // 并且给出未找到标志位
            break;
        } else {
            Q->point = L;  // 如果中心点是黑色，则把左边坐标赋予
            Q->type = 'H'; // 并且给H标志位
            break;
        }
        }
    }
    }
}
void image_basic(void)
{
    int i = 0;

    for (i = 58; i > ImageStatus.OFFLine; i--)  //行间偏差
    {
        Left_Deviation[i] =  (ImageDeal[i].LeftBorder - ImageDeal[i + 1].LeftBorder);//每一行的边界差值 横坐标差值
        Right_Deviation[i] = -(ImageDeal[i].RightBorder - ImageDeal[i + 1].RightBorder); //同理
        if (i >= 20 && ImageDeal[i].IsLeftFind == 'W')   //四十行以下左边丢失数 从上往下数值增大
        {
            ImageStatus.left_WhiteLine_40++;
        }
        if (i >= 20 && ImageDeal[i].IsRightFind == 'W')     //四十行以下右边丢失数
        {
            ImageStatus.right_WhiteLine_40++;
        }
        if (i >= 30 && ImageDeal[i].IsLeftFind == 'W')   //三十行以下左边丢失数
        {
            ImageStatus.left_WhiteLine_30++;
        }
        if (i >= 30 && ImageDeal[i].IsRightFind == 'W')   //三十行以下右边丢失数
        {
            ImageStatus.right_WhiteLine_30++;
        }
        if (i >= 40 && ImageDeal[i].IsLeftFind == 'W')   //二十行以下右边丢失数
        {
            ImageStatus.left_WhiteLine_20++;
        }
        if (i >= 40 && ImageDeal[i].IsRightFind == 'W')   //二十行以下右边丢失数
        {
            ImageStatus.right_WhiteLine_20++;
        }
        if (i >= 45 && ImageDeal[i].IsRightFind == 'W' && ImageDeal[i].IsLeftFind == 'W')   //十五行以下左右边丢失数
        {
            ImageStatus.all_WhiteLine++;
        }
    }
    
    circle_calculate(); //循环计算
}

void circle_calculate(void) //循环计算
{
    for(Ysite = 59;Ysite > ImageStatus.OFFLine;Ysite--)//从下往上找，此处为左边界
    {
       if(l_start == 0 && ImageDeal[Ysite].IsLeftFind == 'T') //确定起始行
       {
           l_start = Ysite;
       }
       else if(l_start != 0&& (ImageDeal[Ysite].IsLeftFind == 'W' || Ysite == ImageStatus.OFFLine + 1)) //确定结束行
       {
           l_end = Ysite;
           break;
       }
    }

   for(Ysite = 59;Ysite > ImageStatus.OFFLine;Ysite--)//从下往上找，此处为右边界
   {
       if(ImageDeal[Ysite].IsRightFind == 'T' && r_start == 0) //确定起始行
       {
           r_start = Ysite;
       }
       else if(r_start != 0 && (ImageDeal[Ysite].IsRightFind == 'W' || Ysite == ImageStatus.OFFLine +1)) //确定结束行
       {
           r_end = Ysite;
           break;
       }
   }
   left_circle_value = cur_circle(0); //计算左边界弯曲程度
   right_circle_value = cur_circle(1);//计算右边界弯曲程度
}
void Filter_Find_Seed_01(uint8 start_hang, uint8 end_hang)//左右扫线
{
    uint8 cursor = 0;    //指向栈顶的游标
    uint8 Seed_temp = 0;
    uint8 first_Seed = 0;
    uint8 i= 0,j = 0;

    // -------------------------扫描左边界--------------------------//
    for (j = start_hang; j < end_hang; j++)
    {
        first_Seed = 0;
        for (i = 39; i > 1; i--)  //从中间那一列向左
        {
            if (i < 10 && first_Seed != 0 && BLACK_blocks_r <= 2)
            {
                ImageDeal[j].IsLeftFind = 'T'; //找到左边界
                ImageDeal[j].LeftBorder = first_Seed; //左边界的横坐标为first_Seed
                break;
            }
            //正常黑白跳变扫描赛道
            if (camera[j][i] == 0xff && camera[j][i + 3] == 0xff && camera[j][i + 1] == 0xff
               && camera[j][i - 1] == 0 && camera[j][i - 3 > 0 ? i - 3 : i - 1] == 0)//确定以小范围确定左侧边线
            {
                Seed_temp = (i - 1); //y坐标
                if (first_Seed == 0)
                    first_Seed = (i - 1);
                if (Seed_temp >= 10)  //斑马线一般在图像靠中间
                {
                    while (camera[j][i - 1] == 0 && i > 10)  //判断处理刚找到的边界右边是否还存在白色区域
                    {
                        i--;
                        if (cursor >= 15 || i == 10)    //如果找了一定行数还是黑色边界的话，设置种子
                        {
                            ImageDeal[j].IsLeftFind  = 'T';
                            ImageDeal[j].LeftBorder = Seed_temp;
                            break;//当黑色元素超过栈长度的操作   break;
                        }
                        else
                            cursor++;
                    }

                    if (cursor >= 4 && cursor <= 12) //黑色区域像素点个数 判断斑马线
                    {
                        BLACK_blocks_r++; //黑色区域块
                        cursor = 0;
                    }
                    else
                        cursor = 0;

                    if (ImageDeal[j].IsLeftFind  == 'T')
                        break;
                }
                else
                {
                    ImageDeal[j].IsLeftFind  = 'T';
                    ImageDeal[j].LeftBorder = Seed_temp;
                    break;//当黑色元素超过栈长度的操作   break;
                }
            }

        }
        if (i == 1 && ImageDeal[j].IsLeftFind  == 'W') //x为1时为边界
        {
            ImageDeal[j].LeftBorder = 2;  //确认边界点
        }
    }

    //---------------寻找种子--后扫描右边界(这里是右边界)---------------//
    for (j = start_hang; j < end_hang; j++)
    {
        first_Seed = 0;
        for (i = 39; i < 79; i++)
        {
            if (i > 70 && first_Seed != 0 && BLACK_blocks_l <= 2)
            {
                ImageDeal[j].IsRightFind = 'T';
                ImageDeal[j].RightBorder = first_Seed;
                break;
            }
            if (camera[j][i - 5] == 0xff && camera[j][i - 3] == 0xff && camera[j][ i] == 0xff //判断条件
               && camera[j][ i + 1] == 0 && camera[j][ i + 5 <= 79 ? i + 5 : i + 1] == 0)
            {
                Seed_temp = (i + 1);
                if (first_Seed == 0)
                    first_Seed = (i - 1);
              //  Console.WriteLine("左初步找到了。。。");
                if (Seed_temp <= 70)
                {
                    while (camera[j][i + 3] == 0 && i < 70)
                    {
                        i++; //以一定初始间隔又移，找点也就是斑马线，大约找15结果为18格
                        if (cursor >= 15 || i == 70)
                        {
                           // Console.WriteLine("进来了" + i);
                            ImageDeal[j].IsRightFind = 'T';
                            ImageDeal[j].RightBorder = Seed_temp; //找到横坐标
                            break;//当黑色元素超过栈长度的操作   break;
                        }
                        else
                            cursor++;
                    }
                    if (cursor >= 1 && cursor <= 8) //黑色区域像素点个数
                    {
                        BLACK_blocks_l++; //黑色区域块  斑马线找到喽
                       // Console.WriteLine("左区域块" + BLACK_blocks_l);
                        cursor = 0;
                    }
                    else
                        cursor = 0;

                    if (ImageDeal[j].IsRightFind == 'T')
                        break;
                }
                else
                {
                    ImageDeal[j].IsRightFind = 'T';
                    ImageDeal[j].RightBorder = Seed_temp;
                    break;//当黑色元素超过栈长度的操作   break;
                }
            }

        }
        if (i==79 && ImageDeal[j].IsRightFind == 'W') //没找到边界
        {
            ImageDeal[j].RightBorder = 78;
        }
    }
}
void GetDet (void) //获取偏差
{
    float DetTemp = 0;
    uint8 TowPoint = 0;
    float UnitAll = 0;
    uint8 Ysite;
    for (Ysite = small_height-1; Ysite > ImageStatus.OFFLine; Ysite--)
    {
        ImageDeal[Ysite].Wide = ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder;
        ImageDeal[Ysite].Center = (ImageDeal[Ysite].RightBorder + ImageDeal[Ysite].LeftBorder) / 2;
    }
    //变前瞻
    TowPoint = ImageStatus.TowPoint;
    if(TowPoint>=53)
    TowPoint=53;
            if ((TowPoint - 5) >= ImageStatus.OFFLine)
            {
                for (int Ysite = (TowPoint - 5); Ysite < TowPoint; Ysite++) //前瞻上下10行的范围
                {
                    DetTemp = DetTemp + Weighting[TowPoint - Ysite - 1] * (ImageDeal[Ysite].Center);
                    UnitAll = UnitAll + Weighting[TowPoint - Ysite - 1];
                }
                for (Ysite = (TowPoint + 5); Ysite > TowPoint; Ysite--)
                {
                    DetTemp += Weighting[-TowPoint + Ysite - 1] * (ImageDeal[Ysite].Center);//偏差值为十行权重累加
                    UnitAll += Weighting[-TowPoint + Ysite - 1];//每一行权重的累加，加权平均数
                }
                DetTemp = (ImageDeal[TowPoint].Center + DetTemp) / (UnitAll + 1);

            }
            else if (TowPoint > ImageStatus.OFFLine)
            {
                for (Ysite = ImageStatus.OFFLine; Ysite < TowPoint; Ysite++)
                {
                    DetTemp += Weighting[TowPoint - Ysite - 1] * (ImageDeal[Ysite].Center);
                    UnitAll += Weighting[TowPoint - Ysite - 1];
                }
                for (Ysite = (TowPoint + TowPoint - ImageStatus.OFFLine); Ysite > TowPoint; Ysite--)
                {
                    DetTemp += Weighting[-TowPoint + Ysite - 1] * (ImageDeal[Ysite].Center);
                    UnitAll += Weighting[-TowPoint + Ysite - 1];
                }
                DetTemp = (ImageDeal[Ysite].Center + DetTemp) / (UnitAll + 1);
                // DetTemp = 2 * 100 * DetTemp / (TowPointSite * TowPointSite);
            }
            else if (ImageStatus.OFFLine < 49)
            {
                for (Ysite = (ImageStatus.OFFLine + 3); Ysite > ImageStatus.OFFLine; Ysite--)
                {
                    DetTemp += Weighting[-TowPoint + Ysite - 1] * (ImageDeal[Ysite].Center);
                    UnitAll += Weighting[-TowPoint + Ysite - 1];
                }
                DetTemp = (ImageDeal[ImageStatus.OFFLine].Center + DetTemp) / (UnitAll + 1);

            }

            else
                DetTemp = ImageStatus.Det;                   //如果是出现OFFLine>50情况，保持上一次的误差
    ImageStatus.Det = DetTemp;
    ImageStatus.Det_True = ImageStatus.Det;
}
void DrawExtensionLine(void)
 {
    float k_l = 0, k_r = 0, k_cen = 0;
    if (ImageStatus.Road_type != Ramp &&
        ImageStatus.Road_type != LeftCirque) {
    if (ImageStatus.WhiteLine >= ImageStatus.TowPoint_True - 15)
        TFSite = 55;
    if (ImageStatus.ExtenLFlag != 'F')
        for (Ysite = 54; Ysite >= (ImageStatus.OFFLine + 4);
            Ysite--) // ?    п ??  ?                    β
        {
        PicTemp = camera[Ysite];                // 浱?
        if (ImageDeal[Ysite].IsLeftFind == 'W') //
        {

            if (ImageDeal[Ysite + 1].LeftBorder >= 70) //     ?    ? ?
            {
            ImageStatus.OFFLine = Ysite + 1;
            break; // ?
            }
            while (Ysite >= (ImageStatus.OFFLine + 4)) //  ?  δ?
            {
            Ysite--; //        ?
            if (ImageDeal[Ysite].IsLeftFind == 'T' &&
                ImageDeal[Ysite - 1].IsLeftFind == 'T' &&
                ImageDeal[Ysite - 2].IsLeftFind == 'T' &&
                ImageDeal[Ysite - 2].LeftBorder > 0 &&
                ImageDeal[Ysite - 2].LeftBorder < 70) //    ????  ?          е ?
            {
                FTSite = Ysite - 2;
                break;
            }
            }

            DetL = ((float)(ImageDeal[FTSite].LeftBorder -
                            ImageDeal[TFSite].LeftBorder)) /
                    ((float)(FTSite - TFSite)); //      ?  б
            if (FTSite > ImageStatus.OFFLine) {
            for (ytemp = TFSite; ytemp >= FTSite; ytemp--) {
                ImageDeal[ytemp].LeftBorder =
                    (int)(DetL * ((float)(ytemp - TFSite))) +
                    ImageDeal[TFSite].LeftBorder; //     ? ??  ?   б
            }
            }
            // }
        } else
            TFSite = Ysite + 2;
        }
    if (ImageStatus.WhiteLine >= ImageStatus.TowPoint_True - 15)
        TFSite = 55;
    if (ImageStatus.ExtenRFlag != 'F')
        for (Ysite = 54; Ysite >= (ImageStatus.OFFLine + 4); Ysite--) {
        PicTemp = camera[Ysite];
        if (ImageDeal[Ysite].IsRightFind == 'W') {
            if (ImageDeal[Ysite + 1].RightBorder <= 10) {
            ImageStatus.OFFLine = Ysite + 1;
            break;
            }
            while (Ysite >= (ImageStatus.OFFLine + 4)) {
            Ysite--;
            if (ImageDeal[Ysite].IsRightFind == 'T' &&
                ImageDeal[Ysite - 1].IsRightFind == 'T' &&
                ImageDeal[Ysite - 2].IsRightFind == 'T' &&
                ImageDeal[Ysite - 2].RightBorder < 79 &&
                ImageDeal[Ysite - 2].RightBorder > 10) {
                FTSite = Ysite - 2;
                break;
            }
            }

            DetR = ((float)(ImageDeal[FTSite].RightBorder -
                            ImageDeal[TFSite].RightBorder)) /
                    ((float)(FTSite - TFSite));
            if (FTSite > ImageStatus.OFFLine)
            for (ytemp = TFSite; ytemp >= FTSite; ytemp--) {
                ImageDeal[ytemp].RightBorder =
                    (int)(DetR * ((float)(ytemp - TFSite))) +
                    ImageDeal[TFSite].RightBorder;
            }
            // }
        } else
            TFSite = Ysite + 2;
        }
    }
    for (Ysite = 59; Ysite >= ImageStatus.OFFLine; Ysite--) {
    ImageDeal[Ysite].Center =
        (ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder) / 2;
    ImageDeal[Ysite].Wide =
        ImageDeal[Ysite].RightBorder - ImageDeal[Ysite].LeftBorder;
    }
}
void RouteFilter(void) {
    for (Ysite = 58; Ysite >= (ImageStatus.OFFLine + 5); Ysite--) {
        if (ImageDeal[Ysite].IsLeftFind == 'W' &&
            ImageDeal[Ysite].IsRightFind == 'W' && Ysite <= 45 &&
            ImageDeal[Ysite - 1].IsLeftFind == 'W' &&
            ImageDeal[Ysite - 1].IsRightFind == 'W') {
            ytemp = Ysite;
            while (ytemp >= (ImageStatus.OFFLine + 5)) {
                ytemp--;
                if (ImageDeal[ytemp].IsLeftFind == 'T' &&
                    ImageDeal[ytemp].IsRightFind == 'T') {
                    if (ytemp - 1 - Ysite - 2 == 0)
                    DetR = (float)(ImageDeal[ytemp - 1].Center -
                                    ImageDeal[Ysite + 2].Center);
                    else
                    DetR = (float)(ImageDeal[ytemp - 1].Center -
                                    ImageDeal[Ysite + 2].Center) /
                            (float)(ytemp - 1 - Ysite - 2);
                    int CenterTemp = ImageDeal[Ysite + 2].Center;
                    int LineTemp = Ysite + 2;
                    while (Ysite >= ytemp) {
                    ImageDeal[Ysite].Center =
                        (int)(CenterTemp + DetR * (float)(Ysite - LineTemp)); //  б ?
                    Ysite--;
                    }
                    break;
                }
            }
        }
        ImageDeal[Ysite].Center =
            (ImageDeal[Ysite - 1].Center + 2 * ImageDeal[Ysite].Center) / 3;
    }
}
uint8 Straight_Test(void)
{

      if (left_circle_value >= 0.98 && right_circle_value >= 0.98 
    //   &&l_start >= 58 && r_start >= 58 && l_end <= 7 && r_end <= 7
    )
         {
        // ImageStatus.Road_type =Straight; // 直线判断条件，得是算出的偏差小于图像状态偏差以及限制行在第10行以上
        return 1;
             //   printf("straight\n");
        }
        else 
        return 0;
}
static int Three_Cross_flag = 0, Three_Cross_flag_process = 0,
            Three_Cross_flag_leijia = 0;
static uint8 cross_left_flag = 0, cross_right_flag = 0, cross_4_flag = 0,
                cross_5_flag = 0; // cross_3_flag是指那个右边丢线，左边的拐点

void Cross_test(void)
{
    printf("ImageStatus.Road_type=%d\n",ImageStatus.Road_type);
    int i;
    float left_circle_value_cross;
    float right_circle_value_cross;
    printf("%d,%d,%d,%d\n",ImageStatus.all_WhiteLine,ImageStatus.WhiteLine,ImageStatus.L_L_Line,ImageStatus.L_R_Line);
    if(  ImageStatus.Road_type==Cross)
    {
    if(ImageStatus.Cornerleft_up==1
    &&ImageStatus.Cornerright_up==1)
    {
        Straight_Thru_01(Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y,0,59,0,
                        Corner_Left_Up_XY.Y-1,59);
            Straight_Thru_01(Corner_Right_Up_XY.X,Corner_Right_Up_XY.Y,79,59,1,
                        Corner_Right_Up_XY.Y-1,59);   
    
    if(ImageStatus.Cornerleft_down==1
    &&ImageStatus.Cornerright_down==1
    )
    {
        Straight_Thru_01(Corner_Left_Down_XY.X,Corner_Left_Down_XY.Y,Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y,0,
                        Corner_Left_Up_XY.Y-1,Corner_Left_Down_XY.Y+1);
        Straight_Thru_01(Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y,Corner_Right_Up_XY.X,Corner_Right_Up_XY.Y,1,
                        Corner_Right_Up_XY.Y-1,Corner_Right_Down_XY.Y);
                        
    }
    }
   if(ImageStatus.Cornerleft_down==1
    &&ImageStatus.Cornerright_down==1
    &&(ImageStatus.Cornerleft_up==0
    ||ImageStatus.Cornerright_up==0))
    {
        Straight_Thru_01(zuo.X,zuo.Y,Corner_Left_Down_XY.X,Corner_Left_Down_XY.Y,0,
                       Corner_Left_Down_XY.Y-20 ,Corner_Left_Down_XY.Y);
        Straight_Thru_01(Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y,you.X,you.Y,1,
                        Corner_Right_Down_XY.Y-20,Corner_Right_Down_XY.Y);
                        ImageStatus.Road_type=Cross;
    }
    }
}
void cross_out(void)
{
    if((ImageStatus.Cornerleft_up==0&&ImageStatus.Cornerleft_down==0)&&(ImageStatus.Cornerright_down==0&&ImageStatus.Cornerright_up==0))
    {
        ImageStatus.Road_type=Straight;
    }
}
float cur_circle(uint8 m) // 算曲率的（这里是越大越直）
{
    int i;
    int a = l_start, b = l_end + (int)((l_start - l_end) / 3),
        c = l_end; // 所选三行的初始值
    if (a - b <= 5)
    b = a - 6;
    float num = 0;
    float cur = 0;
    if (a > 20 && (a - c) > 10 && c < 45) 
    {
    a -= 5;
    if (m == 0) 
    {
        for (i = 0; i <= 5; i++) 
        {
        if (ImageDeal[a + i].IsLeftFind == 'T' &&
            ImageDeal[c + i].IsLeftFind == 'T' &&
            ImageDeal[b + i].IsLeftFind == 'T') 
            {
            num += 1;
            cur += angle_caculate(ImageDeal[b + i].LeftBorder, b + i,
                                ImageDeal[a + i].LeftBorder, a + i,
                                ImageDeal[c + i].LeftBorder, c + i);
        }
        }
    }
    }
    a = r_start;
    b = r_end + (int)((r_start - r_end) / 3);
    c = r_end; // 所选三行的初始值

    if (a > 20 && (a - c) > 10 && c < 45)
     {
    a -= 5;
    if (m == 1) 
    {
        for (i = 0; i <= 5; i++) {
        if (ImageDeal[a + i].IsRightFind == 'T' &&
            ImageDeal[c + i].IsRightFind == 'T' &&
            ImageDeal[b + i].IsRightFind == 'T') {
            num += 1;
            cur += angle_caculate(ImageDeal[b + i].RightBorder, b + i,
                                ImageDeal[a + i].RightBorder, a + i,
                                ImageDeal[c + i].RightBorder, c + i);
        }
        }
    }
    }
    if (num == 0)
    return 0;
    else
    return cur / num;
}
float angle_caculate(int x1, int y1, int x2, int y2, int x3, int y3) {
    float cosA2;

    y1 = 2.115 * y1;
    y2 = 2.115 * y2;
    y3 = 2.115 * y3;
    cosA2 = (float)((x2 - x1) * (x3 - x1) + (y2 - y1) * (y3 - y1)) *
            ((x2 - x1) * (x3 - x1) + (y2 - y1) * (y3 - y1)) /
            (((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1)) *
            ((x3 - x1) * (x3 - x1) + (y3 - y1) * (y3 - y1)));

    return cosA2;
}
float cur_circle_real(uint8 m, int a1,int c1) // 算曲率的（这里是越大越直）,a1是start,c1是end
{
    int i;
    int a = a1, b = c1 + (int)((a1 - c1) / 3), c = c1; // 所选三行的初始值
    if (a - b <= 5)
    b = a - 6;
    float num = 0;
    float cur = 0;
    if (a > 20 && (a - c) > 10 && c < 45) {
        a -= 5;
        if (m == 0) {
            for (i = 0; i <= 5; i++) {
                if (ImageDeal[a + i].IsLeftFind == 'T' &&
                    ImageDeal[c + i].IsLeftFind == 'T' &&
                    ImageDeal[b + i].IsLeftFind == 'T') {
                    num += 1;
                    cur += angle_caculate(ImageDeal[b + i].LeftBorder, b + i,
                                        ImageDeal[a + i].LeftBorder, a + i,
                                        ImageDeal[c + i].LeftBorder, c + i);
                }
            }
        }
    }
    a = r_start;
    b = r_end + (int)((r_start - r_end) / 3);
    c = r_end; // 所选三行的初始值

    if (a > 20 && (a - c) > 10 && c < 45) {
    a -= 5;
    if (m == 1) {
        for (i = 0; i <= 5; i++) {
        if (ImageDeal[a + i].IsRightFind == 'T' &&
            ImageDeal[c + i].IsRightFind == 'T' &&
            ImageDeal[b + i].IsRightFind == 'T') {
            num += 1;
            cur += angle_caculate(ImageDeal[b + i].RightBorder, b + i,
                                ImageDeal[a + i].RightBorder, a + i,
                                ImageDeal[c + i].RightBorder, c + i);
        }
        }
    }
    }
    if (num == 0)
    return 0;
    else
    return cur / num;
}
void element_test(void)//元素判断
{
    //  cross_Judge();
    //  Cross_test();
    // Cirque_Judge_Left();
    // Left_Cir_Progress_01();
    // Cirque_Judge_Right();
    // Right_Cir_Progress_01();
    Stop_test_2();
    // Stop_test();


    //  if(left_circle_value>=0.97&&right_circle_value>=0.97)
    //     {
    //         ImageStatus.Road_type=Straight;
    //     }

     if(ImageStatus.Road_type!=Cross
      &&ImageStatus.Road_type != LeftCirque_01
      &&ImageStatus.Road_type != RightCirque
      &&ImageStatus.Road_type!=Stop)
        {
            // if( right_circle_value>=0.8&&left_circle_value>=0.8)
        ImageStatus.Road_type=Straight;
        // printf("Image,TT%d\n",ImageStatus.Road_type);
        }
        if(cross_out_flag==1)
        {
            ImageStatus.Road_type=Straight;
            cross_out_flag=0;
            // printf("Image,HH%d\n",ImageStatus.Road_type);
        }
}
void cross_Judge(void)
{
    if(ImageStatus.WhiteLine>=18 
    &&ImageStatus.Road_type==Straight
    ) //中点线不是零
    {
   ImageStatus.Road_type=Cross;
   cross_out_flag=1;
    }
}
void Straight_Thru_01(int x1, int y1, int x2, int y2, uint8 direction, int low, int hight)//如果同列直接拉线，如果不是，那就算出k与b，再用公式计算边界点的坐标
{
    double k = 0;
    double b = 0;
    uint8 Str_i = 0;//过程变量
    int guocheng1=0;

    if(x1 > 79)x1 = 79;
    if(x2 > 79)x2 = 79;
    if (x1 == x2) //如果两个点在一列上，直接连线，直接赋予边界行坐标
    {
        if (y1 <= y2)
        {
            for (Str_i = (uint8)y1; Str_i <= y2; Str_i++)
            {
                if (direction == 0)
                    ImageDeal[Str_i].LeftBorder = (uint8)x1;
                else if (direction == 1)
                    ImageDeal[Str_i].RightBorder = (uint8)x1;
                 else if (direction == 2)
                    ImageDeal[Str_i].Center = (uint8)x1;    
            }
            return;
        }
        else
        {
            for (Str_i = (uint8)y2; Str_i <= y1; Str_i++)
            {
                if (direction == 0)
                    ImageDeal[Str_i].LeftBorder = (uint8)x1;
                else if (direction == 1)
                    ImageDeal[Str_i].RightBorder = (uint8)x1;
                     else if (direction == 2)
                    ImageDeal[Str_i].Center = (uint8)x1;
            }
            return;
        }
    }//算k与b
    else if (x1 > x2 && y1 > y2)
    {
        k = (double)(y1 - y2) / (x1 - x2);
    }
    else if (x1 > x2 && y1 < y2)
    {
        k = -(double)(y2 - y1) / (x1 - x2);
    }
    else if (x1 < x2 && y1 > y2)
    {
        k = -(double)(y1 - y2) / (x2 - x1);
    }
    else if (x1 < x2 && y1 < y2)
    {
        k = (double)(y2 - y1) / (x2 - x1);
    }

    if (k != 0)
    {
        b = (double)(y1 - x1 * k);
    }

    //-----------------补线----------------------
    if (direction == 0) //左线，根据k,b计算出坐标，赋予边界点坐标
    {
        func_left.k = k;
        func_left.b = b;
        for (Str_i = (uint8)low; Str_i <= hight; Str_i++)
        {
            guocheng1=(uint8)Pro_value_byte((int)((Str_i - b) / k));
            if(guocheng1<1)
            guocheng1=1;
            if(guocheng1>79)
            guocheng1=79;
            ImageDeal[Str_i].LeftBorder =guocheng1;
        }
    }
    else if (direction == 1)//右线
    {
        func_right.k = k;
        func_right.b = b;
        for (Str_i = (uint8)low; Str_i <= hight; Str_i++)
        {
            guocheng1=(uint8)Pro_value_byte((int)((Str_i - b) / k));
              if(guocheng1<1)
            guocheng1=1;
            if(guocheng1>79)
            guocheng1=79;
            ImageDeal[Str_i].RightBorder = guocheng1;
        }
    }
     else if (direction == 2)//中线
    {
        func_right.k = k;
        func_right.b = b;
        for (Str_i = (uint8)low; Str_i <= hight; Str_i++)
        {
            if(ImageDeal[Str_i].Center>79)
            ImageDeal[Str_i].Center=79;
            if(ImageDeal[Str_i].Center<1)
            ImageDeal[Str_i].Center=1;
            ImageDeal[Str_i].Center = (uint8)Pro_value_byte((int)((Str_i - b) / k));
        }
    }
}

uint8 Pro_value_byte(int value) // 对边界坐标进行限制，不能超出0到79
{
    if (value >= 79)
    value = 79;
    else if (value <= 0)
    value = 0;
    return (uint8)value;
}

/*
 * dir == 1 --- 左
 * dir == 2 --- 右
 * dir == 3 --- 回环----左//没用到
 * dir == 4 --- 回环----右//没用到
 */
uint8 Find_WHITE_Hang(uint8 dir ,uint8 start ,uint8 end)  //此刻为从上往下找
{
    uint8 WHITE_cnt = 0;

    if(dir == 1)
        for(Ysite = start ;Ysite < end ;Ysite++)
        {
            if(ImageDeal[Ysite].IsLeftFind == 0)
            {
                WHITE_cnt++;
            }
        }
    else if(dir == 2)
        for(Ysite = start ;Ysite < end ;Ysite++)
        {
            if(ImageDeal[Ysite].IsRightFind == 0)
            {
                WHITE_cnt++;
            }
        }
    else if(dir == 3)
    {
        for(Ysite = start ;Ysite < end ;Ysite++)
        {
            if(camera[Ysite][Corner_Left_Down_XY.Y] == WHITE)
            {
                WHITE_cnt++;
            }
        }
    }
    else if(dir == 4)
    {
        for(Ysite = start ;Ysite < end ;Ysite++)
        {
            if(camera[Ysite][Corner_Right_Down_XY.Y] == WHITE)
            {
                WHITE_cnt++;
            }
        }
    }
    return WHITE_cnt;
}
//左圆环判断
void Cirque_Judge_Left()
{
    uint8 start_hang = 0;
    uint8 end_hang = 0;
    int buxianbianjie = 0;
    // printf("%d,%f\n",ImageStatus.Road_type,left_C);
    // // printf("r_start%d,%d\n",r_start,r_end);
    // printf("left_WhiteLine_40=%d\n",ImageStatus.left_WhiteLine_40);
    // printf("l%f\n",left_C);
    // printf("ImageStatus.L_L_Line=%d\n",ImageStatus.L_L_Line);
    // printf("%d\n",Find_WHITE_Hang(1,Corner_Left_Down_XY.Y,Corner_Left_Down_XY.Y+20));
    if(ImageStatus.Road_type == Straight 
          && (ImageStatus.Cornerleft_down == 1 ||ImageStatus.Cornerleft_Mid==1||ImageStatus.Cornerleft_up==1)
          && ImageStatus.Cornerright_down == 0
        //   &&ImageStatus.Cornerright_up==0
          &&ImageStatus.left_WhiteLine_40>=15
          &&ImageStatus.right_WhiteLine_40<=3
          &&right_circle_value>=0.98
        //   &&left_circle_value<=0.3
        //   && Find_WHITE_Hang(1,Corner_Left_Down_XY.Y,Corner_Left_Down_XY.Y + 20) >= 3 //左下拐点上方20行白边数
            //左拐点存在，右边是直线n,
    )
    {
            printf("jj11\n");
            ImageStatus.Road_type = LeftCirque_01;
            Left_Circle_progress= 1;//Left_Cir_Status
        }
}
void Cirque_Judge_Right()
{
     uint8 start_hang = 0;
    uint8 end_hang = 0;
    int buxianbianjie = 0;
    // printf("right_WhiteLine_40=%d\n",ImageStatus.right_WhiteLine_40);
    // printf("left_WhiteLine_40=%d\n",ImageStatus.left_WhiteLine_40);
    // printf("left_circle_value=%f",left_circle_value);
    // printf("right_circle_value=%f",right_circle_value);
    // printf("l%f\n",left_C);
    // printf("ImageStatus.L_L_Line=%d\n",ImageStatus.L_L_Line);
    // printf("%d\n",Find_WHITE_Hang(1,Corner_Left_Down_XY.Y,Corner_Left_Down_XY.Y+20));
    if(ImageStatus.Road_type == Straight 
          && (ImageStatus.Cornerright_down == 1 ||ImageStatus.Cornerright_Mid==1||ImageStatus.Cornerright_up==1)
          && ImageStatus.Cornerleft_down == 0
          &&ImageStatus.Cornerleft_up==0
          &&ImageStatus.right_WhiteLine_40>=13
          &&ImageStatus.left_WhiteLine_40<=3
          &&left_circle_value>=0.98
          &&right_circle_value<=0.3)
        //   && Find_WHITE_Hang(1,Corner_Left_Down_XY.Y,Corner_Left_Down_XY.Y + 20) >= 3 //左下拐点上方20行白边数
            //左拐点存在，右边是直线n,
    {
            printf("jj11\n");
            ImageStatus.Road_type = RightCirque;
            Right_Circle_progress= 1;//Left_Cir_Status
        }
}
void Corner_Left_Up(uint8 start, uint8 end ,uint8 dir) //从上向下
{
    uint8 Row_count = 0;
     if(dir == 1) //从上往下行偏差
    {
        for (Row_count = start ; Row_count < end; Row_count++)
        {
            // printf("%d,%d,%d %d\n",Row_count,Left_Deviation[Row_count - 1],Left_Deviation[Row_count],Left_Deviation[Row_count + 1]);
            // printf("111%d,%d\n", ImageDeal[Row_count].IsLeftFind, ImageDeal[Row_count-1].IsLeftFind);
            if (Row_count > 4 && 0 == ImageStatus.Cornerleft_up  &&
                Left_Deviation[Row_count - 1] >= 1 &&
                Left_Deviation[Row_count]>=0&&
                Left_Deviation[Row_count + 1]  >= 2 &&
                camera[Row_count+2][ImageDeal[Row_count].LeftBorder]==WHITE&&
                camera[Row_count+2][ImageDeal[Row_count].LeftBorder-2]==WHITE&&
                ImageDeal[Row_count].IsLeftFind == 'T' &&
                ImageDeal[Row_count - 1].IsLeftFind == 'T'&&
                ImageDeal[Row_count - 1].LeftBorder<40&&
                (Row_count + 1)<=35
                )
            {
                ImageStatus.Cornerleft_up = 1;
                Corner_Left_Up_XY.Y = Row_count + 1;
                Corner_Left_Up_XY.X = ImageDeal[Row_count - 1].LeftBorder;
                 printf("zuoshang%d,%d\n",Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y);
                break;
            }
        }
    }
    else
    {
        for (Row_count = start ; Row_count < end; Row_count++)//从下往上找
        {
           if ((Row_count > 4 && 0 == ImageStatus.Cornerleft_up  &&
                Left_Deviation[Row_count - 1] >= 5 &&
                Left_Deviation[Row_count] <= 15 && Left_Deviation[Row_count] >= 0 &&
                Left_Deviation[Row_count + 1] <= 7 && Left_Deviation[Row_count + 1] >= 0 &&
                ImageDeal[Row_count].IsLeftFind == 'T' &&
                ImageDeal[Row_count + 1].IsLeftFind == 'T'))
            {
                ImageStatus.Cornerleft_up= 1;
                Corner_Left_Up_XY.X = Row_count + 1;
                Corner_Left_Up_XY.Y = ImageDeal[Row_count - 1].LeftBorder;
                break;
            }
        }
    }
}
//   dir------- 0:从下到上        1：从上到下
void Corner_Right_Up(uint8 start, uint8 end, uint8 dir)
{
    uint8 Row_count = 0;
    if(dir == 1)
    {
        for (Row_count = end; Row_count > start; Row_count--)
        {
            // printf("%d,%d,%d,%d\n",Row_count,Right_Deviation[Row_count - 1],Right_Deviation[Row_count],Right_Deviation[Row_count + 1]);
            // printf("%d,%d\n",ImageDeal[Row_count].IsRightFind,ImageDeal[Row_count -1].IsRightFind);
            if(Row_count > 4 && 0 == ImageStatus.Cornerright_up  &&
               Right_Deviation[Row_count - 1] >=1 &&
               Right_Deviation[Row_count] >=1 &&
               Right_Deviation[Row_count + 1] >=2 &&
               ImageDeal[Row_count].IsRightFind == 'T' &&
               ImageDeal[Row_count - 1].IsRightFind == 'T'&&
               Row_count<=30 )
            {
                ImageStatus.Cornerright_up = 1;
                Corner_Right_Up_XY.Y = Row_count;
                Corner_Right_Up_XY.X = ImageDeal[Row_count - 1].RightBorder;
                if(Corner_Right_Up_XY.X<40)
                {
                    ImageStatus.Cornerright_up = 0;
                }
                printf("youshang:%d,%d\n",Corner_Right_Up_XY.X ,Corner_Right_Up_XY.Y);
                break;
            }
        }
    }
    else
    {
        for (Row_count = start; Row_count < end; Row_count++)
        {
            if(Row_count > 4 && 0 == ImageStatus.Cornerright_up  &&
               Right_Deviation[Row_count - 1] >=1 &&
               Right_Deviation[Row_count] <= 4 && Right_Deviation[Row_count] >= 0 &&
               Right_Deviation[Row_count + 1] <= 20 && Right_Deviation[Row_count + 1] >= 0 &&
               ImageDeal[Row_count].IsRightFind == 'T' &&
               ImageDeal[Row_count + 1].IsRightFind == 'T' )
            {
                ImageStatus.Cornerright_up = 1;
                Corner_Right_Up_XY.X = Row_count;
                Corner_Right_Up_XY.Y = ImageDeal[Row_count - 1].RightBorder;
                break;
            }
        }
    }
}
void Circle_Right_Up(uint8 start, uint8 end)
{
        uint8 Row_count = 0;
     for (Row_count = start; Row_count < end; Row_count++)
        {
            // printf("%d,%d,%d,%d\n",Row_count,Right_Deviation[Row_count - 1],Right_Deviation[Row_count],Right_Deviation[Row_count + 1]);
            // printf("%d,%d\n",ImageDeal[Row_count].IsRightFind,ImageDeal[Row_count -1].IsRightFind);
            if(Row_count > 4 && 0 == ImageStatus.Cornerright_up  &&
               Right_Deviation[Row_count - 1] >=5 &&
               Right_Deviation[Row_count] >=1 &&
               Right_Deviation[Row_count + 1] >=1 &&
               ImageDeal[Row_count].IsRightFind == 'T' &&
               ImageDeal[Row_count - 1].IsRightFind == 'T'
            //    &&Row_count<=30
             )
            {
                ImageStatus.Cornerright_up = 1;
                Corner_Right_Up_XY.Y = Row_count;
                Corner_Right_Up_XY.X = ImageDeal[Row_count - 1].RightBorder;
                // if(Corner_Right_Up_XY.X<40)
                // {
                //     ImageStatus.Cornerright_up = 0;
                // }
                printf("youshang:%d,%d\n",Corner_Right_Up_XY.X ,Corner_Right_Up_XY.Y);
                break;
            }
        }
}

void Corner_Right_Down(uint8 start, uint8 end,uint8 dir)
{
   int Row_count = 0;
   if(dir ==0)
   {
       for (Row_count = start; Row_count < end; Row_count++)
       {
        
          if (Row_count > 2 && ImageStatus.Cornerright_down == 0 &&
              // Right_Deviation[Row_count - 2] >= 0 &&
               (Right_Deviation[Row_count - 1] <=-2)&&
               Right_Deviation[Row_count] <= -1  &&
               Right_Deviation[Row_count + 1] <= -1
               //&& ImageDeal[Row_count + 3].RightBorder - ImageDeal[Row_count].RightBorder <= -4)
               && ImageDeal[Row_count - 1].IsRightFind == 'T'
               && ImageDeal[Row_count].IsRightFind == 'T'
               )
          {
              ImageStatus.Cornerright_down = 1;
              //gpio_set(BEEPPIN,1);
              Corner_Right_Down_XY.Y = (uint8)(Row_count - 1);
              Corner_Right_Down_XY.X = ImageDeal[Row_count - 2].RightBorder;
            //   you.X=ImageDeal[Row_count +1].RightBorder;
            //   you.Y=Row_count +1;
              break;
          }
       }
   }
   else
   {
       for (Row_count = start; Row_count > end; Row_count--)
       {
    //    printf("%d,%d,%d,%d\n",Row_count,Right_Deviation[Row_count - 1],Right_Deviation[Row_count],Right_Deviation[Row_count + 1]);
    //    printf("%d,%d\n",ImageDeal[Row_count ].IsRightFind,ImageDeal[Row_count + 1].IsRightFind);
           if (Row_count > 2 && ImageStatus.Cornerright_down == 0 &&
              // Right_Deviation[Row_count - 2] >= 0 &&
               (Right_Deviation[Row_count - 1] <=-2)&&
               Right_Deviation[Row_count] <= -1  &&
               Right_Deviation[Row_count + 1] <= 0
               //&& ImageDeal[Row_count + 3].RightBorder - ImageDeal[Row_count].RightBorder <= -4)
               && ImageDeal[Row_count + 1].IsRightFind == 'T'
               && ImageDeal[Row_count].IsRightFind == 'T'
               )
           {
               ImageStatus.Cornerright_down = 1;
               Corner_Right_Down_XY.Y = Row_count + 1;
               Corner_Right_Down_XY.X = ImageDeal[Row_count+1 ].RightBorder;
               you.X=ImageDeal[Row_count +4].RightBorder;
               you.Y=Row_count +4;
               printf("youxia%d,%d\n",Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y);
               break;
           }
       }
   }
}
void Corner_Left_Down(uint8 start, uint8 end,uint8 dir)
{
    int Row_count = 0;
    if(dir==1)//
    {
        for (Row_count = start; Row_count > end; Row_count--)
        {
        //    printf("%d,%d,%d,%d\n",Row_count,Left_Deviation[Row_count - 1],Left_Deviation[Row_count],Left_Deviation[Row_count + 1]);
        //    printf("%d,%d\n",ImageDeal[Row_count].IsLeftFind,ImageDeal[Row_count + 1].IsLeftFind);
            if (Row_count > 4 && ImageStatus.Cornerleft_down  == 0 &&

                Left_Deviation[Row_count - 1] <=-2 &&
                Left_Deviation[Row_count] >=0

                && (Left_Deviation[Row_count + 1] >= 0

                )
                && ImageDeal[Row_count + 1].IsLeftFind == 'T'
                && ImageDeal[Row_count].IsLeftFind == 'T'
                )
            {
               // gpio_set(BEEPPIN,0);
                ImageStatus.Cornerleft_down  = 1;
                Corner_Left_Down_XY.Y = (Row_count +1);
                Corner_Left_Down_XY.X = ImageDeal[Row_count +1].LeftBorder;
                zuo.X=ImageDeal[Row_count +4].LeftBorder;
                zuo.Y=Row_count+4;
                // cross_x_1=Row_count+4;
                // cross_y_1=ImageDeal[Row_count +4].LeftBorder;
                printf("zuoxia%d,%d\n",Corner_Left_Down_XY.X ,Corner_Left_Down_XY.Y);
                break;
            }
        }
        }
        if(dir==0)
        {
        for (Row_count = start; Row_count < end; Row_count++)
        {
            if (Row_count > 2 && ImageStatus.Cornerleft_down == 0 &&
                Left_Deviation[Row_count - 2] >= 0 &&
                Left_Deviation[Row_count - 1] >= 0 &&
                Left_Deviation[Row_count] >= -1
                && Left_Deviation[Row_count] < 5
                && (Left_Deviation[Row_count + 1] <= -2
                && ImageDeal[Row_count + 3].LeftBorder - ImageDeal[Row_count].LeftBorder >= 5)
                && ImageDeal[Row_count - 1].IsLeftFind == 1
                && ImageDeal[Row_count].IsLeftFind == 1
                )
            {
                //gpio_set(BEEPPIN,1);
                ImageStatus.Cornerleft_down  = 1;
                Corner_Left_Down_XY.X = (Row_count - 1);
                Corner_Left_Down_XY.Y = ImageDeal[Row_count - 2].LeftBorder;
                break;
            }
        }
        }
}
void Circle_Left_Down(uint8 start,uint8 end)
{
    uint8 Row_count=0;
        for (Row_count = start; Row_count > end; Row_count--)
        {
        //    printf("%d,%d,%d,%d\n",Row_count,Left_Deviation[Row_count - 1],Left_Deviation[Row_count],Left_Deviation[Row_count + 1]);
        //    printf("%d,%d\n",ImageDeal[Row_count].IsLeftFind,ImageDeal[Row_count + 1].IsLeftFind);
            if (Row_count > 4 && ImageStatus.Cornerleft_down  == 0 &&

                Left_Deviation[Row_count - 1] <=-2 &&
                Left_Deviation[Row_count] >=0

                && (Left_Deviation[Row_count + 1] >= 0

                )
                && ImageDeal[Row_count + 1].IsLeftFind == 'T'
                && ImageDeal[Row_count].IsLeftFind == 'T'
                )
            {
               // gpio_set(BEEPPIN,0);
                ImageStatus.Cornerleft_down  = 1;
                Corner_Left_Down_XY.Y = (Row_count +1);
                Corner_Left_Down_XY.X = ImageDeal[Row_count +1].LeftBorder;
                zuo.X=ImageDeal[Row_count +4].LeftBorder;
                zuo.Y=Row_count+4;
                // cross_x_1=Row_count+4;
                // cross_y_1=ImageDeal[Row_count +4].LeftBorder;
                printf("zuoxia%d,%d\n",Corner_Left_Down_XY.X ,Corner_Left_Down_XY.Y);
                break;
            }
}
}
void Corner_Get(void)
{
     Corner_Left_Up(10,50,1);
     Corner_Right_Up(10,50,1);
     Corner_Right_Down(55,20,1);//从小往上找
     Corner_Left_Down(55,20,1);//
    // left_circle_value = cur_circle_real(0, 45, 10);// 算曲率的（这里是越大越直）,a1是start,c1是end
    // right_circle_value = cur_circle_real(1, 50, 30);// 算曲率的（这里是越大越直）,a1是start,c1是end
    // if(
    //     ImageStatus.Road_type!=Ramp
    //     &&ImageStatus.Road_type!=Cross
    //     &&ImageStatus.Road_type!=LeftCirque_01
    //     &&ImageStatus.Road_type!=RightCirque
    // )
    // {
    //     ImageStatus.Road_type=Straight;
    // }
}
void Corner_Left_Middle(uint8 start, uint8 end)
{
    int Row_count = 0;
    for (Row_count = start; Row_count < end; Row_count++)
    {
        // printf("%d\n",Row_count);
        // printf("%d,%d,%d,%d,%d,%d\n",Left_Deviation[Row_count - 3],Left_Deviation[Row_count - 2],Left_Deviation[Row_count - 1],Left_Deviation[Row_count + 3],Left_Deviation[Row_count +2],Left_Deviation[Row_count +1]);
        // printf("%d,%d,%d,%d\n",ImageDeal[Row_count + 3].IsLeftFind,ImageDeal[Row_count +2].IsLeftFind,ImageDeal[Row_count - 3].IsLeftFind,ImageDeal[Row_count - 2].IsLeftFind);
        if (Row_count > 8 && ImageStatus.Cornerleft_Mid == 0 &&
           Left_Deviation[Row_count - 3] >= 0 && Left_Deviation[Row_count - 2] >= 1
           && Left_Deviation[Row_count - 1] >= 1 //&& Left_Deviation[Row_count - 4] >= 0
           && Left_Deviation[Row_count + 3] >=2  //&& Left_Deviation[Row_count + 4] <= 0
           && Left_Deviation[Row_count + 2] >= 1 && Left_Deviation[Row_count + 1] >= 1
           && ImageDeal[Row_count + 3].IsLeftFind == 'T' && ImageDeal[Row_count + 2].IsLeftFind ==  'T'
           && ImageDeal[Row_count - 3].IsLeftFind == 'T' && ImageDeal[Row_count - 2].IsLeftFind == 'T'
           &&ImageDeal[Row_count].LeftBorder<=40
           )
        {
            // printf("momo\n");
            //Console.WriteLine("左中拐点" + (Row_count + 1));
            ImageStatus.Cornerleft_Mid = 1;
            Corner_Left_Mid_XY.Y = (uint8)(Row_count);
            Corner_Left_Mid_XY.X = ImageDeal[Row_count].LeftBorder;
            printf("zuozhong%d,%d\n",Corner_Left_Mid_XY.X ,Corner_Left_Mid_XY.Y);
            break;
        }
    }
}
void Corner_Right_Middle(uint8 start, uint8 end)
{
    int Row_count = 0;
    for (Row_count = start; Row_count < end; Row_count++)
    {
        if (Row_count > 8 && ImageStatus.Cornerright_Mid == 0 &&
           Right_Deviation[Row_count - 3] >= 0 && Right_Deviation[Row_count - 2] >= 0
           && Right_Deviation[Row_count - 1] >= 0 //&& Right_Deviation[Row_count - 4] >= 0
           && Right_Deviation[Row_count + 3] <= 0
           && Right_Deviation[Row_count + 2] <= 0
           && Right_Deviation[Row_count + 1] <= 0
           && ImageDeal[Row_count - 3].IsRightFind == 1 && ImageDeal[Row_count + 2].IsRightFind == 1
           && ImageDeal[Row_count - 2].IsRightFind == 1 && ImageDeal[Row_count + 3].IsRightFind == 1
           )
        {
            //Console.WriteLine("左中拐点" + (Row_count + 1));
            ImageStatus.Cornerright_Mid = 1;
            Corner_Right_Mid_XY.X = (uint8)(Row_count);
            Corner_Right_Mid_XY.Y = ImageDeal[Row_count].RightBorder;
            break;
        }
    }
}
void Circle_Left_Up(uint8 start, uint8 end) //从上向下
{
    uint8 Row_count = 0;
    printf("hello9\n");
        for (Row_count = start ; Row_count < end; Row_count++)
        {
            if(camera[Row_count][ImageDeal[Row_count].IsLeftFind]==BLACK
            &&camera[Row_count+2][ImageDeal[Row_count].IsLeftFind]==WHITE//下方
            &&camera[Row_count][ImageDeal[Row_count].IsLeftFind+2]==WHITE//正左方
            &&camera[Row_count-2][ImageDeal[Row_count].IsLeftFind]==BLACK//上方
            &&camera[Row_count-2][ImageDeal[Row_count].IsLeftFind-2]==BLACK//右上
            &&camera[Row_count][ImageDeal[Row_count].IsLeftFind+2]==WHITE//右边
            &&camera[Row_count+2][ImageDeal[Row_count].IsLeftFind-2]==WHITE//左下
            &&ImageDeal[Row_count - 1].LeftBorder<=40
            )
            {
                printf("hello\n");
                ImageStatus.Cornerleft_up = 1;
                Corner_Left_Up_XY.Y = Row_count + 1;
                Corner_Left_Up_XY.X = ImageDeal[Row_count - 1].LeftBorder;
                printf("Circlezuoshang%d,%d\n",Corner_Left_Up_XY.X,Corner_Left_Up_XY.Y);
                break;
            }
        }
}
//  左圆环阶段处理
/*圆环处理
1.左中圆环即将消失，进入下一阶段
2.从右往左扫线，找到新的左边线（找到左上拐点），左上拐点即将消失，右边界几乎没有丢线进入下一阶段
3.右下拐点出现，进入下一阶段
4.右边赛道连续，右下拐点消失
5.二次扫线，右线为基准，从右往左扫线找到正确的左上拐点
*/
//  左圆环阶段
uint8 left_concer_flag = 0;
void Left_Cir_Progress_01()
{
    uint8 break_cnt = 0;
    //  printf("ImageStatus.right_WhiteLine_40=%d\n",ImageStatus.right_WhiteLine_40);
    //  printf("ImageStatus.left_WhiteLine_40=%d\n",ImageStatus.left_WhiteLine_40);
     
    if (ImageStatus.Road_type==LeftCirque_01) //圆环处理阶段--判断条件，切换状态
    { 
        switch (Left_Circle_progress)
        {
            case 1:        
        ImageStatus.Cornerleft_up = 0;
        Corner_Left_Up(20,50,1);//从上往下找上拐点，数据要再判断一下
        if(ImageStatus.Cornerleft_up == 1) //如果找到左上拐点,直接拉
        {
            printf("left_up\n");

            if(ImageStatus.Cornerleft_down==1)
            {    
                printf("up_down\n");
            Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,
                Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, 0, Corner_Left_Up_XY.Y, Corner_Left_Down_XY.Y);
            }
            else if(ImageStatus.Cornerleft_Mid==1)
            {
                printf("up_middle\n");
            Straight_Thru_01(Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y,
                Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, 0, Corner_Left_Up_XY.Y, Corner_Left_Mid_XY.Y);
            }
            else
            {
                printf("up-up\n");
                 Straight_Thru_01(20, 59,
                Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, 0, Corner_Left_Up_XY.Y, 59);
            }
            for(Ysite = Corner_Left_Down_XY.Y;Ysite > Corner_Left_Up_XY.Y ;Ysite--)
            {
                ImageDeal[Ysite].Center = (uint8)((ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder)/2);
            }
          printf("jj22\n");
        }
        else if(ImageStatus.Cornerleft_down == 1) //找到左下拐点
       {
        printf("left_down\n");

        if(ImageStatus.Cornerleft_up == 1) //找到左上拐点
        {
            printf("down-up\n");
            Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,
                Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, 0, Corner_Left_Up_XY.Y, Corner_Left_Down_XY.Y);
        }
       else if(ImageStatus.Cornerleft_Mid==1)//找到左中拐点
        {
            printf("down-middle\n");
            Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,
                Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y, 0, Corner_Left_Mid_XY.Y, Corner_Left_Down_XY.Y);
        }
        else
        {
            printf("down-down\n");
             Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,
                Corner_Left_Down_XY.X+1, Corner_Left_Down_XY.Y-5, 0, 20,Corner_Left_Down_XY.Y);
            printf("444\n");
        }
       } 
       else if(ImageStatus.Cornerleft_Mid==1)
       {
        printf("left_middle\n");
        if(ImageStatus.Cornerleft_up==1)
        {
            printf("middle-up\n");
            Straight_Thru_01(Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y,
                Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, 0, Corner_Left_Up_XY.Y, Corner_Left_Mid_XY.Y);
        }
        else if(ImageStatus.Cornerleft_down==1)
        {
            printf("middle-down\n");
             Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,
                Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y, 0, Corner_Left_Mid_XY.Y, Corner_Left_Down_XY.Y);
        }
        else
        {
            printf("middle-middle\n");
            Straight_Thru_01(20, 59,
                Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y, 0, Corner_Left_Mid_XY.Y,59);
        }
       }
       for(Ysite =50;Ysite >= 20 ;Ysite--)
            {
                ImageDeal[Ysite].Center = (uint8)((ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder)/2);
            }

            // printf("vv1\n");
            // printf("%d\n",l_start-l_end);
            Corner_Left_Middle(5, 40);
                if (
                ImageStatus.L_L_Line<10&&Left_Circle_progress==1)//或者用左右白行数
                {
                    // printf("kk1\n");
                   ImageStatus.Cornerleft_up= 0;
                   Left_Circle_progress = 2;
                    Circle_Left_Up(10, 50);            //先检测一下左上拐点，准备拉线
                    Corner_Left_Up(10,30,1);
                }
                break;
            case 2:
                if(ImageStatus.left_WhiteLine_40 < 10 )  //如果准备进
                { 
                    for(Ysite = 58;Ysite > 0;Ysite--)
                    {
                        
                        ImageDeal[Ysite].IsLeftFind = 0;
                        for(Xsite = ImageDeal[Ysite].RightBorder - 1;Xsite > 0;Xsite--)
                        {
                            if(camera[Ysite][Xsite + 1] ==WHITE &&camera[Ysite][Xsite+2]==WHITE
                                    &&camera[Ysite][Xsite] == BLACK && camera[Ysite][Xsite-1] == BLACK)//用右边界找左边界
                            {
                                ImageDeal[Ysite].LeftBorder = Xsite;
                                ImageDeal[Ysite].IsLeftFind = 'T';
                                break;
                            }
                        if (ImageDeal[Ysite].IsLeftFind == 'W')
                        {
                            ImageDeal[Ysite].LeftBorder = 0;
                            break_cnt++;
                            if(break_cnt > 5)
                            {
                                break_cnt = 0;
                                break;
                            }
                        }
                        Left_Deviation[Ysite + 1] = (int)(ImageDeal[Ysite + 1].LeftBorder - ImageDeal[Ysite].LeftBorder);
                        // printf("hello %d\n",Left_Deviation[Ysite + 1]);
                    }
                    }
                }
                ImageStatus.Cornerleft_up = 0;
                Corner_Left_Up(10,55,1);
                Corner_Right_Up(10,55,1);
                // printf("99\n");
                // if((ImageStatus.Cornerleft_up == 0 ||ImageStatus.Cornerright_up == 1)||( r_start <=45 && r_start - r_end < 50)||left_C<=0.5) 
                if(ImageStatus.left_WhiteLine_40>=20&&Left_Circle_progress==2) //左上拐点消失，并且右边几乎没有截断行
                    {
                        Left_Circle_progress = 3;
                    }                                  //进入入环中
                break;
            case 3:
                ImageStatus.Cornerright_down = 0;
                Circle_Right_Down(10, 50);
                printf("GGBONDImageStatus.Cornerright_down =%d\n",ImageStatus.Cornerright_down);
                if(ImageStatus.Cornerright_down == 1 &&Left_Circle_progress ==3)
                {
                    Left_Circle_progress = 4;
                }
                break;
            case 4:    
             if(ImageStatus.Cornerright_down == 0
                &&Left_Circle_progress==4)
                {
                      uint8 R_F = 0;
                    for(Ysite = 53;Ysite>ImageStatus.OFFLine+1;Ysite--){
                    if(ImageDeal[Ysite].IsRightFind == 'T')
                        R_F++;
                    if(ImageDeal[Ysite].IsRightFind == 'T'&&ImageDeal[Ysite - 1].IsRightFind == 'T'
                    &&ImageDeal[Ysite-2].IsRightFind == 'T'&&ImageDeal[Ysite - 3].IsRightFind == 'T'&&R_F>=10
                    )
                        {
                                Left_Circle_progress=5;
                                break;
                        }  
                }
                 printf("R_F%d\n",R_F); 
                } 

                // if(ImageStatus.Cornerright_down == 0
                // &&Left_Circle_progress==4
                //        )
                // {
                //    Left_Circle_progress = 5;
                //    ImageStatus.Cornerright_up = 0;
                //    Corner_Left_Up(20,58,1);
                // }
                break;
            case 5:
                for(Ysite = 58;Ysite >= 0;Ysite--)
                {
                    ImageDeal[Ysite].IsLeftFind = 'W';
                 for(Xsite = ImageDeal[Ysite].RightBorder - 2;Xsite >=0;Xsite--)
                    {
                        if(camera[Ysite][Xsite] == BLACK && camera[Ysite][Xsite - 1] == BLACK)
                        {
                            ImageDeal[Ysite].LeftBorder = Xsite;
                            ImageDeal[Ysite].IsLeftFind = 'T';
                            break;
                        }
                        if(ImageDeal[Ysite].IsLeftFind == 'W')
                            ImageDeal[Ysite].LeftBorder = 0;
                    }
                    Left_Deviation[Ysite + 1] = ((int)ImageDeal[Ysite + 1].LeftBorder - ImageDeal[Ysite].LeftBorder);
                }
                ImageStatus.Cornerleft_up = 0;
                Circle_Left_Up(10, 50);
                printf("YYY\n");
                printf("ImageStatus.Cornerleft_up=%d,Left_Circle_progress=%d\n",ImageStatus.Cornerleft_up,Left_Circle_progress);
               
                if(ImageStatus.Cornerleft_up == 1  
                &&Left_Circle_progress==5
                )
                {
                    Left_Circle_progress = 6;
                }
                break;
            default:break;
        }
        // printf("Right_Circle_progress=%d\n",Right_Circle_progress);
        Left_Cir_Dispose_01(Left_Circle_progress);
    }
}
int Right_Cir_Dispose_01(int status)
{
     uint8 start_hang = 0;
    uint8 end_hang = 0;
    int buxianbianjie = 0;
    static int buxian_flag = 0;
    uint8 i = 0;

    //("----------------------圆环补线操作-------------------------------------");
    switch (status)
    {
        case 1:
        // printf("dd1\n");
            if(ImageStatus.Cornerright_Mid== 1)       //如果有左中拐点，拟合右线补左线
            {
               // test = 1;
                // printf("momo\n");
                Straight_Thru_01(Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y, Corner_Right_Mid_XY.X+1, Corner_Right_Mid_XY.Y+1, 1, Corner_Right_Mid_XY.Y,59);
                Fill_Top = Corner_Right_Mid_XY.Y;
                //ImageStatus.OFFLine = Fill_Top;
            }
            else    //如果没有左中，判断是否有左上，因为左中与左上有些相似
            {
                ImageStatus.Cornerright_up = 0;
                Corner_Right_Up(20,45,1);
                if(ImageStatus.Cornerright_up== 1)    //如果有左上拐点，拟合右线补左线
                {
                  //  test = 2;
                    Straight_Thru_01(Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, Corner_Right_Up_XY.X+1, Corner_Right_Up_XY.Y+1, 1, Corner_Right_Up_XY.Y,59);
                    Fill_Top = Corner_Right_Up_XY.Y;
                    ImageStatus.OFFLine = Fill_Top;
                }
            }
            break;
        case 2:
        // printf("dd2\n");
        printf("ImageStatus.Cornerright_up=%d\n",ImageStatus.Cornerright_up);
             if(ImageStatus.Cornerright_up == 1 && ImageStatus.right_WhiteLine_40 < 14)  //如果有左上拐点，并且准备开始拐
            {
                printf("r_end=%d\n",r_end);
                if(r_end<=32) //定行上边界 //Y要修改
                {
                    // printf("ww\n");

                    //直接拉左上拐点和右下
                    // Straight_Thru_01(Corner_Left_Up_XY.X + 20, Corner_Left_Up_XY.Y - 5,
                    //             ImageDeal[Corner_Left_Up_XY.Y].RightBorder, Corner_Left_Up_XY.Y - 4, 1, Corner_Left_Up_XY.Y - 5,59) ;
                    Straight_Thru_01(Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y ,
                           0, 49, 0, Corner_Right_Up_XY.Y, 59 );
                }
                else
                {
                    // printf("mm\n");
                        //直接拉左上拐点和右下

                    Straight_Thru_01(Corner_Right_Up_XY.X , Corner_Right_Up_XY.Y ,
                                0, 49, 0,Corner_Right_Up_XY.Y ,59) ;
                        // Straight_Thru_01(Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y ,
                        //         ImageDeal[Corner_Right_Up_XY.Y].RightBorder, 59, 0,  Corner_Right_Up_XY.Y ,59);
                    
                }
                for (i = Corner_Right_Up_XY.Y; i <= 79; i++)
                {
                    //ImageDeal[i].LeftBorder = 79;
                    ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
                }
            }
            else 
            // if(ImageStatus.Cornerleft_up == 0)
            {
                Straight_Thru_01(65, 0 ,
                            0, 49, 0, 20, 59); 
            //    Straight_Thru_01(35, 25 ,
            //                 79, 59, 1, 20, 59); 
            }
        break;
      case 3:

        break;
      case 4:
          if(ImageStatus.Cornerleft_down == 0)
          {
            // printf("RRR%d\n",Right_Deviation[first_break_l - 3]);
            // printf("%d\n",Right_Deviation[first_break_l - 3] );
              //辅助找出环点
              if(first_break_l > 5 && first_break_l > 30 //重新看参数 2024/4/23 22:21
                      && Left_Deviation[first_break_r - 3] <= 2)
              {
                
                  ImageStatus.Cornerleft_down = 1;
                  Corner_Left_Down_XY.Y = first_break_l;
                  Corner_Left_Down_XY.X = ImageDeal[first_break_l].LeftBorder;
              }
          }
          printf("%d,%d\n",Corner_Left_Down_XY.X,Corner_Left_Down_XY.Y);
          buxian_flag = 0;
          if(ImageStatus.Cornerleft_down == 1 && Corner_Left_Down_XY.Y < ImageStatus.TowPoint)//此处为图像前瞻 较远
          {
            printf("WW\n");
               Straight_Thru_01(Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y, Corner_Left_Down_XY.X+1, Corner_Left_Down_XY.Y-1, 0, 0,Corner_Left_Down_XY.Y);//改变函数需特殊处理
            for (i = 0; i <= Corner_Left_Down_XY.Y; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
                    if(camera[i][ImageDeal[i].LeftBorder] == BLACK && camera[i - 1][ImageDeal[i].LeftBorder] == BLACK
                           && camera[i + 1][ImageDeal[i].LeftBorder] == WHITE)
                       break;                       
              }
               Fill_Top = (i + Corner_Left_Down_XY.Y) / 2;
               ImageStatus.OFFLine = Fill_Top;
          }                                                                              //速度决定前瞻
           if(ImageStatus.Cornerleft_down == 1 && Corner_Left_Down_XY.Y >= ImageStatus.TowPoint && Corner_Left_Down_XY.Y <=55) //比较近
          {

              for(Ysite = 10 ; Ysite <55 ; Ysite++)
              {
                  if( camera[Ysite +3][79] == WHITE//黑白跳变边界
                          && camera[Ysite + 1][79] == WHITE
                          && camera[Ysite - 1][79] == BLACK
                          && camera[Ysite - 3][79] == BLACK)
                  {
                    // printf("momo%d\n",Ysite);
                      end_hang = Ysite;
                      break;
                  }
                  else
                      end_hang = 20;
              }
              printf("888\n");
            //   printf("%d\n",Corner_Right_Down_XY.Y);
              Straight_Thru_01(59, end_hang, //出环特殊处理,上方都是黑色，黑白跳变
                    Corner_Left_Down_XY.X, Corner_Left_Down_XY.Y,  //拉线
                    0,end_hang,Corner_Left_Down_XY.Y);

              for (i = 0; i <= end_hang; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
              }

              Fill_Top = end_hang;
              ImageStatus.OFFLine = Fill_Top;
          }
          else 
          {
            printf("111\n");
             Straight_Thru_01(0, 59, //出环特殊处理
                    79, 20,  //拉线
                    0,20,59);
            for (i = 0; i <= 59; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
              }

          }
        break;
      case 5:
      printf("KKK\n");
          if(ImageStatus.Cornerright_up == 1
          &ImageStatus.WhiteLine<=5)
          {
              start_hang = Corner_Right_Up_XY.Y ;
              end_hang = Corner_Right_Up_XY.Y+ 4;
              Straight_Thru_01(Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y,Corner_Right_Up_XY.X+1,Corner_Right_Up_XY.Y+1,
                      1,Corner_Right_Up_XY.Y,59);
              Fill_Top = Corner_Right_Up_XY.Y + 4;
              //ImageStatus.OFFLine = Fill_Top;
          }
          break;
      case 6:
          if(ImageStatus.Cornerright_up == 1)
          {
              start_hang = Corner_Right_Up_XY.Y ;
              end_hang = Corner_Right_Up_XY.Y + 4;
              Straight_Thru_01(Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y,Corner_Right_Up_XY.X+1,Corner_Right_Up_XY.Y+1,
                      1,Corner_Right_Up_XY.Y,59);
              Fill_Top = Corner_Right_Up_XY.Y + 4;
              //ImageStatus.OFFLine = Fill_Top;
          }
        if(ImageStatus.Road_type==RightCirque&&left_circle_value>=0.97&&right_circle_value>=0.97&&Right_Circle_progress==6)
        {
        Right_Circle_progress= 0;
        ImageStatus.Road_type= Straight;
        ImageStatus.Last_Road_type = RightCirque;//是圆环但是还要区别左/右圆环，标志位的确定
        }
        //   printf("kll\n");
        //   out_cir = 1;
          break;
    }
    return status;
}
int Left_Cir_Dispose_01(int status)
{
    uint8 start_hang = 0;
    uint8 end_hang = 0;
    int buxianbianjie = 0;
    static int buxian_flag = 0;
    uint8 i = 0;

    //("----------------------圆环补线操作-------------------------------------");
    switch (status)
    {
        case 1:
        // printf("dd1\n");
            if(ImageStatus.Cornerleft_Mid== 1)       //如果有左中拐点，拟合右线补左线
            {
               // test = 1;
                // printf("momo\n");
                Straight_Thru_01(Corner_Left_Mid_XY.X, Corner_Left_Mid_XY.Y, Corner_Left_Mid_XY.X-1, Corner_Left_Mid_XY.Y+1, 0, Corner_Left_Mid_XY.Y,59);
                Fill_Top = Corner_Left_Mid_XY.Y;
                //ImageStatus.OFFLine = Fill_Top;
            }
            else    //如果没有左中，判断是否有左上，因为左中与左上有些相似
            {
                ImageStatus.Cornerleft_up = 0;
                Corner_Left_Up(20,45,0);
                if(ImageStatus.Cornerleft_up== 1)    //如果有左上拐点，拟合右线补左线
                {
                  //  test = 2;
                    Straight_Thru_01(Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y, Corner_Left_Up_XY.X-1, Corner_Left_Up_XY.Y+1, 0, Corner_Left_Up_XY.Y,59);
                    Fill_Top = Corner_Left_Up_XY.Y;
                    ImageStatus.OFFLine = Fill_Top;
                }
            }
            break;
        case 2:
        // printf("dd2\n");
        // printf("IMAGE=%d\n",ImageStatus.Cornerleft_up);
             if(ImageStatus.Cornerleft_up == 1 && ImageStatus.right_WhiteLine_40 < 14)  //如果有左上拐点，并且准备开始拐
            {
                // printf("bb3\n");
                if(l_end<=32) //定行上边界 //Y要修改
                {
                    // printf("ww\n");

                    //直接拉左上拐点和右下
                    // Straight_Thru_01(Corner_Left_Up_XY.X + 20, Corner_Left_Up_XY.Y - 5,
                    //             ImageDeal[Corner_Left_Up_XY.Y].RightBorder, Corner_Left_Up_XY.Y - 4, 1, Corner_Left_Up_XY.Y - 5,59) ;
                    Straight_Thru_01(Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y ,
                           79, 59, 1, Corner_Left_Up_XY.Y, 59 );
                }
                else
                {
                    // printf("mm\n");
                        //直接拉左上拐点和右下

                    Straight_Thru_01(Corner_Left_Up_XY.X , Corner_Left_Up_XY.Y ,
                                79, 59, 1,Corner_Left_Up_XY.Y ,59) ;
                        // Straight_Thru_01(Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y ,
                        //         ImageDeal[Corner_Left_Up_XY.Y].RightBorder, 59, 1,  Corner_Left_Up_XY.Y ,59);
                    
                }
                for (i = 0; i >= Corner_Left_Up_XY.Y; i++)
                {
                    //ImageDeal[i].LeftBorder = 79;
                    ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
                }
            }
            else 
            // if(ImageStatus.Cornerleft_up == 0)
            {
                Straight_Thru_01(15, 0 ,
                            79, 59, 1, 20, 59); 
            //    Straight_Thru_01(35, 25 ,
            //                 79, 59, 1, 20, 59); 
            }
        break;
      case 3:

        break;
      case 4:
          if(ImageStatus.Cornerright_down == 0)
          {
            // printf("RRR%d\n",Right_Deviation[first_break_l - 3]);
            // printf("%d\n",Right_Deviation[first_break_l - 3] );
              //辅助找出环点
              if(first_break_r > 5 && first_break_r > 30
                      && Right_Deviation[first_break_l - 3] <= 2)
              {
                
                  ImageStatus.Cornerright_down = 1;
                  Corner_Right_Down_XY.Y = first_break_r;
                  Corner_Right_Down_XY.X = ImageDeal[first_break_r].RightBorder;
              }
          }
          printf("%d,%d\n",Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y);
          buxian_flag = 0;
          if(ImageStatus.Cornerright_down == 1 && Corner_Right_Down_XY.Y < ImageStatus.TowPoint)//此处为图像前瞻 较远
          {
               Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y, Corner_Right_Down_XY.X-1, Corner_Right_Down_XY.Y-1, 1, 0,Corner_Right_Down_XY.Y);//改变函数需特殊处理
            for (i = 0; i <= Corner_Right_Down_XY.Y; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
                    if(camera[i][ImageDeal[i].RightBorder] == BLACK && camera[i - 1][ImageDeal[i].RightBorder] == BLACK
                           && camera[i + 1][ImageDeal[i].RightBorder] == WHITE)
                       break;                       
              }
               Fill_Top = (i + Corner_Right_Down_XY.Y) / 2;
               ImageStatus.OFFLine = Fill_Top;
          }                                                                              //速度决定前瞻
          else if(ImageStatus.Cornerright_down == 1 && Corner_Right_Down_XY.Y >= ImageStatus.TowPoint && Corner_Right_Down_XY.Y <=55) //比较近
          {
              ImageStatus.left_corner_flag = 1;


              for(Ysite = 10 ; Ysite <55 ; Ysite++)
              {
                  if( camera[Ysite +3][79] == WHITE
                          && camera[Ysite + 1][79] == WHITE
                          && camera[Ysite - 1][79] == BLACK
                          && camera[Ysite - 3][79] == BLACK)
                  {
                    // printf("momo%d\n",Ysite);
                      end_hang = Ysite;
                      break;
                  }
                  else
                      end_hang = 20;
              }
            //   printf("888\n");
            //   printf("%d\n",Corner_Right_Down_XY.Y);
              Straight_Thru_01(0, end_hang, //出环特殊处理,上方都是黑色，黑白跳变
                    Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,  //拉线
                    1,end_hang,Corner_Right_Down_XY.Y);

              for (i = 0; i <= end_hang; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
              }

              Fill_Top = end_hang;
              ImageStatus.OFFLine = Fill_Top;
          }
          else
          {
            // printf("111\n");
             Straight_Thru_01(0, 0, //出环特殊处理
                    79, 59,  //拉线
                    1,0,59);
            for (i = 0; i <= 59; i++)
              {
                  ImageDeal[i].Center = (uint8)((ImageDeal[i].LeftBorder + ImageDeal[i].RightBorder)/2);
              }

          }
        break;
      case 5:
          if(ImageStatus.Cornerleft_up == 1)
          {
              start_hang = Corner_Left_Up_XY.X ;
              end_hang = Corner_Left_Up_XY.X + 4;
              Straight_Thru_01(Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y,Corner_Left_Up_XY.X-1,Corner_Left_Up_XY.Y+1,
                      0,Corner_Left_Up_XY.Y,59);
              Fill_Top = Corner_Left_Up_XY.Y + 4;
              //ImageStatus.OFFLine = Fill_Top;
          }
          break;
      case 6:
          if(ImageStatus.Cornerleft_up == 1)
          {
              start_hang = Corner_Left_Up_XY.X ;
              end_hang = Corner_Left_Up_XY.X + 4;
              Straight_Thru_01(Corner_Left_Up_XY.X, Corner_Left_Up_XY.Y,Corner_Left_Up_XY.X-1,Corner_Left_Up_XY.Y+1,
                      0,Corner_Left_Up_XY.Y,59);
              Fill_Top = Corner_Left_Up_XY.Y + 4;
              //ImageStatus.OFFLine = Fill_Top;
          }
        if(ImageStatus.Road_type==LeftCirque_01&&left_circle_value>=0.97&&right_circle_value>=0.97&&Left_Circle_progress==6
        &&ImageStatus.left_WhiteLine_40<=4)
        {
        Left_Circle_progress= 0;
        ImageStatus.Road_type= Straight;
        ImageStatus.Last_Road_type = RightCirque;//是圆环但是还要区别左/右圆环，标志位的确定
        }
        //   printf("kll\n");
        //   out_cir = 1;
          break;
    }
    return status;
}
void Right_Cir_Progress_01()
{
    uint8 break_count=0;
   if (ImageStatus.Road_type==RightCirque) //圆环处理阶段--判断条件，切换状态
    { 
        switch (Right_Circle_progress)
        {
            case 1:        
        ImageStatus.Cornerright_up = 0;
        Corner_Right_Up(20,50,1);//从上往下找上拐点，数据要再判断一下
        if(ImageStatus.Cornerright_up == 1) //如果找到左上拐点,直接拉
        {
            printf("right_up\n");

            if(ImageStatus.Cornerright_down==1)
            {    
                printf("up_down\n");
            Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,
                Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, 1, Corner_Right_Up_XY.Y, Corner_Right_Down_XY.Y);
            }
            else if(ImageStatus.Cornerright_Mid==1)
            {
                printf("up_middle\n");
            Straight_Thru_01(Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y,
                Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, 1, Corner_Right_Up_XY.Y, Corner_Right_Mid_XY.Y);
            }
            else
            {
                printf("up-up\n");
                 Straight_Thru_01(60, 59,
                Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, 1, Corner_Right_Up_XY.Y, 59);
            }
            for(Ysite =59;Ysite > Corner_Right_Up_XY.Y ;Ysite--)
            {
                ImageDeal[Ysite].Center = (uint8)((ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder)/2);
            }
          printf("jj22\n");
        }
        else if(ImageStatus.Cornerright_down == 1) //找到左下拐点
       {
        printf("right_down\n");

        if(ImageStatus.Cornerright_up == 1) //找到左上拐点
        {
            printf("down-up\n");
            Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,
                Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, 1, Corner_Right_Up_XY.Y, Corner_Right_Down_XY.Y);
        }
       else if(ImageStatus.Cornerright_Mid==1)//找到左中拐点
        {
            printf("down-middle\n");
            Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,
                Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y, 1, Corner_Right_Mid_XY.Y, Corner_Right_Down_XY.Y);
        }
        else
        {
            printf("down-down\n");
             Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,
                Corner_Right_Down_XY.X+1, Corner_Right_Down_XY.Y-5, 1, 20,Corner_Right_Down_XY.Y);
            printf("444\n");
        }
       } 
       else if(ImageStatus.Cornerright_Mid==1)
       {
        printf("right_middle\n");
        if(ImageStatus.Cornerright_up==1)
        {
            printf("middle-up\n");
            Straight_Thru_01(Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y,
                Corner_Right_Up_XY.X, Corner_Right_Up_XY.Y, 1, Corner_Right_Up_XY.Y, Corner_Right_Mid_XY.Y);
        }
        else if(ImageStatus.Cornerright_down==1)
        {
            printf("middle-down\n");
             Straight_Thru_01(Corner_Right_Down_XY.X, Corner_Right_Down_XY.Y,
                Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y, 1, Corner_Right_Mid_XY.Y, Corner_Right_Down_XY.Y);
        }
        else
        {
            printf("middle-middle\n");
            Straight_Thru_01(20, 59,
                Corner_Right_Mid_XY.X, Corner_Right_Mid_XY.Y, 1, Corner_Right_Mid_XY.Y,59);
        }
       }
       for(Ysite =50;Ysite >= 20 ;Ysite--)
            {
                ImageDeal[Ysite].Center = (uint8)((ImageDeal[Ysite].LeftBorder + ImageDeal[Ysite].RightBorder)/2);
            }

            // printf("vv1\n");
            // printf("%d\n",l_start-l_end);
            Corner_Right_Middle(5, 40);
                if (
                ImageStatus.L_R_Line<5&&Right_Circle_progress==1)//或者用左右白行数
                {
                    // printf("kk1\n");
                   ImageStatus.Cornerright_up= 0;
                   Right_Circle_progress = 2;
                    // Circle_Right_Up(10, 50);            //先检测一下右上拐点，准备拉线
                    Corner_Right_Up(10,30,1);
                }
                break;
            case 2:
                if(ImageStatus.right_WhiteLine_40 < 10 )  //如果准备进
                { 
                    for(Ysite = 58;Ysite >= 0;Ysite--)
                    {
                        ImageDeal[Ysite].IsRightFind = 'W';
                        for(Xsite = ImageDeal[Ysite].LeftBorder +1;Xsite <=79;Xsite++)
                        {
                            if(camera[Ysite][Xsite ] ==BLACK &&camera[Ysite][Xsite+1]==BLACK
                                    &&camera[Ysite][Xsite-1] == WHITE)//用左边界找右边界
                            {
                                // printf("UUU\n");
                                ImageDeal[Ysite].RightBorder = Xsite;
                                ImageDeal[Ysite].IsRightFind = 'T';
                                // printf("ImageDeal[%d].RightBorder=%d\n",Ysite,Xsite);
                                break;
                            }
                        if (ImageDeal[Ysite].IsRightFind == 'W')
                        {
                            ImageDeal[Ysite].RightBorder = 79;
                        }
                        Right_Deviation[Ysite + 1] = (int)(ImageDeal[Ysite + 1].RightBorder - ImageDeal[Ysite].RightBorder);
                        // printf("hello %d\n",Left_Deviation[Ysite + 1]);
                    }
                    }
                }
                ImageStatus.Cornerright_up = 0;
                 Circle_Right_Up(10, 50);
                // Corner_Right_Up(10,55,1);
                // printf("99\n");
                // if((ImageStatus.Cornerleft_up == 0 ||ImageStatus.Cornerright_up == 1)||( r_start <=45 && r_start - r_end < 50)||left_C<=0.5) 
                if(ImageStatus.right_WhiteLine_40>=10&&Right_Circle_progress==2) //左上拐点消失，并且右边几乎没有截断行
                    {
                        Right_Circle_progress = 3;
                    }                                  //进入入环中
                break;
            case 3:
               ImageStatus.Cornerleft_down=0;
                  Circle_Left_Down(55, 35);
                if(ImageStatus.Cornerleft_down == 1 &&Right_Circle_progress ==3)
                {
                    Right_Circle_progress = 4;
                }
                break;
            case 4:    
            printf("HH/n");
               if(ImageStatus.Cornerleft_down == 0
                &&Right_Circle_progress==4
                &&ImageStatus.WhiteLine>=10)
                { 
               Right_Circle_progress=5;
                }
                // if(ImageStatus.Cornerright_down == 0
                // &&Left_Circle_progress==4
                //        )
                // {
                //    Left_Circle_progress = 5;
                //    ImageStatus.Cornerright_up = 0;
                //    Corner_Left_Up(20,58,1);
                // }
                break;
            case 5:
                for(Ysite = 58;Ysite >= 0;Ysite--)
                {
                    ImageDeal[Ysite].IsRightFind = 'W';
                 for(Xsite = ImageDeal[Ysite].LeftBorder + 1;Xsite <=79;Xsite++)
                    {
                        if(camera[Ysite][Xsite] == BLACK 
                        && camera[Ysite][Xsite + 1] == BLACK
                        &&camera[Ysite][Xsite - 1]==WHITE)
                        {
                            ImageDeal[Ysite].RightBorder = Xsite;
                            ImageDeal[Ysite].IsRightFind = 'T';
                            break;
                        }
                        if(ImageDeal[Ysite].IsRightFind == 'W')
                            ImageDeal[Ysite].RightBorder =79 ;
                    }
                    Right_Deviation[Ysite + 1] = ((int)ImageDeal[Ysite + 1].RightBorder - ImageDeal[Ysite].RightBorder);
                }
                ImageStatus.Cornerright_up = 0;
                Circle_Right_Up(20,40);
                // printf("ImageStatus.Cornerright_up=%d,Right_Circle_progress=%d\n",ImageStatus.Cornerright_up,Right_Circle_progress);
                if(ImageStatus.Cornerright_up == 1  
                &&Right_Circle_progress==5
                &&ImageStatus.WhiteLine<=5
                )
                {
                    Right_Circle_progress = 6;
                }
                break;
            default:break;
        }
        // printf("Right_Circle_progress=%d\n",Right_Circle_progress);
        Right_Cir_Dispose_01(Right_Circle_progress);
    }  
}
void Circle_Right_Down(uint8 start, uint8 end)
{
   int Row_count = 0;
   printf("yy\n");
       for (Row_count = start; Row_count < end; Row_count++)
       {
        // printf("%d,%d,%d,%d,%d\n",Row_count,ImageDeal[Row_count -2 ].IsRightFind,ImageDeal[Row_count -1 ].IsRightFind,ImageDeal[Row_count].IsRightFind,ImageDeal[Row_count+1 ].IsRightFind);
          if (ImageDeal[Row_count -2 ].IsRightFind=='W'
               &&ImageDeal[Row_count - 1].IsRightFind=='W'
               && ImageDeal[Row_count].IsRightFind == 'T'
               && ImageDeal[Row_count+1].IsRightFind == 'T'
               )
          {
              ImageStatus.Cornerright_down = 1;
              Corner_Right_Down_XY.Y = (uint8)(Row_count);
              Corner_Right_Down_XY.X = ImageDeal[Row_count].RightBorder;
              printf("YOUXIA=%d,%d\n",Corner_Right_Down_XY.X,Corner_Right_Down_XY.Y );
              break;
          }
       }
}
void zebra(uint8 start_point, uint8 end_point , int dir)
{
    uint8 times = 0;
     black_blocks = 0;
    for (Ysite = start_point; Ysite <= end_point; Ysite++)
    { 
        
        uint8 cursor = 0;
        if(dir == 1)
        {
            for (Xsite = 15;Xsite <=70; Xsite++)
            {
                if (camera[Ysite][Xsite] == BLACK
                &&camera[Ysite][Xsite+1] == BLACK
                &&camera[Ysite][Xsite+2] == WHITE
                )
                {
                        cursor++;
                }
            }
              if (cursor >= 5 && cursor <= 15) //黑色区域像素点个数
                    {
                        black_blocks++; //黑色区域块,多少行
                        cursor = 0;
                    }
                    else
                        cursor = 0;
                        // printf("black_blocks=%d\n",black_blocks);
        }
    }
}

void Check_Zebra_Line(uint8 start_point, uint8 end_point , int dir)
{
    uint8 times = 0;
    
    for (Ysite = start_point; Ysite <= end_point; Ysite++)
    { 
         black_blocks = 0;
        uint8 cursor = 0;
        if(dir == 1)
        {
            for (Xsite = ImageDeal[Ysite].LeftBorder;Xsite <=ImageDeal[Ysite].RightBorder; Xsite++)
            {
                if (camera[Ysite][Xsite] == BLACK)
                {
                    if (cursor >= 20)
                        break;//当黑色元素超过黑色块长度的操作   break;
                    else
                        cursor++;
                }
                else
                {
                    if (cursor >= 3 && cursor <= 15) //黑色区域像素点个数
                    {
                        black_blocks++; //黑色区域块,多少行
                        cursor = 0;
                    }
                    else
                        cursor = 0;
                }
            }
        }
        printf("black_blocks=%d\n",black_blocks);

        if (black_blocks >= 6 && black_blocks <= 12)    
        {
        printf("black_blocks=%d\n",black_blocks);
        times++;
        }
    if (black_blocks >= 3 && black_blocks <= 5)    
        {
        printf("black_blocks=%d\n",black_blocks);
        }  
              if (times >= 2)
        {
            flag_zebra_line= flag_zebra_line+1;
            zebra_times = times;
            zebra_top = Ysite;
            printf("Ysite%d\n",Ysite);
            printf("flag_zebra_line%d\n",flag_zebra_line);
            break;
        }
        else
            flag_zebra_line = 0;
    }
    printf("times%d\n",times);
}
void Stop_test_2()
{
     zebra(20,50,1);
    //  printf("black_blocks=%d",black_blocks);
     if((black_blocks>=10 ))// || Left_Back_Ku == 1)
        {
        ImageStatus.Road_type=Stop;
        }
}

void maopao(int arr[], int size, int result[],int arr1[],int result1[]) //前面两个用于冒泡排序，后面是跟着的x
{
// 将原始数组复制到结果数组中
for(int i = 0; i < size; i++) 
{
    result[i] = arr[i];
}
for(int i = 0; i < size-1; i++) 
{
    for(int j = 0; j < size-i-1; j++) 
    {
        if(result[j] < result[j+1]) 
        {  // 由大到小排序，改为 >
            std::swap(result[j], result[j+1]);
            std::swap(result1[j], result1[j+1]);
        }
    }
}
}
static int cone_flag_5=0,cone_num=0;

void renwu_left()
{
 printf("counterR=%d\n",counterR);
 printf("counterL=%d\n",counterL);
 cone_flag_5=0;
 if(number_num>=6)
 {
    number_num=0;
    cone_flag_5=1;
 }
printf("cone_num=%d\n",number_num);
printf("cone_flag_5=%d\n",cone_flag_5);
printf("ImageStatus.Road_type=%d\n",ImageStatus.Road_type);
printf("ImageStatus.OFFLine=%d\n",ImageStatus.OFFLine);
 if(((cone_flag_5==1&&counterL>=2&&counterR>=2)||
 (cone_flag_5==1&&counterL>=1&&counterR>=0))
 &&ImageStatus.OFFLine>=18
&&ImageStatus.Road_type!=Task_Left)
 {
  printf("Task_Left\n");
  ImageStatus.Road_type=Task_Left;
  //det_flag_1=1;//这里不知道要不要
 }
 maopao(y_1,number_num,y_new,x_1,x_new);//分别对左右的值进行新的冒泡排序
 printf("y_new=%d\n",y_new[0]);
 printf("y_new1=%d\n",y_new[1]);
 printf("y_new2=%d\n",y_new[2]);
 printf("y_new3=%d\n",y_new[3]);
if(ImageStatus.Road_type==Task_Left&&y_new[0]>=35)
{
  printf("hello\n");
  Task_Left_progress=1;
}
else if (ImageStatus.Road_type==Task_Left
&&Task_Left_progress==1
&&number_num>=3
&&y_new[0]>=30
&&y_new[1]>=20
&&y_new[2]>=20
)
{
   Task_Left_progress=2;
   printf("LLLL2\n");
}
else if(ImageStatus.Road_type==Task_Left_Out
// &&ImageStatus.left_WhiteLine_40<=2
// &&ImageStatus.right_WhiteLine_40<=2
&&left_circle_value>=0.99
&&right_circle_value>=0.99
&&Task_Left_progress==2)
{
printf("ZHENG\n");
Task_Left_progress=3;
}
}
void renwu_left_handle(int Task_Left_progress)
{
    if(Task_Left_progress==1)
    {
        num=-10;
    }
    else if(Task_Left_progress==2)
    {
        num=-10;
        ImageStatus.Road_type=Task_Left_Out;
    }
    else if(Task_Left_progress==3)
    {
        ImageStatus.Road_type=Straight;
    }
}
void renwu_right()
{
 printf("counterR=%d\n",counterR);
 printf("counterL=%d\n",counterL);
 cone_flag_5=0;
 int smallerIdx, largerIdx;
 if(number_num>=6)
 {
    number_num=0;
    cone_flag_5=1;
 }
printf("cone_num=%d\n",number_num);
printf("cone_flag_5=%d\n",cone_flag_5);
printf("ImageStatus.Road_type=%d\n",ImageStatus.Road_type);
printf("ImageStatus.OFFLine=%d\n",ImageStatus.OFFLine);
 if(((cone_flag_5==1&&counterL>=2&&counterR>=2)||
 (cone_flag_5==1&&counterL>=1&&counterR>=0))
 &&ImageStatus.OFFLine>=18
&&ImageStatus.Road_type!=Task_Right)
 {
  printf("Task_Left\n");
  ImageStatus.Road_type=Task_Right;
  //det_flag_1=1;//这里不知道要不要
 }
 maopao(y_1,number_num,y_new,x_1,x_new);//分别对左右的值进行新的冒泡排序
 printf("y_new=%d\n",y_new[0]);
 printf("y_new1=%d\n",y_new[1]);
 printf("y_new2=%d\n",y_new[2]);
 printf("y_new3=%d\n",y_new[3]);
if(ImageStatus.Road_type==Task_Right&&y_new[0]>=35)
{
  printf("hello\n");
  Task_Right_progress=1;
}
else if (ImageStatus.Road_type==Task_Right
&&Task_Right_progress==1
&&number_num>=3
&&y_new[0]>=30
&&y_new[1]>=20
&&y_new[2]>=20
)
{
   Task_Right_progress=2;
   printf("LLLL2\n");
}
else if(ImageStatus.Road_type==Task_Right_Out
// &&ImageStatus.left_WhiteLine_40<=2
// &&ImageStatus.right_WhiteLine_40<=2
&&left_circle_value>=0.99
&&right_circle_value>=0.99
&&Task_Right_progress==2)
{
printf("AOAO\n");
Task_Right_progress=3;
}
}
void renwu_right_handle(int Task_Right_progress)
{
    if(Task_Right_progress==1)
    {
        num=10;
    }
    else if(Task_Right_progress==2)
    {         
         printf("HOHO\n");                                               
        ImageStatus.Road_type=Task_Right_Out;
    }
    else if(Task_Right_progress==3)
    {

        ImageStatus.Road_type=Straight;        
    }
}




































```
