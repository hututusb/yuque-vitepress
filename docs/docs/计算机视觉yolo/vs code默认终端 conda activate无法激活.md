输入conda activate base之后没有任何反应，在网上查阅了许多资料＋不断更正自己的搜索关键词后终于发现了一个比较关键的帖子。
[解决使用anaconda VSCODE无法import cv2问题](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2FFlandre_Remilia%2Farticle%2Fdetails%2F108320753%3Futm_medium%3Ddistribute.pc_aggpage_search_result.none-task-blog-2%7Eaggregatepage%7Efirst_rank_v2%7Erank_aggregation-15-108320753.pc_agg_rank_aggregation%26utm_term%3DVSCode%2B%25E4%25B8%25AD%25E5%2588%2587%25E6%258D%25A2%25E9%25BB%2598%25E8%25AE%25A4%25E7%25BB%2588%25E7%25AB%25AF%26spm%3D1000.2123.3001.4430)
> 这个问题是由于anaconda 多环境导致的 ,默认VSCode里的默认终端是powershell,但是powershell不能执行conda activate,所以Python无法切换到需要的环境。

**解决办法：**
输入：Ctrl+Shift+P
输入：terminal:select default profile
将默认的PS改为cmd


> 来自: [vs code更改默认终端 conda activate激活 - 简书](https://www.jianshu.com/p/a89001470be0)

