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

![image-20200929101147667](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200929101147667.png)



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



