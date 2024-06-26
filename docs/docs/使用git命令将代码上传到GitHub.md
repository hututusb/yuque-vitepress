

(7)创建一个仓库
![image.jpg](../images/fd7a1c419f35621fecad474c85c7e1b2.png)
(8)填写仓库相关信息,有:仓库名,仓库描述,
选择仓库公开还是私有…大家自己选择,(只有仓库名是必填的)
填好仓库信息之后,点击创建!
![image.jpg](../images/3465a021b31430f620597cf6689e78f3.png)
(9)点击创建之后,会显示这个页面,表明远程仓库创建成功!!!
![image.jpg](../images/f5c970ff8bdda58f33064b31027374d1.png)
(1)找到gitbash.exe.(可以在开始菜单里面寻找)
![image.jpg](../images/0619f0b785b07bf1dfc01533286b9b7c.png)
(2)因为Git是分布式版本控制系统，所以需要填写用户名和邮箱作为一个标识，用户和邮箱为你github注册的账号和邮箱
补充说明:git config --global 参数，有了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然你也可以对某个仓库指定的不同的用户名和邮箱。
```
git config --global user.name "用户名"

```
```
git config --global user.email "邮箱"

```
![image.jpg](../images/d174e48dac6423bdea8c9afd7eb86dfc.png)
(1)生成ssh key
打开gitbash,输入
```
ssh-keygen -t rsa -C "邮箱"

```
第一个提示输入是路径确认，直接按回车存默认路径即可
第二个提示输入直接回车键，这里我们不使用密码进行登录, 用密码太麻烦;
第三个提示输入直接回车键
![image.jpg](../images/ea765839769f7f5d2890d53e0d3c8765.png)
找到这个默认路径
![image.jpg](../images/86a7c00034ee0af40733e5e91166f164.png)
用记事本打开id_rsa.pub,里面的便是ssh key公钥
![image.jpg](../images/8c32b46f07ddd3859464adca4e84c984.png)
(1)点击头像旁边的三角,然后点击setting
![image.jpg](../images/8407664c54ed1cdd2a1478794cf4f76a.png)
(2)点击这里
![image.jpg](../images/cc48afe67e2d6d94f0026cf70a02acec.png)
(3)点击New SSH KEY
![image.jpg](../images/e162edf45d41c6dcde63ab43aef55c15.png)
(4)
![image.jpg](../images/4edf9cecabc2b1567fcd3837ab123743.png)
(5)输入GitHub的密码进行确认
![image.jpg](../images/e8889bda99ad8a0a5ab378f22809d560.png)
(6)这样ssh key便在GitHub上配置成功
![image.jpg](../images/6da0c5a5d7222d9519ae4af2b1cb8073.png)
(7)这意味着GitHub和本地电脑便建立起了联系!
#### 整体过程:
![image.jpg](../images/4e4eed181489e4e397684fe3b317855e.png)
(1)命令一:
在要上传的文件夹下,右键Git Bash Here
![image.jpg](../images/07e495756d8f091383dd1ace225bc13b.png)
然后输入:
```
git init

```
这个git命令是将当前文件夹变成用git管理的文件夹
![image.jpg](../images/1ca513246d7eb3dce2131a765c33c79b.png)
执行成功之后,勾选这个
![image.jpg](../images/20b8ba87ac0d01d99667401a9d62e8ea.png)
会看到该文件夹下多了.git的文件
![image.jpg](../images/8544289e4b1f1c6ab4dd53a867576d01.png)
(2)命令二:
```
git add .

```
这个命令是将当前文件夹下的所有文件添加到缓存中
![image.jpg](../images/8080df0efbed47441680bb71c70c18f2.png)
(3)命令三:
```
git commit -m "提交信息"

```
这个命令是将缓存中的内容提交到本地仓库
![image.jpg](../images/62bfeb5bac84460e7be75e6c55093f42.png)
(4)命令四:
找到GitHub上新建立完仓库之后的这句命令
![image.jpg](../images/130c6ca3867c16628c9c4d5f38dd2ff7.png)
```
git remote add origin https://github.com/ZhangXiangyu23/smallpaper.git

```
复制到git bash中
这句命令是建立本地仓库和GitHub远程仓库的联系!!!
![image.jpg](../images/7a09637b785ba1a3f81f9abb7e91e834.png)
(5)命令五:
```
git push -u origin main

```
这句命令是将本地仓库中的代码推送到GitHub远程仓库,第一次推送的话应该加-u参数.之后推送就不用加-u参数了
![image.jpg](../images/7fd5cad77fdfa33bab453e00d024205c.png)
![image.jpg](../images/0a446351926555b964fe01bcc676cd2d.png)
看看GitHub上的远程仓库,远程仓库上也显示上传成功了!!!
![image.jpg](../images/5f746f2964f3053378f55348d7d10a84.png)

> 来自: [史上最详细教程------使用git命令将代码上传到GitHub(一看就会)_github_时代&信念-开放原子开发者工作坊](https://openatomworkshop.csdn.net/6604e0ff1a836825ed7a28b9.html?dp_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6Nzk5MDk0LCJleHAiOjE3MTI4NDg5NzksImlhdCI6MTcxMjI0NDE3OSwidXNlcm5hbWUiOiJ3ZWl4aW5fNzQzMTM1OTIifQ._b7iCLzSYCr0y6fWXCGxraJJ5ERl06UBZ-_rbZk1t_o)

