不知道这个NCCL到底从哪里来的灵感

难道是高级的mpi，或者是受到mpi的启发

1. all-reduce
2. all-gather
3. reduce-scatter
4. reduce
5. broadcast

---

这几个算子是像是mpi的阉割版。

---
需要高级的版本的GPU

NCCL requires at least CUDA 7.0 and Kepler or newer GPUs. Best performance is achieved when all GPUs are located on a common PCIe root complex, but multi-socket configurations are also supported.

Note: NCCL may also work with CUDA 6.5, but this is an untested configuration.

---

```c++

#include <nccl.h>

typedef struct {
  double* sendBuff;
  double* recvBuff;
  int size;
  cudaStream_t stream;
} PerThreadData;

int main(int argc, char* argv[])
{
  int nGPUs;
  cudaGetDeviceCount(&nGPUs);
  ncclComm_t* comms = (ncclComm_t*)malloc(sizeof(ncclComm_t)*nGPUs);
  ncclCommInitAll(comms, nGPUs); // initialize communicator
                                // One communicator per process

  PerThreadData* data;

  ... // Allocate data and issue work to each GPU's
      // perDevStream to populate the sendBuffs.

  for(int i=0; i<nGPUs; ++i) {
    cudaSetDevice(i); // Correct device must be set
                      // prior to each collective call.
    ncclAllReduce(data[i].sendBuff, data[i].recvBuff, size,
        ncclDouble, ncclSum, comms[i], data[i].stream);
  }

  ... // Issue work into data[*].stream to consume buffers, etc.
}

```

只能说这个代码就是mpi的用法啊。

随着代码看的多了，终于也明白了一些东西。

比如这里的ncclCommInitAll 函数的调用，我看就是一个变量的初始化，或者说是环境的初始化问题。
