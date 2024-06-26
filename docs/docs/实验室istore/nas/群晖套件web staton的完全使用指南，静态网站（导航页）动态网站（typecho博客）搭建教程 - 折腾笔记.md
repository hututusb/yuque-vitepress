## 前言
本篇教程涉及到群晖web station套件的安装、设置、实例搭建并且通过两个例子静态网站【导航页】带交互网站例子【typecho博客】来了解web station的详细使用步骤，然后根据本文章举一反三，搭建自己喜欢的网站。
## 套件安装
1、 打开套件中心，搜索web station，点击安装套件。
![image.jpg](../../../images/b19d1ca9fd4a70cf803f013cc778a5b3.webp)
2、 打开套件勾选不要提示此信息，然后点击确定。
![image.jpg](../../../images/7a347b64f2a027a221697f0e354c7dfa.webp)
3、 这里需要先配置环境安装一些后端套件，点击管理列对应的跳转图标会自动跳转到套件中心的相关套件安装界面，安装成功，状态会变为正常，这里为了后续的使用方便，将套件全部安装，服务相关套件后续我们按需安装即可。
![image.jpg](../../../images/515e9e6b29a7c47a41235db0fc9b049b.webp)
## 静态导航页搭建
![image.jpg](../../../images/2ef4838bc32a7387953ff5d957f21ec9.webp)
我这里搭建的静态网页为一个导航页，相关资源来源于网络（链接：[https://pan.baidu.com/s/1-4DByrAWrNZ5OgThlItvGQ](https://pan.baidu.com/s/1-4DByrAWrNZ5OgThlItvGQ)
提取码：1234 ）
1、登录群辉，打开file station 在web目录下新建demo1目录用来存放静态网页的相关文件（web目录在安装web sttion套件后会自动生成）。
![image.jpg](../../../images/a7658e964a4d0ff35ac9b3d717d94aef.webp)
2、将下载好的导航页资源解压，里面共有11个静态页面网站，我用web00.tgz来演示。
![image.jpg](../../../images/d1ae1e180449599251aaeed1c0ab27ee.webp)
3、将web00压缩包拖动到nas新建立的demo1中。
![image.jpg](../../../images/ecc8cdfec0ca7a9c59277cfb7ef5c6f6.webp)
4、拖动上传完成以后，鼠标右击文件，解压缩至此目录。（本文中资源需要解压两次）
![image.jpg](../../../images/35898a3eaa677ff0473511fd4085943b.webp)
5、解压以后如下所示，保留解压出来的文件，压缩包已经无用，为了节省空间直接删除掉。
![image.jpg](../../../images/5092c684127cc55f55bb23a9fe7089a5.webp)
6、打开套件web station，点击网页服务门户→新增→创建服务门户
![image.jpg](../../../images/2fbb8cd91ed2fe8789cacbe6daafe1ee.webp)
7、弹出的页面选择虚拟主机。
![image.jpg](../../../images/e545ed228e74c033ee61f08d58cdf5bc.webp)
8、选择基于端口，勾选http并输入未使用过得端口，文档目录选择创建的demo1。
![image.jpg](../../../images/eb3a08cbdd517c23094531a92d9fc8d4.webp)
9、因为是静态页面其他设置选项保持默认，然后点击右下角的新增即可，弹出的赋权对话框点击确定。
![image.jpg](../../../images/282f7c626c529f71cda7cbbc5203eb3b.webp)
10、回到首页，可以看到自定义的门户下多了一个新增的虚拟主机。
![image.jpg](../../../images/1ac3e73fab10ede837cfc109ec0c79f0.webp)
## 静态导航页演示
1、打开浏览器输入http://192.168.31.3:8889/ （ip:端口），即可成功进入搭建的页面。
![image.jpg](../../../images/748f727110d1e5e0ea79629cadf7d08b.webp)
2、如果想用这个静态导航页的可以按照下面的配置设置自己的导航页，如果感觉静态的设置太麻烦可以参考我的动态导航页heimdall。
![image.jpg](../../../images/e4031225de46f2f163890fa435286780.webp)
3、静态导航页搭建还是很简单的，只需要把相应的静态网站上传到nas目录然后，进行简单的设置即可。
## 动态网站搭建
![image.jpg](../../../images/2e527158461b02ff7eea8c33fe4860d4.png)
typecho是一款特别好用的Markdown语法编辑软件，超级好用，而且可以把编辑的文章发布成博客，很人性化。
![image.jpg](../../../images/155b64405369e5f8bb65f5a7c2143df3.webp)
### typeco环境配置
1、登录群辉，打开套件中心，安装MariaDB10（数据库）、phpMyAdmin（数据库管理）两款套件。
![image.jpg](../../../images/451019cede3995ac32b556505e093418.webp)
2、安装MariaDB10的时候需要根据要求输入密码，可以用记事本保存下来后续会用到，端口保持默认即可。
![image.jpg](../../../images/bbfc206754df28f4e04c04b700a0dbbb.webp)
3、打开phpMyAdmin。
![image.jpg](../../../images/47a797f2044e3aa734b70cc03b851b46.webp)
4、输入步骤2配置的密码，用户名默认为root
![image.jpg](../../../images/f8cb3dc0401d1d2bfe7efe88427d833b.webp)
5、成功登录数据库以后，需要给typecho创建数据库，点击数据库，在新建数据库中输入typecho然后点击右边的创建。
![image.jpg](../../../images/16e80e0471db10a7f9dae04585b64d84.webp)
6、百度搜索Typecho ，进入官网下载正式版本，下载完成以后是一个名称为typecho.zip的压缩包。
![image.jpg](../../../images/92381dfa3fb603d0b67e09f3cb505a1f.webp)
7、登录群晖，打开file station，新建目录Typecho。
![image.jpg](../../../images/76e0d58562c43023c608a96225ed3e47.webp)
8、将步骤6下载文件拖动到Typecho目录，并解压。.
![image.jpg](../../../images/58645e71f5f38c986bec553e5cba8a49.webp)
9、右击建立的文件夹，属性→权限→新增Everyone权限，勾选 应用到这个文件夹、子文件夹及文件。
![image.jpg](../../../images/fb2bdb7f9ef1da861b25464ede81e457.webp)
10、还需要最后一步进行php的拓展设置，打开web station，脚本语言设置→选择php7.4，点击编辑→拓展名，勾选所有然后点击保存。
![image.jpg](../../../images/2240f768f794bbba49a6b0069a51198b.webp)
11、环境配置完成。
### typeco搭建
1、打开套件web station，点击网页服务门户→新增→创建服务门户
![image.jpg](../../../images/6249fa36f4dc45e7743c97ddf7de2b9d.webp)
2、弹出的页面选择虚拟主机。
![image.jpg](../../../images/c5531d1f35f9017b421703e832daa03c.webp)
3、选择基于端口，勾选http并输入未使用过得端口，文档目录选择创建的Typecho，如下图选择相应的后端服务器和PHP，其他设置保持默认，然后点击新增。（php版本为环境配置中的php7.4版本）
![image.jpg](../../../images/f1f42c6c8a9f4f7856e7fcd07e63894e.webp)
4、回到首页，可以看到自定义的门户下多了一个新增的虚拟主机。
![image.jpg](../../../images/4bb5b38e55cc188d5750c43124b0fc84.webp)
### typeco web端配置
1、打开浏览器输入ip：端口号，出现下面页面，点击我准备好了，开始下一步。
![image.jpg](../../../images/ab08eab8759be0c9c9a2792d18575f92.webp)
2、数据库适配器：Mysql原生函数适配器，数据用户名为 root，数据库密码为上面环境配置中设置的数据库密码，数据库名为typecho，其他保持默认 。
![image.jpg](../../../images/2d4edc3d137eb912702e1806cae7684e.webp)
3、设置博客后台的用户名、密码、邮箱，然后点击继续安装。
![image.jpg](../../../images/11a26cd990cd592e7ea123a0440d60ed.webp)
4、如果出现下面界面即表示安装成功。
![image.jpg](../../../images/feb53debe648ffec0e46cbef4ea9f5dd.webp)
## 动态博客Typecho演示
1、浏览器地址输入http://192.168.31.3:8886（ip：端口）进入博客。
![image.jpg](../../../images/24d337590baf37e2e9a749e1aa342ac5.webp)
2、在端口后面加上/admin进入博客后台管理界面。
![image.jpg](../../../images/09f7a03546ca6abfa443dbdb3746f970.webp)
3、输入用户名和密码成功登录后台，后台提供了文章撰写、系统设置、主题插件管理等。
![image.jpg](../../../images/d766a1e0f911f0f392c57008c36db52f.webp)
4、尝试写一篇文章测试一下。
![image.jpg](../../../images/70a35765598a7f7720581911796c024f.webp)
5、点击发布文章，然后回到博客首页已经成功显示。
![image.jpg](../../../images/1035cbbfc3f9d8f52acdeea222ba16c8.webp)
6、动态网站typecho博客搭建成功，此博客非常好用支持原生的markdown，这里不做介绍，大家有兴趣可以百度了解一下。
## 总结
总体来说利用群晖的web station套件可以很简单的搭建一个静态动态网站，利用群晖的反向代理功能，还可以轻松实现外网访问功能。还是很不错的，疫情在家隔离没事写写教程挺好。今天爆肝了3篇，希望大家多多支持关注，咱们下面文章再见。

> 来自: [群晖套件web staton的完全使用指南，静态网站（导航页）动态网站（typecho博客）搭建教程 - 折腾笔记](http://nas.zwbcc.cn:8090/archives/webstaton)

