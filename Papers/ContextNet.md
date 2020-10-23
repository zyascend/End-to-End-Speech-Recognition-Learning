### 题目

**ContextNet: Improving Convolutional Neural Networks for Automatic Speech
Recognition with Global Context**

### 链接

[https://arxiv.org/pdf/2005.03191.pdf](https://arxiv.org/pdf/2005.03191.pdf)

代码实现：
[https://github.com/iankur/ContextNet](https://github.com/iankur/ContextNet)
### 标签

*Speech Recognition, CNN*

  

### Contributions
- 使用了基于global context的CNN作为ASR的模型
- 持续的负采样和模型缩放来减小模型的参数的同时保证WER维持较小值


### 亮点与启发
文章指出：

- 一般CNN由于卷积核大小的限制无法获取到长距离的依赖，导致表现不如RNN-based模型
- **文中使用的SE结构，将一个序列的特征向量“挤压”（squeeze）到一个global context vector，然后将这个上下文向量作用与每个原始的特征向量（两者通过相乘结合）**
- 提出负采样方案：8次不间断减小输入序列的长度，减小计算耗时的同时保证识别的准确率
### 文章重点
#### SE如何构造计算
公式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020102321133633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
这里的激活函数Act()使用**Swish**，经实验证明，swish函数表现优于ReLU。![在这里插入图片描述](https://img-blog.csdnimg.cn/2020102321185314.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)
如图红框，x经过平均池化后，转换成 一个global channelwise 的权重 θ(x)，在与原向量x进行element-wise multiplication之后得到SE处理的结果。本质是**对原向量注入权重信息，增加了全局向量，使原向量有了对长距离（上下文）的感知**
#### CNN块的构造
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201023212349312.png#pic_center)
f(x) = Act(BN(Conv(x)) 得到SE的结果后在加上残差连接P(x)构成一个单一的CNN-block。 

多个CNN-block叠加构成模型主体：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201023212712322.png#pic_center)

### 实验结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201023213101329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5Y29kZWNzZG4=,size_16,color_FFFFFF,t_70#pic_center)

***持续记录关于端到端语音识别论文与资料：***
**[https://github.com/zyascend/End-to-End-Speech-Recognition-Learning](https://github.com/zyascend/End-to-End-Speech-Recognition-Learning)**