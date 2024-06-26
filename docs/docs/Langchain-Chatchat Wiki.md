![image.jpg](../images/f1d5da5df3ba0ab27d58d60bd7c9e1ed.png)

> 来自: [Home · chatchat-space/Langchain-Chatchat Wiki](https://github.com/chatchat-space/Langchain-Chatchat/wiki/)

> 欢迎来到 Langchain‐Chatchat 的 Wiki , 在这里开启 Langchain 与大模型的邂逅!

## 
## 项目简介
📃 **LangChain-Chatchat** (原 Langchain-ChatGLM): 基于 Langchain 与 ChatGLM 等大语言模型的本地知识库问答应用实现。
🤖️ 一种利用 [langchain](https://github.com/hwchase17/langchain) 思想实现的基于本地知识库的问答应用，目标期望建立一套对中文场景与开源模型支持友好、可离线运行的知识库问答解决方案。
💡 受 [GanymedeNil](https://github.com/GanymedeNil) 的项目 [document.ai](https://github.com/GanymedeNil/document.ai) 和 [AlexZhangji](https://github.com/AlexZhangji) 创建的 [ChatGLM-6B Pull Request](https://github.com/THUDM/ChatGLM-6B/pull/216) 启发，建立了全流程可使用开源模型实现的本地知识库问答应用。本项目的最新版本中通过使用 [FastChat](https://github.com/lm-sys/FastChat) 接入 Vicuna, Alpaca, LLaMA, Koala, RWKV 等模型，依托于 [langchain](https://github.com/langchain-ai/langchain) 框架支持通过基于 [FastAPI](https://github.com/tiangolo/fastapi) 提供的 API 调用服务，或使用基于 [Streamlit](https://github.com/streamlit/streamlit) 的 WebUI 进行操作。
✅ 依托于本项目支持的开源 LLM 与 Embedding 模型，本项目可实现全部使用**开源**模型**离线私有部署**。与此同时，本项目也支持 OpenAI GPT API 的调用，并将在后续持续扩充对各类模型及模型 API 的接入。
⛓️ 本项目实现原理如下图所示，过程包括加载文件 -> 读取文本 -> 文本分割 -> 文本向量化 -> 问句向量化 -> 在文本向量中匹配出与问句向量最相似的 `top k`个 -> 匹配出的文本作为上下文和问题一起添加到 `prompt`中 -> 提交给 `LLM`生成回答。
## 算法流程
大家可以前往Bilibili平台查看原理介绍视频：
📺 [原理介绍视频](https://www.bilibili.com/video/BV13M4y1e7cN/?share_source=copy_web&vd_source=e6c5aafe684f30fbe41925d61ca6d514)
开发组也为大家绘制了一张实现原理图，效果如下：
![image.jpg](../images/4d49dbb3324dc23ae778f8256231e720.png)
从文档处理角度来看，实现流程如下：
![image.jpg](../images/8caf9b3ef0805a4f6cab988101934179.png)
## 技术路线图（截止0.2.10）

-  Langchain 应用 
   -  本地数据接入 
      -  接入非结构化文档 
         -  .txt, .rtf, .epub, .srt
         -  .eml, .msg
         -  .html, .xml, .toml, .mhtml
         -  .json, .jsonl
         -  .md, .rst
         -  .docx, .doc, .pptx, .ppt, .odt
         -  .enex
         -  .pdf
         -  .jpg, .jpeg, .png, .bmp
         -  .py, .ipynb
      -  结构化数据接入 
         -  .csv, .tsv
         -  .xlsx, .xls, .xlsd
      -  分词及召回 
         -  接入不同类型 TextSplitter
         -  优化依据中文标点符号设计的 ChineseTextSplitter
   -  搜索引擎接入 
      -  Bing 搜索
      -  DuckDuckGo 搜索
      -  Metaphor 搜索
   -  Agent 实现 
      -  基础React形式的Agent实现，包括调用计算器等
      -  Langchain 自带的Agent实现和调用
      -  智能调用不同的数据库和联网知识
-  LLM 模型接入 
   -  支持通过调用 [FastChat](https://github.com/lm-sys/fastchat) api 调用 llm
   -  支持 ChatGLM API 等 LLM API 的接入
   -  支持 Langchain 框架支持的LLM API 接入
-  Embedding 模型接入 
   -  支持调用 HuggingFace 中各开源 Emebdding 模型
   -  支持 OpenAI Embedding API 等 Embedding API 的接入
   -  支持 智谱AI、百度千帆、千问、MiniMax 等在线 Embedding API 的接入
-  基于 FastAPI 的 API 方式调用
-  Web UI 
   -  基于 Streamlit 的 Web UI
## [Home](https://github.com/chatchat-space/Langchain-Chatchat/wiki)
## [支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)

- [LLM 模型支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8#llm-%E6%A8%A1%E5%9E%8B%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
- [Embedding 模型支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8#embedding-%E6%A8%A1%E5%9E%8B%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
- [分词器支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8#%E5%88%86%E8%AF%8D%E5%99%A8%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
- [向量数据库支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8#%E5%90%91%E9%87%8F%E6%95%B0%E6%8D%AE%E5%BA%93%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
- [工具支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8#%E5%B7%A5%E5%85%B7%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
## [开发环境部署](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2)
### 前期准备

- [软件要求](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E8%BD%AF%E4%BB%B6%E8%A6%81%E6%B1%82)
- [硬件要求](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E7%A1%AC%E4%BB%B6%E8%A6%81%E6%B1%82)
- [VPN](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#vpn)
### 部署代码

- [Docker 部署](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#docker-%E9%83%A8%E7%BD%B2)
- [最轻模式部署方案](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E6%9C%80%E8%BD%BB%E6%A8%A1%E5%BC%8F%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2%E6%96%B9%E6%A1%88)
- [常规模式本地部署方案](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E5%B8%B8%E8%A7%84%E6%A8%A1%E5%BC%8F%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2%E6%96%B9%E6%A1%88)
   - [环境安装](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2%E7%8E%AF%E5%A2%83%E5%AE%89%E8%A3%85)
   - [模型下载](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E6%A8%A1%E5%9E%8B%E4%B8%8B%E8%BD%BD)
   - [初始化知识库](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E5%88%9D%E5%A7%8B%E5%8C%96%E7%9F%A5%E8%AF%86%E5%BA%93)
   - [一键启动](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E4%B8%80%E9%94%AE%E5%90%AF%E5%8A%A8)
   - [多卡加载](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2#%E5%A4%9A%E5%8D%A1%E5%8A%A0%E8%BD%BD)
## [参数配置](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE)

- [基础配置项 basic_config.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E5%9F%BA%E7%A1%80%E9%85%8D%E7%BD%AE%E9%A1%B9-basic_configpy)
- [模型配置项 model_config.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E6%A8%A1%E5%9E%8B%E9%85%8D%E7%BD%AE%E9%A1%B9-model_configpy)
- [提示词配置项 prompt_config.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E6%8F%90%E7%A4%BA%E8%AF%8D%E9%85%8D%E7%BD%AE%E9%A1%B9-prompt_configpy)
- [数据库配置 kb_config.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E6%95%B0%E6%8D%AE%E5%BA%93%E9%85%8D%E7%BD%AE-kb_configpy)
- [服务和端口配置项 server_config.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E6%9C%8D%E5%8A%A1%E5%92%8C%E7%AB%AF%E5%8F%A3%E9%85%8D%E7%BD%AE%E9%A1%B9-server_configpy)
- [覆盖配置文件 或者配置 startup.py](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE#%E8%A6%86%E7%9B%96%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6-%E6%88%96%E8%80%85%E9%85%8D%E7%BD%AE-startuppy)
## [自定义](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89)

- [使用自定义的分词器](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E5%88%86%E8%AF%8D%E5%99%A8)
- [使用自定义的 Agent 工具](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84-agent-%E5%B7%A5%E5%85%B7)
- [使用自定义的微调模型](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E5%BE%AE%E8%B0%83%E6%A8%A1%E5%9E%8B)
- [使用自定义的嵌入模型](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E5%B5%8C%E5%85%A5%E6%A8%A1%E5%9E%8B)
- [日志功能](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E6%97%A5%E5%BF%97%E5%8A%9F%E8%83%BD)
## [最佳实践](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5)

- [推荐的模型组合](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E6%8E%A8%E8%8D%90%E7%9A%84%E6%A8%A1%E5%9E%8B%E7%BB%84%E5%90%88)
- [微调模型加载实操](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E5%BE%AE%E8%B0%83%E6%A8%A1%E5%9E%8B%E5%8A%A0%E8%BD%BD%E5%AE%9E%E6%93%8D)
- [预处理知识库文件](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E9%A2%84%E5%A4%84%E7%90%86%E7%9F%A5%E8%AF%86%E5%BA%93%E6%96%87%E4%BB%B6)
- [自定义的关键词调整Embedding模型](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E5%85%B3%E9%94%AE%E8%AF%8D%E8%B0%83%E6%95%B4embedding%E6%A8%A1%E5%9E%8B)
- [实际使用效果](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5#%E5%AE%9E%E9%99%85%E4%BD%BF%E7%94%A8%E6%95%88%E6%9E%9C)
## [做出贡献](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%81%9A%E5%87%BA%E8%B4%A1%E7%8C%AE)

- [Issue 规范](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%81%9A%E5%87%BA%E8%B4%A1%E7%8C%AE#issue-%E8%A7%84%E8%8C%83)
- [PR 规范](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%81%9A%E5%87%BA%E8%B4%A1%E7%8C%AE#pr-%E8%A7%84%E8%8C%83)
## [合作伙伴](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%90%88%E4%BD%9C%E4%BC%99%E4%BC%B4)
## [常见问题](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
##### Clone this wiki locally
### Toggle tagle of contentsPages 9

- [ ] 

- [Home](https://github.com/chatchat-space/Langchain-Chatchat/wiki)
   - [项目简介](https://github.com/chatchat-space/Langchain-Chatchat/wiki#%E9%A1%B9%E7%9B%AE%E7%AE%80%E4%BB%8B)
   - [算法流程](https://github.com/chatchat-space/Langchain-Chatchat/wiki#%E7%AE%97%E6%B3%95%E6%B5%81%E7%A8%8B)
   - [技术路线图（截止0.2.10）](https://github.com/chatchat-space/Langchain-Chatchat/wiki#%E6%8A%80%E6%9C%AF%E8%B7%AF%E7%BA%BF%E5%9B%BE%E6%88%AA%E6%AD%A20210)
- [做出贡献](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%81%9A%E5%87%BA%E8%B4%A1%E7%8C%AE)
- [参数配置](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE)
- [合作伙伴](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%90%88%E4%BD%9C%E4%BC%99%E4%BC%B4)
- [常见问题](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
- [开发环境部署](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2)
- [支持列表](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)
- [最佳实践](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5)
- [自定义](https://github.com/chatchat-space/Langchain-Chatchat/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89)

**导航栏，一切从这里出发**
## 

- [ ] 

