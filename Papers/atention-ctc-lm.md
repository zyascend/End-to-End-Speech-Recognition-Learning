[![BFrih6.md.png](https://s1.ax1x.com/2020/10/22/BFrih6.md.png)](https://imgchr.com/i/BFrih6)

**题目：**

**Improving Transformer-based End-to-End Speech Recognition with Connectionist Temporal Classification and Language Model Integration**

**链接：**

http://www.isca-speech.org/archive/Interspeech_2019/abstracts/1938.html

**标签：**

*Speech Recognition, Transformer, CTC, LM*

**Contributions：**

- 将CTC，LM与Transformer在decoding阶段融合，实现更好的识别表现
- 通过实验调查了融合效果在大数据集上的表现
- 在开源项目ESPnet上实现基于Transformer的ASR toolkit

**亮点与启发：**

文章指出，Transformer应用于ASR主要有两个问题：

1. 相比于 RNN-based ASR，收敛速度较慢。
2. 不易与语言模型结合

而通过与CTC在解码阶段的结合，可以加速Transformer的收敛速度。

> CTC can encourage monotonic alignment between the speech and transcription. Therefore, the attention at an early epoch with CTC appears more monotonic than that without CTC

CTC的自动对齐功能使语音片段与文字序列保持单调一致的对齐，在训练的初始阶段使attention的更加单调集中

[![BF6k90.png](https://s1.ax1x.com/2020/10/22/BF6k90.png)](https://imgchr.com/i/BF6k90)

提出结合后的损失函数：

[![BFs4ds.png](https://s1.ax1x.com/2020/10/22/BFs4ds.png)](https://imgchr.com/i/BFs4ds)

[![BFsTJ0.png](https://s1.ax1x.com/2020/10/22/BFsTJ0.png)](https://imgchr.com/i/BFsTJ0)

**实验结果：**

[![BF6CAs.png](https://s1.ax1x.com/2020/10/22/BF6CAs.png)](https://imgchr.com/i/BF6CAs)









