mx::mxrecordIO
threadpool 多线程

lst-> write_record-> bin
mxrecordIO & multi-thread

---

data loading dmlc-core

1. IO design
2. data format
3. data loading
4. interface design

---

1.1 data preparation
1. peak dataset -> small file
2. parallel packing

----

1.2 data loading
把data load 进ram

1. 连续读
2. 减少loading byte  image -> jpeg
3. 多线程
4. ram saving

不能打文件整个loading进内存中

---

2. data format
DNN需要大数据：-> binary data 可split格式
(dmlc-core) binary recordIO format


binaray recordIO basic format

cflag 

0 complete record
1 start of multiple rec
2. middle of multiple rec 
3. end of multiple rec
