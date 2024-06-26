```
#没安装过lfs的安装一下，上传大文件用
git lfs install

# 本地克隆仓库，这种oauth2方法我在internstudio和本地电脑都可以，但是在autodl不行
git clone https://oauth2:your_git_token@www.modelscope.cn/user/my_test_model.git

#cd 进入my_test_model文件夹
git add -A .
git commit -m "commit message"
git push 


```
```
愤怒！！ git老是抽风，今天来总结一下,qisi老子了
下面是在autodl的遇到的,解决如下
艹艹艹，
先看官网怎么说的

```
![image.png](../images/13aec702855bfbfc19b2e0eef3a9b102.png)
关键是oauth不起作用好不
![image.png](../images/50cef441443da6988f65a68bbcbf0791.png)
```
解决如下

我用的是方法2
拿ChatGLM3-LLaMA-Factory-Fine-tuning举例
#git clone https://user@www.modelscope.cn/user/my_test_model.git
# Password for 'http://user@modelscope.cn':
# input git token

看好了，修改user和仓库名
git clone https://comefly@www.modelscope.cn/comefly/ChatGLM3-LLaMA-Factory-Fine-tuning.git

然后copy git token 粘贴进去

然后就是
git add -A .
git commit -m "first push"
然后就提示 who are you ，要你输入邮箱和用户名

git config --global user.email "hscyyds90@163.com"
git config --global user.name "comefly"

 然后git push 就完事了
```
![image.png](../images/5bff8b8cebe63290ad1b15430c6b53b0.png)


