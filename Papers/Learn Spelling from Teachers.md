### 题目

**Learn Spelling from Teachers: Transferring Knowledge from Language
Models to Sequence-to-Sequence Speech Recognition**

### 链接

[https://arxiv.org/pdf/1907.06017.pdf](https://arxiv.org/pdf/1907.06017.pdf)

### 标签

*知识蒸馏, 外部语言模型, 端到端, sequence-to-sequence*

### Contributions
基于知识蒸馏的思想，在训练阶段，以一个预训练的基于RNN的LM作为“老师”模型得到软标签（soft label），并以此"指导"一个基于Transformer的seq2seq的模型训练（拟合老师模型的softmax分布）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201022201102339.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
### 亮点与启发
文章指出：

- 类似于[LAS](http://arxiv.org/abs/1508.01211)的seq-to-seq模型中的语言模型是一个受限制的语言模型（conditional），原因在于其是基于**声音-文字**这种成对数据训练的，这种数据相对较少，相比于由大量纯文本语料库训练出来的语言模型，准确率相对较低，故为受限。
- 传统的语言模型融合方式（Shallow fusion / Deep fusion等）会增加模型本身的复杂度，特别是在测试阶段也需要其参与。
- soft labels比hard labels包含着更多的信息，其概率分布信息更加柔和，更利于另一个模型进行拟合
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201022202421823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
### 文章重点
使用**KL散度**（Kullback-Leibler divergence），即**相对熵**作为描述学生模型与老师模型输出概率分布的相似性，并作为最终的损失函数的重要组成部分。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201022203859880.png#pic_center)

而在最终的损失函数中，采用一个参数λ ∈ [0, 1]来权衡seq2seq模型本身与LST（Learn Spelling from Teachers）各自分布所占的比重：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201022203729641.png#pic_center)
最关键的是，这样训练出来的模型，在测试阶段完全可以去掉语言模型参与测试，在减小模型复杂度的同时保持较高准确率。

### 实验结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201022204445270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)

  

***持续记录关于端到端语音识别论文与资料：***

**[https://github.com/zyascend/End-to-End-Speech-Recognition-Learning](https://github.com/zyascend/End-to-End-Speech-Recognition-Learning)**