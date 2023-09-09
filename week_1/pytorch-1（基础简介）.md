# pytorch—1

# 一.第一步：读入数据

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled.png)

> torch.utils.data.Dataset
torch.utils.data.DataLoader
> 

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%201.png)

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%202.png)

dataloader调用MyDataset类的函数，比如会调用五次getitem

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%203.png)

# 二.数据的处理存储（tensors）

## 1.tensor的形状

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%204.png)

## 2.tensor的建立

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%205.png)

移除维度

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%206.png)

合并tensor

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%207.png)

## 3.tensor的设备选择

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%208.png)

默认CPU，使用to函数

1. 查看设备

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%209.png)

## 4.tensor的梯度计算

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2010.png)

# 三.第二步—定义神经网络

> torch.nn.Module
> 

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2011.png)

## 1.torch.nn线性函数

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2012.png)

线性模型输入32，输出64理解：

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2013.png)

即W为64*32，X为32*1

## 2.torch.nn非线性激活函数

> nn.Sigmoid]()
nn.ReLU()
> 

## 3.例子

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2014.png)

左右两边是否有Sequential是等价的

# 四.第三步—定义损失函数

## 常用的损失函数

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2015.png)

# 五.第四步—选择优化算法

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2016.png)

# 六—整个处理过程合并

## 1.前置流程：

大致流程：

读入数据到——将数据放到DataLoader——定义模型并将模型放入对应的运算设备——设置损失函数——设置优化器

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2017.png)

## 2.训练轮次设置：

model.train()设置为训练模式

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2018.png)

## 3.验证轮次设置

model.eval()设置为验证模式

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2019.png)

## 4.测试轮次设置

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2020.png)

## 5.保存/加载模型

![Untitled](pytorch%E2%80%941%201a878ec3d0a94fc6a20f7799c6812779/Untitled%2021.png)