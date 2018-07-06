# Learning-Note

Deeper Depth Prediction with Fully Convolutional Residual Networks
—	Note by Jesse
1.网络结构
网络建立于ResNet-50基础之上，分为两块：第一块是ResNet-50，第二块是使用上池化-卷积层模块代替了传统的全连接层模块。
当输入向量的维度高于输出向量的维度时，神经网络就相当于一个编码器，实现了对高维向量的低维特征提取。
当输入向量维度低于输出向量维度时，神经网络就相当于一个解码器，实现了低维向量到高维向量的重构。
我们注意到整个网络的深度预测过程其实就是对一个图片由高维度到低纬度的特征提取，再由特征图重构到高维度的处理过程。
第一块ResNet-50传统深度卷积网络结构，初始化参数采用预训练权重，训练数据来自NYU Depth v2/ Make 3D。
第二块：
(1)普通版：unpooling-convolution block
上池化提升图片分辨率，在文章中，作者首先使用了上图的a结构，过2*2的unpooling层，没有值的地方填补0，过5*5的卷积，使让所有有0的地方都能被卷积到，过relu。4个这种结构就行了，五块性能没提高，反而耗内存。作者又在该基础上，设计了res-block的结构，也就是图c，其中up-projection就是在convolution后面加上一个3×3的卷积，同时再加上一个从低质量分辨率特征映射至最终结果的projection连接，这些块串起来使得特征映射尺寸越来越大。
(2)fast版：
在unpooling的结构，这就造成了75%的地方都是0，这样卷积跟放大的特征层进行卷积的话会有很多地方都是无效计算。因此，将卷积分块，分成四部分，四种小卷积核恰好能够包含5*5卷积核的所有部分，分别计算。然后再聚合，这就节省了计算时间。卷积得到的结果可以大量减少0的出现。将这种小卷积的结构替换掉unpooling就是fast up-projection。
2.损失函数
使用了huber做loss function：
 
这样做的好处是当残差（residual）很小的时候，loss函数为L2范数，残差大的时候，为L1范数的线性函数
3.效果
(1)室内：数据集NYU Depth v2
(2)室外：数据集Make3D
(3)代码复现效果图：
预测单张图像深度速度：
up-sampling：55ms   up-convolution：78ms
GPU ：a single NVIDIA GeForce GTX TITAN with 12GB of GPU memory 
4.本文章贡献：
FCRN这个网络做了两点贡献： 
1.使用了小卷积代替大卷积的方式实现了上采样。效果好，节省了计算时间。
2.使用了huber作为了loss function

 
论文连接：https://arxiv.org/abs/1606.00373
Github：  https://github.com/iro-cp/FCRN-DepthPrediction

