今天仔细的看了一下TF的website的那个dataflow 的数据流动。
我发现了TF和MXNET是不一样的。

TF 将weight和bias 定义为Var。并且使用了一个list 来将这些数据包括在一起。

MXNET的Symbol是将这些包装起来，掩盖了。

那么这里就有一个疑问？
mxnet和tf到底是怎么反向传播的。


---
如果这个任务我要来做，我会怎么做呢？

有条件

1. DAG
2. 算子
3. 每个算子的输入输出是已知条件
