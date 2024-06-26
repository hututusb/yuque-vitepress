- 开放防火墙
```shell
#centos
yum install ufw

#ubuntu
apt-get install ufw

ufw enable  # 选择y
ufw allow 22
ufw allow 53
ufw allow 67
ufw allow 68
ufw allow 69
ufw allow 3306
ufw enable #选择y


```

- 拉取脚本
```shell
#https://github.com/HPUhushicheng/openvpn-install-script

git clone https://hub.gitmirror.com/https://github.com/HPUhushicheng/openvpn-install-script.git && cd openvpn-install-script && bash openvpn-install.sh
```
or
```
wget https://hub.gitmirror.com/https://github.com/HPUhushicheng/openvpn-install-script/blob/main/openvpn-install.sh && bash openvpn-install.sh
```

#### 运行之后，选择自己的IP地址
![image.png](../images/0ad655274fcd0f4a1bda4730deb4b558.png)
##### 然后到下一步直接回车
[![image.jpg](../images/f5b9b7a8e02188150fd737831ed67217.png)](https://s2.loli.net/2023/08/19/QBfOmTyX25J7Aow.png)
#### 填一下67
[![image.jpg](../images/2cf7e1f5aa8cbbe7f0e84896b357736c.png)](https://s2.loli.net/2023/08/19/RGyH8mpQbaM4qNY.png)
#### 下一步选择3 1.1.1.1
![image.png](../images/9231dfca3fc1d7c3e5907153c9f70efe.png)
#### 名字随便给一个，随后敲击两次回车，进入安装
[![image.jpg](../images/c3dae665aad5060f6b0883e72ed1e1f3.png)](https://s2.loli.net/2023/08/19/IDRTHBd6aO9Axpq.png)

![image.png](../images/cc49d317a4286ab427c431b39c734c40.png)

#### 把net.ovpn下载下来导入到openvpn，连上校园网，不要认证，就行了


