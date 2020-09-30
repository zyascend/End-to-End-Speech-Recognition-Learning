



# [Google]Towards End-to-End Speech Recognition

Google关于端到端语音识别的综述

ISCSLP Tutorial 4 Nov. 26, 2018

原文链接: http://iscslp2018.org/images/T4_Towards%20end-to-end%20speech%20recognition.pdf

## 什么是 End-to-End ASR

> A system which directly maps a sequence of input acoustic features into a sequence of graphemes or words.
>
> A system which is trained to optimize criteria that are related to the final evaluation metric that we are interested in (typically, word error rate).

即：**直接**把一串输入的语音转换成一串字词

**直接**意味着它极大地简化了传统语音识别系统的结构。

[![0ZIFde.md.png](https://s1.ax1x.com/2020/09/29/0ZIFde.md.png)](https://imgchr.com/i/0ZIFde)

## End-to-End ASR的发展历程

### CTC (Connectionist Temporal Classification)

- 不需要帧级别对齐就可以训练声学模型
- 基于CD音素的CTC模型在传统ASR方面取得了表现优异，但在词级别落后于传统ASR

CTC使用空白符来构造单个标签的所有可能排布，并将这些排布的概率累加，作为此标签的最终概率。

而在计算单个排布的概率时需要使用到**前向-后向算法**（forward-backward algorithm [Rabiner, 1989]）

CTC的不足：

- 为了提高效率，CTC假设每帧的解码输出都是互相独立的——**which is not**
- 需要搭配一个额外的语言模型来题设准确率——直接使用贪心解码效果不好（ps：这还算端到端吗）

### Attention-based Models

始于机器翻译领域

相关论文：

Listen，Attend and Spell [Chan et al., 2015]

Attention-Based Models for Speech Recognition [Chorowski et al., 2015]

#### 模型结构

[![0ZIYzq.md.png](https://s1.ax1x.com/2020/09/29/0ZIYzq.md.png)](https://imgchr.com/i/0ZIYzq)

- Encoder: 类似于声学模型:, 将输入的语音转换成更高层次的表示
- Attention：计算解码器和编码器每一帧之间的相似度分数，并将计算结果输给decoder
- Decoder：类似与语言模型，接受Attention的输出与前一个时间步的输出，计算当前时间步token的概率分布

### Online Models——RNN-T, NT, MoChAs

经过实验表明，Attention-based mode相比与其他模型表现地最好，但是它并不适用于流式语音识别

> As typically used in sequence-to-sequence transduction tasks, Transformer-based models attend over encoder features using decoder features, implying that the decoding has to be done in a label-synchronous way, thereby posing a challenge for streaming speech recognition applications.
>
> arXiv:2002.02562 [cs, eess]

另一方面，[Battenberg et al., 2017] 的实验证明，没有使用LM的RNN-T要比使用了LM的CTC表现更优。

#### 模型结合

- CTC与Attention-based models结合：[Kim et al., 2017]  https://arxiv.org/abs/1609.06773
- RNN-T可以与具有以下能力的Attention-based models结合
  - 以声学的角度调节语言模型 [Prabhavalkar et al., 2017] https://www.isca-speech.org/archive/Interspeech_2017/pdfs/0233.PDF
  - 可以用于使decoder偏向向特定感兴趣的方向解码 [He et al., 2017] https://arxiv.org/abs/1710.09617
- Attention-based models可以与另一个Attention-based models结合，达到使decoder偏向向特定感兴趣的方向解码 https://arxiv.org/abs/1808.02480

## 进一步改进

### 针对模型结构

#### 1. 使用词块作为建模单元

有哪些优势：

- 相比于字单元的LM，词单元的LM更具稳定性（？）
- 使用词级别建模可以使LM解码更加强大
- 使用更长的单元建模可以提高解码器LSTM记忆的有效性
- 使模型更有可能记住常用词的发音形状
- 更长的建模单元减小decode的步数，有效提升模型表现

#### 2. Multi-headed Attention

每个Head贡献不同的attention权重

### 针对优化方法

#### Minimum Word Error Rate (MWER)

传统Attention-based Sequence-to-Sequence model使用cross-entropy作为损失函数，不是直接对WER指标进行优化，而真正的目标是直接或间接地最小化WER  => MWER Training

- 使用样本估值--> [Optimizing expected word error rate via sampling for speech recognition]
- 使用N-best list估值-->[Explicit  word  error  minimization  in  N-best  list  rescoring] 

使用MWER的优化方法：

- WER降低8%-20%
- 需解码出N个最好的预测值--> computationally expensive

#### 计划采样(Scheduled Sampling)

decoder时随机使用模型真实label来作为下一个时刻的输入，而不是使用上一时间步的预测输出，因为此输出可能是错误的，其生成的下一个值也是不可信的。

[Scheduled Sampling for Sequence Prediction with Recurrent Neural Networks] https://arxiv.org/abs/1506.03099

- 避免错误累积（Exposure Bias）

- 帮助减少训练时与真实使用环境之间的表现差异

#### Asynchronous and Synchronous Training

- [ ] TODO

#### Label Smoothing

- 减小过拟合
- 预测时增加熵值，使模型更具可适性

## 额外的语言模型

### 为什么需要LM？

- 声音-文字这种成对的数据少，用于训练LM的text-only类的数据多
- 一些发音导致的问题可以使用语言模型来修正
- LM可以很好的融合到LAS中

More details：

 [An analysis of incorporating an external language model into a sequence-to-sequence model]

 https://arxiv.org/abs/1712.01996 

### 用LM扩展LAS

怎样融合？

- Shallow fusion：AM 和 LM 分开各自训练，然后在 beam-search 时做得分的加权 [相关论文](https://cs.corp.google.com/piper///depot/google3/experimental/users/anjuli/papers/icassp2018/lm/main.pdf)
- Deep fusion：AM 与 LM 各自训练后，添加 FineTune 层进行隐层融合 [相关论文](https://arxiv.org/pdf/1503.03535.pdf)
- Cold fusion：LM 仍然是预训练得到的，而 AM 则在 LM 的帮助下从零开始训练 [相关论文](https://arxiv.org/pdf/1708.06426.pdf)

融合结果表现对比：

![0mAInP.png](https://s1.ax1x.com/2020/09/29/0mAInP.png)

## Online Models

LAS无法做到实时识别

### RNN-T

- RNN-T架构里的CTC-AM与RNN-LM可以分别训练
- 一般来说，RNN-T比单独使用CTC的表现更好

RNN-T components作为LOSS可以单独初始化 => 可以提高表现

[![0mo74g.png](https://s1.ax1x.com/2020/09/30/0mo74g.png)](https://imgchr.com/i/0mo74g)



为了进一步提升RNN-T的表现，使用word pieces作为输出单元

- 使用字（graphemes ）单元 => 限制LM的上下文
- 使用词（word）单元 => OOVs

使用基于两者之间的词块（word pieces）建模

其他改进：

> - Recurrent projection layer
>   - Introduced after each LSTM layer in both the encoder and decoder.
>   - Improves accuracy with more compact representation.
> - Layer normalization
>   - Applied to all layers.
>   - Stabilizes hidden state dynamics of the recurrent layers.
>   - Improves accuracy
> - Native TF support for efficient batched forward-backward computation

 

- - [ ] 时间缩减层(Time Reduction Layer)

- 预测网络状态缓存 => 减小计算量
- Encoder / prediction network 多线程
  - Encoder分割为两个线程：before and after time-reduction layer
  - 预测网络运行与单个线程
  - 多个线程之间通过管道异步交互
- - [ ] 参数量化(Parameter Quantization)

### NT (Neural Transducer)











