打开终端
```
conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/main/

conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/free/

conda config --set show_channel_urls yes
```
```
conda config --show
```
速度慢可切换其他源
```
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
阿里云 http://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣(douban) http://pypi.douban.com/simple/
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
```
```
pip install <name> -i 源地址
# pip install pyside6==6.4.2 -i https://pypi.tuna.tsinghua.edu.cn/simple

```
