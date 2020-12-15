---
title: 常用优化算法的一些细节探讨
subtitle: "SGDM, NAG, AdaGrad, RMSProp, Adam在TensorFlow中的实现"
layout: post
mathjax: true
author: Meyer
catalog: true
header-img: "img/wp2478972.jpg"
tags: 
    - Deep Learning
    - Machine Learning
    - Summary

---

前几天在看tensorflow keras的文档，其中SGD的实现描述看得我一头雾水。

![](/img/in-post/opt_algs_details/tf_sgd_doc.jpg)

"Update rule when `momentum` is larger than 0" 和 "When `nesterov=False`" 的更新规则怎么看都不对。纠结了一会，看到keras SGD的文档时才豁然开朗，原来tensorflow的文档抄都抄错！

![](/img/in-post/opt_algs_details/keras_sgd_doc.jpg)

keras文档中描述的就是我们熟悉的更新规则了，但我又发现了一个小小的问题，就是SGD with momentum(SGDM)的更新规则表述有很多种，我们设初始的速度$v=0$，初始的参数为$\theta_0$，momentum参数为$\beta$，learning rate为$\alpha$，则最常见的是以下两种：

①  

$$v_t=\beta v_{t-1} - \alpha\nabla\mathcal{L}(\theta_{t-1})$$ 
   
$$\theta_t=\theta_{t-1}+v_t$$
  

②  
  
$$v_t=\beta v_{t-1} + \alpha\nabla\mathcal{L}(\theta_{t-1})$$
  
$$\theta_t=\theta_{t-1}-v_t$$
  

经过简单推导发现，①②两种表述其实是一样的，尝试展开写出前面几项：$v_1, \theta_1, v_2, \theta_2$，就能发现其结果是一样的。第①种表述可以理解为$v$表示了**前进的方向**，由上一次的前进方向和当前梯度的负方向来决定，因此在参数更新时用（向量）**加法**。而第②种可以理解为$v$是**梯度的指数加权移动平均**，因此在参数更新时就像一般的梯度下降法一样**减去**梯度。  

还有两种表述如下：  
③  
  
$$v_t=\beta v_{t-1} + \nabla\mathcal{L}(\theta_{t-1})$$
  
$$\theta_t=\theta_{t-1}-\alpha v_t$$
  

④   
  
$$v_t=\beta v_{t-1} + (1-\beta)\nabla\mathcal{L}(\theta_{t-1})$$
  
$$\theta_t=\theta_{t-1}-\alpha v_t$$
  

这里学习率$\alpha$移到了更新$\theta$时使用，$v$的更新方式稍有不同。

其实以上的方式**都可以work**，只不过可能收敛速度稍有不同，超参数可能也要因应调整。不过，我们经常用到的应该是第①②种，也是keras的实现方式，而tensorflow也有提供第③种，见下图

![](/img/in-post/opt_algs_details/tf_momentum_doc.jpg)

也可以从tensorflow的[源码](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/kernels/training_ops.cc)中看到参数更新的方式（注：`optimizers`下并没有实现参数更新，参数更新是在`training_ops.cc`中实现的）

```cpp
// TensorFlow momentum & Keras momentum

template <typename T>
struct ApplyMomentum<CPUDevice, T> {
  void operator()(const CPUDevice& d, typename TTypes<T>::Flat var,
                  typename TTypes<T>::Flat accum,
                  typename TTypes<T>::ConstScalar lr,
                  typename TTypes<T>::ConstFlat grad,
                  typename TTypes<T>::ConstScalar momentum, bool use_nesterov) {
    accum.device(d) = accum * momentum() + grad;
    if (use_nesterov) {
      var.device(d) -= grad * lr() + accum * momentum() * lr();
    } else {
      var.device(d) -= accum * lr();
    }
  }
};

template <typename T>
struct ApplyKerasMomentum<CPUDevice, T> {
  void operator()(const CPUDevice& d, typename TTypes<T>::Flat var,
                  typename TTypes<T>::Flat accum,
                  typename TTypes<T>::ConstScalar lr,
                  typename TTypes<T>::ConstFlat grad,
                  typename TTypes<T>::ConstScalar momentum, bool use_nesterov) {
    accum.device(d) = accum * momentum() - grad * lr();
    if (use_nesterov) {
      var.device(d) += (accum * momentum() - grad * lr());
    } else {
      var.device(d) += accum;
    }
  }
};
```

接下来探讨NAG(Nesterov Accelerated Gradient)，在NAG中，梯度是根据当前参数apply了当前速度$v$之后来计算的

![](/img/in-post/opt_algs_details/NAG.jpg)

而文档中的更新规则是这样写的：
```python
velocity = momentum * velocity - learning_rate * g
w = w + momentum * velocity - learning_rate * g
```

我们没有见到根据临时的参数更新来计算梯度的步骤，这是怎么来的呢？NAG的更新规则可以描述如下：
  
$$v_t=\beta v_{t-1} + \alpha\nabla\mathcal{L}(\theta_{t-1}-\beta v_{t-1})$$
  
$$\theta_t=\theta_{t-1}-v_t$$
 

与不使用Nesterov的SGDM相比，唯一的区别就是计算梯度时是使用$\theta_{t-1}$还是$\theta_{t-1}-\beta v_{t-1}$，我们对公式做一个变形，令$\varphi_t=\theta_t-\beta v_t$，则$\varphi_0=\theta_0$，

$$
\begin{aligned}
\varphi_t&=\theta_t - \beta v_t \\
&=\theta_{t-1}-v_t - \beta v_t \\
&=\theta_{t-1} - \beta v_{t-1} - \alpha\nabla\mathcal{L}(\theta_{t-1}-\beta v_{t-1}) - \beta v_t \\
&=\varphi_{t-1}-\alpha\nabla\mathcal{L}(\varphi_{t-1}) - \beta v_t
\end{aligned}
$$

即  
  
$$v_t=\beta v_{t-1} + \alpha\nabla\mathcal{L}(\varphi_{t-1})$$
  
$$\varphi_t=\varphi_{t-1}-\alpha\nabla\mathcal{L}(\varphi_{t-1}) - \beta v_t$$

也就是文档中描述的规则（注：正负号的不同犹如上述①②的差别，实际是相同的）。要得到参数，计算$\theta_t=\varphi_t+\beta v_t$即可。这样做的好处是当模型参数很多时，我们不用多存储一份临时参数来计算梯度，只需存储$\varphi_t$即可，节省了空间。  

最后，我们看一下AdaGrad，RMSProp，Adam的实现：
```cpp
// AdaGrad

template <typename T>
struct ApplyAdagradV2<CPUDevice, T> {
  void operator()(const CPUDevice& d, typename TTypes<T>::Flat var,
                  typename TTypes<T>::Flat accum,
                  typename TTypes<T>::ConstScalar lr,
                  typename TTypes<T>::ConstScalar epsilon,
                  typename TTypes<T>::ConstFlat grad, bool update_slots) {
    if (update_slots) {
      accum.device(d) += grad.square();
    }
    var.device(d) -= grad * lr() / (accum.sqrt() + epsilon());
  }
};
```

```cpp
// RMSProp

struct ApplyRMSProp<CPUDevice, T> {
  void operator()(const CPUDevice& d, typename TTypes<T>::Flat var,
                  typename TTypes<T>::Flat ms, typename TTypes<T>::Flat mom,
                  typename TTypes<T>::ConstScalar lr,
                  typename TTypes<T>::ConstScalar rho,
                  typename TTypes<T>::ConstScalar momentum,
                  typename TTypes<T>::ConstScalar epsilon,
                  typename TTypes<T>::ConstFlat grad) {
    ms.device(d) += (grad.square() - ms) * (static_cast<T>(1) - rho());
    mom.device(d) =
        mom * momentum() + (grad * lr()) / ((ms + epsilon()).sqrt());
    var.device(d) -= mom;
  }
};
```

乍一看计算累积变量的方式好像不一样：
   
$$ms = ms + (grad^2 - ms) * (1-\rho)$$  

其实和我们熟悉的
  
$$ms=\rho\cdot ms + (1-\rho)\cdot grad^2$$
  
是等价的。

```cpp
// Adam

template <typename Device, typename T>
struct ApplyAdamNonCuda {
  void operator()(const Device& d, typename TTypes<T>::Flat var,
                  typename TTypes<T>::Flat m, typename TTypes<T>::Flat v,
                  typename TTypes<T>::ConstScalar beta1_power,
                  typename TTypes<T>::ConstScalar beta2_power,
                  typename TTypes<T>::ConstScalar lr,
                  typename TTypes<T>::ConstScalar beta1,
                  typename TTypes<T>::ConstScalar beta2,
                  typename TTypes<T>::ConstScalar epsilon,
                  typename TTypes<T>::ConstFlat grad, bool use_nesterov) {
    // ...
    const T alpha = lr() * Eigen::numext::sqrt(T(1) - beta2_power()) /
                    (T(1) - beta1_power());
    // ...

      if (use_nesterov) {
        m += (g - m) * (T(1) - beta1());
        v += (g.square() - v) * (T(1) - beta2());
        var -= ((g * (T(1) - beta1()) + beta1() * m) * alpha) /
               (v.sqrt() + epsilon());
      } else {
        m += (g - m) * (T(1) - beta1());
        v += (g.square() - v) * (T(1) - beta2());
        var -= (m * alpha) / (v.sqrt() + epsilon());
      }
    };
```
debias的系数是通过计算变量`alpha`得到的，  

$$alpha=\frac{\sqrt{1-\beta_2^t}}{1-\beta_1^t}$$


与分别对累积变量`m`和`v`进行去偏置，减少了一次乘法运算。而`m`和`v`的计算方式与RMSProp中类似，形式稍有改动优化，也减少了一次乘法运算，即由原本的**两次乘法两次加法变成三次加法一次乘法**。   

还注意到RMSProp中的`epsilon`是放在分母的根号里面的，不过`epsilon`的位置对于实际优化过程应该影响不大。   


**参考**  

- [【TensorFlow】优化器AdamOptimizer的源码分析](https://zhuanlan.zhihu.com/p/63500952)
- [tensorflow/core/kernels/training_ops.cc](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/kernels/training_ops.cc)
- [An overview of gradient descent optimization algorithms](https://arxiv.org/abs/1609.04747)
- The [*Deep Learning*](https://www.deeplearningbook.org/) book by Ian Goodfellow and Yoshua Bengio and Aaron Courville.