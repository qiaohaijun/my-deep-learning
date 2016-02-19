<img src="http://parameterserver.org/images/parameterserver.png"  width=400 />

[![Build Status](https://travis-ci.org/dmlc/ps-lite.svg?branch=master)](https://travis-ci.org/dmlc/ps-lite)
[![GitHub license](http://dmlc.github.io/img/apache2.svg)](./LICENSE)

---

>这里是limu的ps-lite的readme, 自从我第一次听说parameter server的时候,我同时知道了两个项目.

>1. eric xing 的petuum
>2. limu 的parameterserver 项目, ( 现在这个项目还在,但是已经不再维护,原因在后面的history中有介绍)

>说实话, 这两个平台我哪一个也不清楚, 不知道那个好用, 而且, petuum 提出的ssp模型, 在很多算法上都取得了非常好的效果, 但是让我们在caffe >onpetuum上吃了很多苦头, 很难取得一个比较好的结果.
>
>最后, 同事通过邮件问了一下作者, 作者的答复让我们松了一口气, 原来作者也承认, caffe on petuum 效果不是很好, 尤其是机器很多的时候. 
>
>ssp 模型到底能不能用这里有几个可能:
>
>1. 代码中间有bug, 在 caffe on petuum上比较严重
>2. ssp 模型, 可以应用在凸模型上, 非凸模型不能用.
>3. 以后补充
>
>by qhj 

---

A light and efficient implementation of the parameter server
framework. It provides simple yet powerful APIs. For example, a worker node can
communicate with the server nodes via
- `Push(keys, values)`: push a list of (key, value) pairs to the server nodes
- `Pull(keys)`: pull the values from servers for a given key list
- `Wait`: wait a push or pull finished.

A simple example:

```c++
  std::vector<uint64_t> key = {1, 3, 5};
  std::vector<float> val = {1, 1, 1};
  std::vector<float> recv_val;
  ps::KVWorker<float> w;
  w.Wait(w.Push(key, val));
  w.Wait(w.Pull(key, &recv_val));
```

More features:

- Flexible and high-performance communication: zero-copy push/pull, supporting
  dynamic length value, user-defined filters for communication compression
- Server-side programming: supporting user-defined handles on server nodes

### Build

`ps-lite` requires a C++11 compiler such as `g++ >= 4.8`. On Ubuntu >= 13.10, we
can install it by
```
sudo apt-get update && sudo apt-get install -y build-essential git
```
Instructions for
[older Ubuntu](http://ubuntuhandbook.org/index.php/2013/08/install-gcc-4-8-via-ppa-in-ubuntu-12-04-13-04/),
[Centos](http://linux.web.cern.ch/linux/devtoolset/),
and
[Mac Os X](http://hpc.sourceforge.net/).

Then clone and build

```bash
git clone https://github.com/dmlc/ps-lite
cd ps-lite && make -j4
```

### How to use

`ps-lite` provides asynchronous communication for other projects. For example:
  - Distributed deep neural networks:
    [MXNet](https://github.com/dmlc/mxnet),
    [CXXNET](https://github.com/dmlc/cxxnet) and
    [Minverva](https://github.com/minerva-developers/minerva)
  - Distributed high dimensional inference, such as sparse logistic regression,
    factorization machines:
    [DiFacto](https://github.com/dmlc/difacto)
    [Wormhole](https://github.com/dmlc/wormhole)

### History

We started to work on the parameter server since 2010.

>这个项目是在2010年启动的, 那个时候我刚刚进入中科院软件所攻读并行计算的硕士生, >虽然后来读的是做科学计算(长了不少见识,对现在的工作没有直接的作用,但是有潜在影响), 我只能说这个项目是很长的项目, >这个项目应该是在百度内部的一个项目,而不是开源项目.

1. The first generation was
designed and optimized for specific algorithms, such as logistic regression and
LDA, to serve the sheer size industrial machine learning tasks (hundreds billions of
examples and features with 10-100TB data size) .

>第一代系统,专门为系统的特定算法做的优化,
>
>1. logistic regression
>2. LDA
>
>目的是为了对海量的数据挖掘项目提供支持.


2. Later we tried to build a open-source general purpose framework for machine learning
algorithm, the project is available at [dmlc/parameter_server](https://github.com/dmlc/parameter_server).

>第二代系统就是我们现在看到的第一个parameter server 项目, limu 论文提到的也是这个项目.

3. Given the growing demands from other projects, we created this projects,
named `ps-lite`, which provides a clean data communication API and an
lightweight implementation. We refactored the job launch, file I/O and machine
learning algorithms codes into other projects such as `dmlc-core` and
`wormhole`.

>第三代系统, 因为有了很多其他项目对ps 的需求, 原来的项目做了重构, 创建了现在ps-lite 项目. 这个项目使用清晰的数据交互API和轻量级的实现.
>
>我们把和ps 不相关的任务从中剥离, 将他们移到其他项目中, 比如
>
>1. job lauch, 文件IO, 移到了 dmlc-core
>2. 机器学习算法移到了wormhole


4. From the experience we learned when developing
   [dmlc/mxnet](https://github.com/dmlc/mxnet), we further refactored the codes
   based on [v1](https://github.com/dmlc/ps-lite/releases/tag/v1). The main
   changes include
   - lesser library dependencies
   - more flexible user-defined callbacks, which facilities different languages
   binding
   - leaves the data consistency management to users, such as the dependency
     engine of mxnet.

>现在ps-lite 的变化
>
>1. 更少的库依赖
>2. 灵活的用户定义的callback, 回调函数
>3. 将数据一致性的工作交给了用户, 比如mxnet中的 dependency engine

### Research papers
  1. Mu Li, Dave Andersen, Alex Smola, Junwoo Park, Amr Ahmed, Vanja Josifovski,
     James Long, Eugene Shekita, Bor-Yiing
     Su. [Scaling Distributed Machine Learning with the Parameter Server](http://www.cs.cmu.edu/~muli/file/parameter_server_osdi14.pdf). In
     Operating Systems Design and Implementation (OSDI), 2014
  2. Mu Li, Dave Andersen, Alex Smola, and Kai
     Yu. [Communication Efficient Distributed Machine Learning with the Parameter Server](http://www.cs.cmu.edu/~muli/file/parameter_server_nips14.pdf). In
     Neural Information Processing Systems (NIPS), 2014
efactor of ps-lite
