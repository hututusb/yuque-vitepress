## 准备
- 群晖NAS
- 华硕路由器，前提配置好 AiMesh 和 网络环境
- 记住自己的路由器的 IP和网关 。
## 软件

1. 下载最新的 [iStoreOS 固件包](https://fw.koolcenter.com/iStoreOS/x86_64/) (以 21.02.3-2022102715-x86-64 为例）
2. 群晖安装最新的 [Virtual Machine Manager](https://www.synology.cn/zh-cn/dsm/feature/virtual_machine_manager)（以 DSM7.1.1 和 VM2.6.1-12139 演示，以下简称VM）
3. [iStoreOS文档](https://doc.linkease.com/zh/guide/istoreos/install.html)
4. [iStoreOS 插件包](https://github.com/AUK9527/Are-u-ok)（x86_64平台）
## 教程：
### 1、安装镜像
1、将下载好的 iStoreOS 固件 21.02.3-2022102715-x86-64.img.gz 文件解压得到 .img 固件镜像。
![image.jpg](../../../images/c7fa291027d7f2b57c9bd1130837fda1.jpeg)
.img格式的固件
2、登入群晖后台 > 打开 VM > 检查 网络 和 储存空间 已 创建完毕。
> 此处不做解释

![image.jpg](../../../images/7c4d5d3d6f9e935155d3e8471f56cfde.jpeg)
检查网络和空间是否创建
![image.jpg](../../../images/a5b62a0f1a7aa59b2f8016a0ed0b018d.jpeg)
3、添加解压好的格式为 .img 的 iStoreOS固件包
![image.jpg](../../../images/02c3005560302086a303498aa3c29f19.jpeg)
添加镜像
![image.jpg](../../../images/d842741c8052cd886de785f8d206d051.jpeg)
选择储存空间
状态良好表示已创建完成
![image.jpg](../../../images/cfe62f7f73e67216ecc6f2a5451961a6.jpeg)
4、添加 iStoreOS
![image.jpg](../../../images/3f0ea9dfdaf4e1e12a571c4a5fc08bb2.jpeg)
导入固件
![image.jpg](../../../images/d4f32d3203ca5158e7ba0099c12e4402.jpeg)
选择硬盘导入
![image.jpg](../../../images/4031033e1e5318b342bd04dfc2d52591.jpeg)
输入参数
![image.jpg](../../../images/e6e612ecda910afb53d2be2f8ab0e959.jpeg)
启动CPU兼容模式
![image.jpg](../../../images/6444482d58084b00d92a7cdd3762c691.jpeg)
储存空间最小10G
![image.jpg](../../../images/e5990aa074da3d5a496faeb2f903550f.jpeg)
选择你设置好的网络
![image.jpg](../../../images/ac8de30f75010f8738a682981c34614e.jpeg)
其他设置：默认参数
![image.jpg](../../../images/ff18696fae1f86e964ce7e8529659494.jpeg)
按需求选择
![image.jpg](../../../images/63f1aa9b621c316448df0233c8a51ee9.jpeg)
完成设置
### 2、配置 iStoreOS
1、连接 iStoreOS
![image.jpg](../../../images/3c6f27eef8da811fb1b58335d19d69ab.jpeg)
![image.jpg](../../../images/fe259ea1e1ca61f494f6c14bbb1c9218.jpeg)
稍等片刻后，点回车
![image.jpg](../../../images/ca7e1ad6ea96a52e00b4a0a09e311db9.jpeg)
安装成功
6、为 iStoreOS 设置相同 网关 下的 IP地址
![image.jpg](../../../images/6813d2503a1ab1a794a26997d64fd781.jpeg)
![image.jpg](../../../images/55d82430df8e2ad0bbb3eaa51992c17b.jpeg)
```
option gateway '192.168.50.1'
```
修改为（按需求修改）
```
option gateway '192.168.50.2'
```
修改后单击 esc > 输入 :wq >单击回车退出
继续输入 reboot ，点击 回车 重启
### 3、旁路由模式
7、进入iStoreOS，浏览器打开 192.168.50.2，初始密码：password，设定旁路由模式。
接下来可以观看视频教程（文图以方法二设置）
![image.jpg](../../../images/374cacfdee91cab601b5d1c6126b7597.jpeg)
选择旁路由
![image.jpg](../../../images/659cf8bbc9532472f9b83fa350047224.jpeg)
设置参数，保存参数
8、进入华硕路由器后台：192.168.50.1 或 [http://router.asus.com](http://router.asus.com/)
进入DHCP： 内部网络（LAN） > [DHCP 服务器](http://192.168.50.1/Advanced_DHCP_Content.asp)
![image.jpg](../../../images/9bfb42d2e0db7c0b093df812a80fc9a1.jpeg)
按图片参数设置并点击 应用本页面设置，即可。

> 来自: [群晖 安装 iStoreOS 作为 旁路由，华硕路由器,OpenWRT - 知乎](https://zhuanlan.zhihu.com/p/589065349)

