## 解决方案
在网上终于查到解决方案，分享给大家。 在 Whisper 的[讨论区](https://github.com/openai/whisper/discussions/277)中，[@jongwook](https://github.com/jongwook) 提出：

- 使用 `--initial_prompt` 参数，用简体中文输入 `"以下是普通话的句子。"` 就能生成简体中文字幕。（补充：whisper.cpp 用户可以尝试使用`--prompt`参数）
- 以此类推，用繁体中文输入 `"以下是普通話的句子。"` 就能得到繁体字幕。

`$ whisper --language Chinese --model large audio.wav
[00:00.000 --> 00:08.000] 如果他们使用航空的方式运输货物在某些航线上可能要花几天的时间才能卸货和通关
$ whisper --language Chinese --model large audio.wav --initial_prompt "以下是普通話的句子。" # traditional
[00:00.000 --> 00:08.000] 如果他們使用航空的方式運輸貨物,在某些航線上可能要花幾天的時間才能卸貨和通關。
$ whisper --language Chinese --model large audio.wav --initial_prompt "以下是普通话的句子。" # simplified
[00:00.000 --> 00:08.000] 如果他们使用航空的方式运输货物,在某些航线上可能要花几天的时间才能卸货和通关。`
另外，有关 `--initial_prompt` 参数，可在 [openai的文档](https://platform.openai.com/docs/guides/speech-to-text/prompting) 查看更多。其中也提到 "有些语言可以用不同的方式书写，比如简体或繁体中文。模型可能默认不会使用你想要的转录写作风格。你可以通过使用你偏好的写作风格的提示来改善这一点。"。

> 来自: [指定 Whisper 输出为简体中文 - Wulu's Blog](https://wulu.zone/posts/whisper-cn)

