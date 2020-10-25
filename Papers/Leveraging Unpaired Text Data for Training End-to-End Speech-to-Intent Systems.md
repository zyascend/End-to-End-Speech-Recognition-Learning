### 题目

**Leveraging Unpaired Text Data for Training End-to-End Speech-to-Intent Systems**

### 链接

[https://arxiv.org/pdf/2010.04284.pdf](https://arxiv.org/pdf/2010.04284.pdf)
### 标签

*Speech-to-intent, spoken language understanding, end-to-end systems, pre-trained text embedding, synthetic speech
augmentation*

### Contributions
- 运用迁移学习的知识，联合训练speech-to-intent 模型和text-to-intent模型，利用较多的text-intent数据作为基于speech-intent的端到端S2I(speech-to-intent)模型的补充
- 为了解决speech-intent数据集的稀缺，通过TTS系统把text-intent数据转换为speech-intent数据，以达到数据增强的目的。

### 亮点与启发
由于深度神经网络极度依赖于大量的数据来驱动得到较好的效果，而对于端到端的S2I系统， intent-labeled speech data太过稀缺了，为了比肩传统的模型（speech-text-intent）,需要设法弥补数据稀缺的问题。
为解决此问题的答案就是——**迁移学习**。

基于Text-intent类数据较多这一现状，使用类似bert的预训练模型，对基于speech-intent的声学模型进行“指导”，将知识迁移到需要的端到端S2I系统上。

### 文章重点
1. 首先使用一般的**语音-文本**数据预训练一个一般的ASR模型。
2. 使用**语音-意图**数据在此ASR的基础上训练初步的S2I（speech-to-intent）模型。如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201025205736989.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
这样的好处是得到一个声学embedding层，此层把LSTM的所有hidden state做了一个时间平均，相当于概括了一整个语音序列的上下文，并以此对应预测一个意图（intent）。而在测试阶段，完成迁移后的ASR部分便可丢弃不用。
3. 使用Bert预训练一个 text-to-intent模型。此模型使用的数据量较大，其输出具有较好的逻辑分布。
4. 用1，2步得到的speech-to-intent模型与第3步模型联合训练，完成第二次知识迁移。![在这里插入图片描述](https://img-blog.csdnimg.cn/20201025210730130.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
值得注意的是，分割线上下分别为两个不同的任务（multi-task），同时训练，共享最后一个shared DNN layer（这个layer由 text-only classification task即上面的BERT模型初始化得到），上下拥有各自的优化函数：  
**text branch：** **LCE(AE) + αLCE(T E).**
**speech branch：** **LMSE(AE, T E) + LCE(AE) + αLCE(T E)**
MSE：mean squared error，此LOSS造成的参数更新只会反向传播到声学部分（speech branch），因为已经默认text部分是在大量数据下训练的，已经趋于完善，无需再进行自身对text-intent数据的学习。
  
  5. 除了以上尝试之外，本文还将text-intent文本通过TTS将文字转化成语音，构造了更多的speech-intent数据，直接训练端到端的模型。
### 实验结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020102521222250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
可见，准确率最高的是使用20小时speech-intent数据直接训练端到端S2I系统，不过经过迁移学习的模型，即使是数据量小，也能获得不错的准确率表现。

### 引申
根据此文的思想，在处理端到端语音识别时，同样面临着speech-text成对数据较少，纯文本（text-only）数据较多的情况，这亦是目前端到端ASR系统提高识别准确率的瓶颈所在，我们是否也可使用类似的方法解决数据稀缺的问题，值得更加深入的思考和实践。

***持续记录关于端到端语音识别论文与资料：***
**[https://github.com/zyascend/End-to-End-Speech-Recognition-Learning](https://github.com/zyascend/End-to-End-Speech-Recognition-Learning)**