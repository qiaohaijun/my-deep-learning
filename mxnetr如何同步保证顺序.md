我遇到的问题是一个很简答的问题，如何保证mxnet的每个计算节点的启动顺序是按照host.txt[0]格式来启动。

当时的解决方案有几个

1. 对kvstore+ps-lite 做修改，他们协调一下启动顺序
2. 简单粗暴的方方法

```python
import time
time.sleep(1)
```

---

host.txt是节点的列表，计算节点的列表，名字可以随便起。
