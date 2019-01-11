
### 01-NAIS （item sim）
**NAIS Neural Attentive Item Similarity Model for Recommendation-TKDE2018**

这篇论文运用attention机制的地方在计算项目相似度，作者认为用户的历史商品对于推荐的目标商品所产生的影响是不同的，所以在计算用户对商品的预测评分时，根据用户的历史商品得到的用户特征应该根据影响因子作权重加和。在使用传统的softmax时发现效果不好，因为要计算影响因子的数量太大，最后得出的概率分布并不好，所以在softmax的分母添加了指数衰减因子              
###  公式
![](https://upload-images.jianshu.io/upload_images/8161042-0e6a5d697ed8ffec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/8161042-fc9a2869f14a149d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**损失函数：**
![](https://upload-images.jianshu.io/upload_images/8161042-d655c1d59dd8e255.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 结构图                                                                                                                                                                                      
![NAIS结构图](https://upload-images.jianshu.io/upload_images/8161042-11c94cf1ef625538.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 数据集
![](https://upload-images.jianshu.io/upload_images/8161042-ec00ff4af8fcac75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 实验结果
![](https://upload-images.jianshu.io/upload_images/8161042-567f04d43049eae3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 相关工作
这部分作者介绍的很迷，花了几大段写推荐系统的任务从显示评分到隐式评分，分别采用不同的度量方式。介绍了一个最先进的排序方法，是对抗个性化排序。具体论文没有看，以后需要再看。然后介绍了深度学习在推荐系统的应用，分了两个部分，一个是学习特征表示，另一个是学习scoring function。关于第二种介绍了三篇比较先进的论文。最后讨论了另一个采用attention的论文，这篇论文是基于用户的，并且强调了自己论文的亮点，想出了一个解决softmax计算大规模概率分布的方法。
## 代码分析
### 定义参数
* **path :** 数据路径
* **dataset:**  选择的数据集，pinterest 还是movielens
* **pretrain:** 0: No pretrain, 1: Pretrain with updating  FISM variables, 2:Pretrain with fixed FISM variables.
* **verbose:** Interval of evaluation
*  **batch_choice:** user: generate batches by user, fixed:batch_size: generate batches by batch size
*  **epochs:** 周期数
*  **weight_size:** weight size
*  **embed_size:** Embedding size
*  **data_alpha:** Index of coefficient of embedding vector
*  **regs:** Regularization for user and item embeddings.
*  **alpha:** Index of coefficient of embedding vector
*  **train_loss:** Caculate training loss or nor
*  **beta:** Index of coefficient of sum of exp(A)
*  **num_neg:** Number of negative instances to pair with a positive instance.
*  **lr:** learning rate
*  **activation:** Activation for ReLU, sigmoid, tanh.
*  **algorithm:** 0 for NAIS_prod, 1 for NAIS_concat

### 定义输入接口
```
 def _create_placeholders(self):
        with tf.name_scope("input_data"):
            self.user_input = tf.placeholder(tf.int32, shape=[None, None])	#the index of users
            self.num_idx = tf.placeholder(tf.float32, shape=[None, 1])	#the number of items rated by users
            self.item_input = tf.placeholder(tf.int32, shape=[None, 1])	  #the index of items
            self.labels = tf.placeholder(tf.float32, shape=[None,1])	#the ground truth
```
* **user_input:** 用户的index， shape=[None, None]
* **num_idx:** 每个用户评分的物品，shape=[None, 1]
* **item_input:** 所有物品的index， shape=[None, 1]
*  **labels:** 标签， shape=[None,1]
### 创建变量
为了便于理解，简化了参数：
batch_size:b
embedding size: e
weight size: w
Q_:[N+1, e] 用来训练历史商品的嵌入
Q:[N,e] 用来训练目标商品的嵌入
b: [1, w]
h:[w,1]
dot product algo: W: [e,w]
concat product algo: W: [2e, w]

### attention network
![image.png](https://upload-images.jianshu.io/upload_images/8161042-379efbe73377e345.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/8161042-fc9a2869f14a149d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
作者定义了一个attention函数，输入是矩阵q_和q的concat或者点积。输出矩阵每行结果为为$\sum a_{ij}q_j$



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcwNDI5Njc4LC0xNjg4NTkzNDg4LC0xMz
A0OTQ1NzA0XX0=
-->