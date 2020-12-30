---
title: Comparison of Bahdanau Attention and Luong Attention
subtitle: Bahdanau Attention和Luong Attention的区别
layout: post
mathjax: true
author: Meyer
catalog: true
header-img: "img/52f61nfzmwl51.jpg"
tags: 
    - Deep Learning
    - Machine Learning
    - Summary

---

最近在看李宏毅老师的[深度学习与人类语言处理](http://speech.ee.ntu.edu.tw/~tlkagk/courses_DLHLP20.html)课程，其中语音辨识的部分提到有两种Attention的类型，而LAS模型第一次成功应用时是这两种Attention结合使用的。

光看投影片还有点不是很理解，具体的操作是如何进行的，参考了原论文之后思路就清晰了，下面就简要说明一下课上提到的两种Attention机制，即Bahdanau Attention和Luong Attention，以及它们的结合版。

![](/img/in-post/attention/back_to_attention.png)

![](/img/in-post/attention/back_to_attention2.png)

### Bahdanau Attention

在Bahdanau Attention中，当前time step的输出$y_i$是由上一个time step的输出$y_{i-1}$，**当前**RNN的hidden state $s_i$，以及根据**上一个**time step的hidden state $s_{i-1}$ 计算出来的attention $c_i$来决定的，亦即
  
$$
p(y_i|\{y_1, ..., y_{i-1}\}, x)=g(y_{i-1}, s_i, c_i)
$$

而当前RNN的hidden state则是由上一个time step的hidden state和输出，以及当前的attention计算得到的，即

$$
s_i=f(s_{i-1},y_{i-1},c_i)
$$

而attention的计算，则利用到上一个time step的hidden state和encoder各个time step的hidden state的信息$h_{encoder}$，即

$$
c_i=a(s_{i-1}, h_{encoder})
$$  

  
[下图](https://github.com/lvapeab/nmt-keras)很好地描述了这个过程，不过没有包含算法初始时的几步，根据[原论文](https://arxiv.org/abs/1409.0473)的描述，decoder的初始hidden state为
  
$$
s_0=W_s \overleftarrow{h}_1
$$
  
即encoder backward RNN的最后一个hidden state的线性变换。当然，也许用forward RNN的hidden state $\overrightarrow{h}_{T_x}$，或者全零初始化，效果也相差不大。

![](/img/in-post/attention/attention_nmt_model.png)

有了$s_0$，就可以计算第1个time step的attention $c_1$，在该论文中用到的方法为**concat**（Luong Attention中会提到）。我们再定义一个特殊的初始的输出字符$y_0 = \left \langle \text{GO} \right \rangle$，于是我们便可以计算decoder的第1个hidden state $s_1 = f(s_0, y_0, c_1)$，最后再根据当前的hidden state产生输出的分布$p(y_1)=g(y_{0}, s_1, c_1)$。根据$s_1$，我们可以继续计算$c_2$，$s_2$， $y_2$，依此类推，直至输出$\langle \text{EOS} \rangle$或超出长度限制为止。


### Luong Attention

根据[Luong论文](https://arxiv.org/abs/1508.04025)中的描述，他们的方法与Bahdanau的主要有以下三个不同。

一是他们用到了多层的LSTM网络，decode的时候只使用顶层的hidden state信息，如下图所示。而Bahdanau论文中没有使用多层的网络，encoder用的是一个双向的RNN，decoder是一个单向的RNN。

![](/img/in-post/attention/figure_1.jpg)

二是计算attention和输出的路径不同。注意到在Bahdanau Attention中，当前time step的attention是根据**上一个**time step的hidden state计算的，而在Luong Attention中，是根据**当前**timp step的hidden state计算的。给定当前decoder的hidden state $h_t$以及由$h_t$计算得到的attention $c_t$，由下式给出输出的分布：

$$
\tilde{h}_t=\tanh (W_c[c_t; h_t])  \\
p(y_t|y_{<t}, x) = \text{softmax}(W_s\tilde{h}_t)
$$

即Luong计算attention及输出的路径是

$$h_t \rightarrow a_t \rightarrow c_t \rightarrow \tilde{h}_t \rightarrow y_t$$

而Bahdanau的路径则是

$$h_{t-1} \rightarrow a_t \rightarrow c_t \rightarrow h_t \rightarrow y_t$$

![](/img/in-post/attention/figure_2.jpg)

三是计算attention的方式，前面提到Bahdanau中用到的是concat，而Luong论文中又实验了几种方式。encoder各个hidden state $\bar{h}_s$对应的权重为

$$
\begin{aligned}
a_t(s) &= \text{align}(h_t, \bar{h}_s)  \\
&= \frac{\exp(\text{score}(h_t, \bar{h}_s))}{\sum_{s'}\exp(\text{score}(h_t, \bar{h}_{s'}))}
\end{aligned}
$$

其中，score的计算方式有如下几种

![](/img/in-post/attention/score.jpg)

### 两者结合

在[LAS模型](https://arxiv.org/abs/1508.01211)中，用到的attention机制是以上两种的结合：即**当前**时刻的context vector $c_i$，是根据当前时刻decoder的hidden state $s_i$计算的，不仅用于预测当前的输出$y_i$，也作为**下一个**time step的RNN的输入，如下图所示

![](/img/in-post/attention/las_model.jpg)

具体的计算如下：

![](/img/in-post/attention/las_formula.jpg)

至于decoder初始状态$s_0$的选择，原文中似乎没有提到。此外，模型结构图中标识的符号似乎与文中描述的公式不太吻合，从图中也很难说清楚各个部分涉及的运算，原文中只提到decoder用的RNN是2层的LSTM，实现应该与Luong Attention中的相似。总而言之，takeaway是在LAS中，context vector用于预测当前time step的输出，也作为下一个time step的输入，这使模型能知道上一个time step时的attention信息，并期望其能运用上该过去的信息。
  
**参考**  
- [What is the difference between Luong attention and Bahdanau attention?](https://stackoverflow.com/questions/44238154/what-is-the-difference-between-luong-attention-and-bahdanau-attention)
- [Attention Mechanism](https://blog.floydhub.com/attention-mechanism/)
- [seq2seq-translation.ipynb](https://github.com/spro/practical-pytorch/blob/master/seq2seq-translation/seq2seq-translation.ipynb)