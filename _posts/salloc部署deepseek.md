---
layout：post
title: salloc部署deepseek
date: 2025-03-02 20:03:18
---





该文件是对在华科hpc使用salloc部署deepseek的一个补充

> https://mp.weixin.qq.com/s/E8jV450Y42YbTBfPxR813g



- 申请gpu计算节点：用salloc方式申请公共集群计算资源。

```
# 指定gpu分区，申请1张gpu卡、8个cpu核心,使用项目账户扣费
salloc -p gpu --gres=gpu:1 -n 8 --comment=your_project_name
```

- 运行Ollama服务：申请好计算资源后，先登录到计算节点。例如，分配了gpu4

```
ssh gpu4
```

- 设置相应环境变量，并启动 ollama 服务。

```
# 启动一个session
screen -S serve
export OLLAMA_MODELS="/opt/app/ollama/0.5.7/models"
export PATH=$PATH:/opt/app/ollama/0.5.7/bin
ollama serve
# 稍作等待，确认ollama 开始工作
# Ctrl + a, d 离开session
```

* 这时候可以用`nvidia-smi`确认一下显存占用

- 运行DeepSeek模型：打开一个新的ssh窗口，并在计算节点运行下面的命令，开始对话。

```
export PATH=$PATH:/opt/app/ollama/0.5.7/bin
ollama list
ollama run deepseek-r1:32b
```

* 开始对话，可以研究一下怎么保存输出到文件，记得记录输入和输出对
* 关闭salloc分配的节点：退出ssh连接到的资源

```
Ctrl + D
```

* 关闭资源：以下命令关闭你用户名下的所有资源，注意不是引号

```
scancel -u `whoami`
```

* 可以用`squeue`确认，也可以查看企业微信的通知