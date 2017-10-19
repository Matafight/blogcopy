title: CNN结构的发展
date: 2017-10-19 19:41:19
categories: 机器学习
tags: Deep Learning
---


## 前言
最近李沐大神开了一门关于深度学习的直播课程，每周六都在斗鱼上直播，这门课不仅介绍深度学习的理论知识，更重要的是还会给大家演示实战操作，包括各种模型的代码，采用的深度学习框架为Mxnet，这门课不仅使大家对深度学习有了更深一步的了解，毕竟有了上手操作的机会，由于框架是Mxnet，其实在一定程度上也推广了Mxnet的使用，可谓一举两得。当然，对于我来说就是既学习了理论又掌握了一个新的框架，简直不要太爽。

这篇文章的主要内容是回顾比较有名的几个卷积神经网络结构以及它们的动机。

## AlexNet
AlexNet是2012年ImageNet 图像识别挑战的冠军模型，AlexNet是一个八层的卷积神经网络，其中前五层是卷积层，后两层是全连接层，最后一层是输出层。在当时，这个模型的参数可以说是非常巨大了，所以当时的AexnNet采用了双数据流的设计，采用了两个GPU，每个GPU存储每层网络的一半节点，同时在一些特定层相互通信。这个设计是由于当时硬件条件的限制而产生的，现在一般都用不着这样设计了。
从论文中截取的网络结构如下：
![](http://7xiegr.com1.z0.glb.clouddn.com/Alexnet.PNG)

假设输入为一张图片，那么该图片卷积之后的大小的计算公式为：

$$ 
\frac{N + 2\times padding - F}{stride} +1
$$
其中N是图片的长或宽，F是卷积核的长或宽。

输入层： 输入图片的大小是224x224x3。

第一层卷积：第一层一共有96个卷积核，kernel_size : 11x11，stride :4x4 . 之后接一个pooling操作，kernel_size: 3x3,stride:2x2,padding: 0x0

第二层卷积： 第二层有256个卷积核, kernel_size: 5x5, stride:1x1, padding:2x2, 之后也是接一个pooling 操作： kernel_size : 3x3, stride:2x2, padding:0x0

第三层卷积： 第三层有384个卷积核，kernel_size: 3x3, stride:1x1, padding:1x1, 之后不接pooling层

第四层卷积： 第四层卷积与第三层参数一致

第五层卷积： 第五层有256个卷积核，kernel_size:3x3, stride:1x1,padding:1x1, 之后不接pooling层

第六层： 全连接层,隐节点数为 4096，之后接一个dropout层

第七层： 全连接层，隐节点数为4096, 之后接一个dropout层

第八层：输出层, 结点个数为10(因为分类任务有10类)。




## VGGNet
AlexNet在构建过程中需要手写代码一层一层地构建，而且每一层都比较复杂，参数比较多。VGG的思路将神经网络分成多个block，每个block里面都是重复结构的网络层，这样VGG在构建过程中可以利用编程语言的循环操作，在每个block内都循环地增加相同的网络。最后将所有block组合成最终的网络结构,而且最终可以生成比较深的网络。
![](http://7xiegr.com1.z0.glb.clouddn.com/vggnet.PNG)

## NiN(Network in Network)
![](http://7xiegr.com1.z0.glb.clouddn.com/NiN_compare.PNG)
在一般的CNN中，输入的图片在与卷积核卷积之后生成 feature map, 卷积核与图片的作用是线性的，NiN的想法是用一个多层感知机替代这个线性操作，从而非线性化卷积过程，能提取更多有用的特征。
NiN另一个特点是不再使用全连接层作为最后一层，对于一般的分类任务来说，输出层都是一个节点数为类别数的全连接层，全连接层会导致网络过于复杂，影响泛化能力。NiN使用了全局均值池化的操作作为最后一层的输出，假设类别数为10，NiN最后一层为一个10层的 feature map,对每一层做一个均值pooling作为10个类的分类指标。

具体在实现过程中可以用卷积核大小为1x1的卷积操作替代多层感知机。
![](http://7xiegr.com1.z0.glb.clouddn.com/mlpconv.PNG)

http://blog.csdn.net/hjimce/article/details/50458190
## GoogLeNet
2014年的ImageNet竞赛的冠军网络，GoogleNet借鉴了NiN的的MLP思想，也是用kernel size 为1x1的卷积操作替代MLP。
其中一个核心部件称为:Inception
![](http://7xiegr.com1.z0.glb.clouddn.com/inception.PNG)
GoogLeNet的特点是较复杂且比较深，每个Inception由四个并行的卷积块组成。不同大小的卷积核对应着获取输入数据不同粒度的特征，图中1x1大小的卷积操作的作用是一方面调整输入数据channel的个数，防止后面卷积操作参数过多，过拟合。另一方面类似于NiN中的MLP，起到了非线性化卷积操作的作用。


## ResNet
2015年的ImageNet竞赛的最大赢家是ResNet(残差网络)，其第一次极大地提高了网络的深度，达到了上百层。提高网络深度的一大难题就是梯度消失，梯度在反向传播过程中无法传递到靠前的网络层，从而导致网络难训练。

ResNet的思想是采用跨层连接来解决梯度消失问题，其结构如下所示：
![](http://7xiegr.com1.z0.glb.clouddn.com/res_%E8%B7%A8%E5%B1%82%E8%BF%9E%E6%8E%A5.PNG)


为什么叫残差网络呢？
将上面的结构拆成两个网络的和，其中最下面一层是共享的,如下图所示：
![](http://7xiegr.com1.z0.glb.clouddn.com/resnet_%E5%B1%95%E5%BC%80.PNG)
在训练过程中，左边的网络更容易训练，所以这个网络会更快地收敛，这个网络没有拟合到的部分，可以称为残差，就被右边的网络抓取住，所以称为残差网络。直观上来看，即使加深网络，跨层连接仍然可以使得底层网络得到充分训练，从而不会让训练更难。


## DenseNet

![](http://7xiegr.com1.z0.glb.clouddn.com/dense_net.PNG)
DenseNet与 ResNet最大的区别是ResNet采用累加的方式去利用过去的信息，而DenseNet采用拼接的方式。

**DenseNet号称参数比ResNet参数更少？为什么？**

因为DenseNet每一层的output(也就是 feature map)都会concat到后续所有层的input上，每一层的input都是由前面所有层的output concat得到的。这个设计看似会产生很多参数，其实并没有，因为feature map不需要重新训练，而且，由于采用了这种直接拼接的方式(Resnet采用的是加和的方式)，信息保留的比较多，不需要通过增大feature map的层数来保留信息，所以feature map 的层数可以设置得比较小，所以参数较少。





**神经网络为什么致力于提高深度而不是提高宽度呢？毕竟网络较深时不好优化，而宽度较大却没有这个问题?**
因为增大宽度使得模型的复杂性急速上升，会overfitting。

这里只是列出来一些直觉上的和结构上的东西，并没有深入阐述，想要深入了解还需要去看原始论文，参考文献就是按照本文介绍网络的先后顺序排列的。
## **参考**：
1. https://zh.gluon.ai/chapter_convolutional-neural-networks/index.html
2. Krizhevsky A, Sutskever I, Hinton G E. Imagenet classification with deep convolutional neural networks[C]//Advances in neural information processing systems. 2012: 1097-1105.
3. Simonyan K, Zisserman A. Very deep convolutional networks for large-scale image recognition[J]. arXiv preprint arXiv:1409.1556, 2014.
4. Szegedy C, Liu W, Jia Y, et al. Going deeper with convolutions[C]//Proceedings of the IEEE conference on computer vision and pattern recognition. 2015: 1-9.
5. He K, Zhang X, Ren S, et al. Deep residual learning for image recognition[C]//Proceedings of the IEEE conference on computer vision and pattern recognition. 2016: 770-778.
6