# 分布式并行训练 (GPU)

<!-- TOC -->

- [分布式并行训练 (GPU)](#分布式并行训练-gpu)
    - [概述](#概述)
    - [准备环节](#准备环节)
        - [下载数据集](#下载数据集)
        - [配置分布式环境](#配置分布式环境)
        - [调用集合通信库](#调用集合通信库)
    - [数据并行模式加载数据集](#数据并行模式加载数据集)
    - [定义网络](#定义网络)
    - [运行脚本](#运行脚本)

<!-- /TOC -->

<a href="https://gitee.com/mindspore/docs/blob/master/tutorials/source_zh_cn/advanced_use/distributed_training_gpu.md" target="_blank"><img src="../_static/logo_source.png"></a>

## 概述

本篇教程我们主要讲解，如何在GPU硬件平台上，利用MindSpore的数据并行及自动并行模式训练ResNet-50网络。

## 准备环节

### 下载数据集

本样例采用`CIFAR-10`数据集，数据集的下载以及加载方式和Ascend 910 AI处理器一致。

> 数据集的下载和加载方式参考：
>
> <https://www.mindspore.cn/tutorial/zh-CN/master/advanced_use/distributed_training_ascend.html>。

### 配置分布式环境

- `OpenMPI-3.1.5`：MindSpore采用的多进程通信库。

  > OpenMPI-3.1.5源码下载地址：<https://www.open-mpi.org/software/ompi/v3.1/>，选择`openmpi-3.1.5.tar.gz`下载。
  >
  > 参考OpenMPI官网教程安装：<https://www.open-mpi.org/faq/?category=building#easy-build>。

- `NCCL-2.4.8`：Nvidia集合通信库。

  > NCCL-2.4.8下载地址：<https://developer.nvidia.com/nccl/nccl-legacy-downloads>。
  >
  > 参考NCCL官网教程安装：<https://docs.nvidia.com/deeplearning/nccl/install-guide/index.html#debian>。

### 调用集合通信库

在GPU硬件平台上，MindSpore分布式并行训练的通信使用的是NCCL。

> GPU平台上，MindSpore暂不支持用户进行：
>
> `get_local_rank`、`get_local_size`、`get_world_rank_from_group_rank`、`get_group_rank_from_world_rank`、`create_group`操作。

下面是调用集合通信库的代码样例：

```python
from mindspore import context
from mindspore.communication.management import init

if __name__ == "__main__":
    context.set_context(mode=context.GRAPH_MODE, device_target="GPU")
    init("nccl")
    ...   
```

其中，

- `mode=context.GRAPH_MODE`：使用分布式训练需要指定运行模式为图模式（PyNative模式不支持并行）。
- `init("nccl")`：使能NCCL通信，并完成分布式训练初始化操作。

## 定义网络

在GPU硬件平台上，网络的定义和Ascend 910 AI处理器一致。

> 网络、优化器、损失函数的定义参考：<https://www.mindspore.cn/tutorial/zh-CN/master/advanced_use/distributed_training_ascend.html>。

## 运行脚本

在GPU硬件平台上，MindSpore采用OpenMPI的`mpirun`进行分布式训练。下面以使用8张卡的分布式训练脚本为例，演示如何运行脚本：

```bash
#!/bin/bash

DATA_PATH=$1
export DATA_PATH=${DATA_PATH}

rm -rf device
mkdir device
cp ./resnet50_distributed_training.py ./resnet.py ./device
cd ./device
echo "start training"
mpirun -n 8 pytest -s -v ./resnet50_distributed_training.py > train.log 2>&1 &
```

脚本需要传入变量`DATA_PATH`，表示数据集的路径，resnet50_distributed_training.py是适配GPU后的Python文件。日志文件保存`device`目录下，关于Loss部分结果保存在`train.log`中。将loss值 `grep`出来后，示例如下：

```
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
epoch: 1 step: 1, loss is 2.3025854
```
