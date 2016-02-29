吴育昕，THU-CMU;Hulu-Google-Megvii炼丹中
贾开、Viko Tse、乔海军 等人赞同
DeepLearning并不只是用来做imagenet的.

当然在刷imagenet的过程中出现了很好的进展,比如BN,residual net, 别人都已经说过了. 
prelu这种我觉得不能算是什么创新,只能算是个偶尔涨点的trick. 这种脑洞我们以前一天能拍好几个.

其他方面一些感觉很有趣的进展:
### GAN

GAN (Generative Adversarial Net). 提出有段日子了，最近开始有些人继续做. GAN作为一个生成模型主要解决了以前用convnet生成图片的loss function太傻的问题, GAN使用一个network去评价生成出来的东西好不好. 最近的工作包括DCGAN, Laplacian-Pyramid GAN

### Binary Quantization 
Binary Quantization. 要把network跑在弱设备上(如手机), 一般要么做参数少的parametrization要么做Quantization. 在Quantization上, 最近的一些工作(BinaryConnect, BinaryNet)做到了在网络中使用Binary的参数和Binary的activation, 性能仍然不错. 这里继续做下去可能能改变我们对网络的认识.

### Reinforce
Reinforce. 一个是DeepMind一直以来做了很多RL方面的工作(游戏,围棋,博弈论), 把deep learning用在了各种task里, 非常夺人眼球. 再一个是RL的框架其实是能够将deep learning用在不可微函数上的一种手段, 现在的hard attention, binary quantization这些其实多少都跟RL有点关系.

### supervised
(weak/un)supervised. 几年来因为没有实用性一直被大家忽略, 而Bengio一直坚持觉得这里头有潜力. 最近的ladder network作为一种加强版的autoencoder, 已经能够从少量标注样本中学习. 另外就是利用video里的optical flow进行无监督学习.

既然不匿了就顺手广告好了. 北京的同学可以来face++看看,这些东西都有人在玩.
