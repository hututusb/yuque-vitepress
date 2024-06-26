【部署微调ChatGlm3-6B大模型【小白0到1】】 [https://www.bilibili.com/video/BV1Cx4y1m7Gd/?p=5&share_source=copy_web&vd_source=759145a85d9dc689722e9d34a3a9612e](https://www.bilibili.com/video/BV1Cx4y1m7Gd/?p=5&share_source=copy_web&vd_source=759145a85d9dc689722e9d34a3a9612e)

[PAI][https://pai.console.aliyun.com/&spm=5176.28008736&scm=20140722.M_10000005532.P_121.MO_2230-ID_10000005532-MID_10000005532-CID_0-ST_8726-V_1?regionId=cn-shanghai&workspaceId=450884#/notebook-buy/buy](https://pai.console.aliyun.com/&spm=5176.28008736&scm=20140722.M_10000005532.P_121.MO_2230-ID_10000005532-MID_10000005532-CID_0-ST_8726-V_1?regionId=cn-shanghai&workspaceId=450884#/notebook-buy/buy)

【csdn文字版】[https://blog.csdn.net/weixin_44480960/article/details/137092717](https://blog.csdn.net/weixin_44480960/article/details/137092717)
【阿里云pai人工智能训练教程】[https://help.aliyun.com/document_detail/2261126.html?spm=5176.28008736.websiteFastBuy.4.6b233e4dhllhW6](https://help.aliyun.com/document_detail/2261126.html?spm=5176.28008736.websiteFastBuy.4.6b233e4dhllhW6)
【chatglm3官方文档搭建问答机器人】[https://zhipu-ai.feishu.cn/wiki/WvQbwIJ9tiPAxGk8ywDck6yfnof](https://zhipu-ai.feishu.cn/wiki/WvQbwIJ9tiPAxGk8ywDck6yfnof)

【interLLM教程GitHub作业】[https://github.com/InternLM/Tutorial/tree/camp2](https://github.com/InternLM/Tutorial/tree/camp2)

【进阶版】[https://www.bilibili.com/video/BV1UH4y1W7PH/?spm_id_from=333.788.recommend_more_video.0&vd_source=cbdbd8d9f59c48c14f18e0c14f857854](https://www.bilibili.com/video/BV1UH4y1W7PH/?spm_id_from=333.788.recommend_more_video.0&vd_source=cbdbd8d9f59c48c14f18e0c14f857854)

【可看】【chatglm3模型本地部署及微调】 [https://www.bilibili.com/video/BV1YD42177VE/?share_source=copy_web&vd_source=759145a85d9dc689722e9d34a3a9612e](https://www.bilibili.com/video/BV1YD42177VE/?share_source=copy_web&vd_source=759145a85d9dc689722e9d34a3a9612e)


【chatglm3官网文档】[https://zhipu-ai.feishu.cn/wiki/HIj5wVxGqiUg3rkbQ1OcVEe5n9g](https://zhipu-ai.feishu.cn/wiki/HIj5wVxGqiUg3rkbQ1OcVEe5n9g)

【chatglm3 github地址】[https://github.com/THUDM/ChatGLM3](https://github.com/THUDM/ChatGLM3)

【hugging face镜像站】[https://hf-mirror.com/THUDM/chatglm3-6b](https://hf-mirror.com/THUDM/chatglm3-6b)  速度快得很

【魔搭社区】[https://www.modelscope.cn/models/ZhipuAI/chatglm3-6b/files](https://www.modelscope.cn/models/ZhipuAI/chatglm3-6b/files)  也快

【chaglm3结合langchain-chatchat文档】[https://blog.csdn.net/m0_52695557/article/details/134907862](https://blog.csdn.net/m0_52695557/article/details/134907862)

【有道Qanything知识库】[https://wisemodel.cn/models/Netease_Youdao/qanything/file](https://wisemodel.cn/models/Netease_Youdao/qanything/file)

【chatglm3外挂知识库保姆级教程（langchain+chatglm）-附详细完整代码可实现】
[https://zhuanlan.zhihu.com/p/680692103](https://zhuanlan.zhihu.com/p/680692103)

【ai开源项目，一般般】[https://ai-big-bang.feishu.cn/wiki/RpSWwUUJqi79CDk5qIucdVj5n7e](https://ai-big-bang.feishu.cn/wiki/RpSWwUUJqi79CDk5qIucdVj5n7e)

选腾讯云服务器，aliyun，智谱任务和微调也可做

【企业级ai】[https://dataelem.feishu.cn/wiki/ZxW6wZyAJicX4WkG0NqcWsbynde](https://dataelem.feishu.cn/wiki/ZxW6wZyAJicX4WkG0NqcWsbynde)

[https://github.com/logspace-ai/langflow](https://github.com/logspace-ai/langflow)

[https://github.com/microsoft/promptflow](https://github.com/microsoft/promptflow)

报错终于会处理了，我直接把chatglm3-6b的模型路径变成默认的不就行了，意思就是/THUDM/chatglm3-6b，代码如下
```shell
mkdir THUDM
cd /root/THUDM

apt update
apt install git-lfs

#从魔搭社区拉取chatglm3-6b模型文件
git clone https://www.modelscope.cn/ZhipuAI/chatglm3-6b.git

#再从GitHub上拉取页面框架代码
mkdir webcodes
cd /root/webcodes
# 下载chatglm3-6b web_demo项目
git clone https://github.com/THUDM/ChatGLM3.git

# 安装依赖
pip install -r requirements.txt

```
查看conda虚拟环境
```shell
 conda info --envs
```
