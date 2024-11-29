

## pytorch学习资源来源
b站
https://www.bilibili.com/video/BV1hE411t7RN/?spm_id_from=333.999.0.0

# 基本概念

## 两大函数

### dir():打开
工具箱以及工具箱中分隔区的东西

#### torch安装成功
```python
import torch
torch.cuda.is_available()
```
返回true，则表示安装成功，cuda为N卡的GPU版本

#### 查看工具箱
```python
print(dir(torch))
print(dir(torch.cuda))
print(dir(torch.cuda.is_available))
```
函数表示为``__xxx__``，函数不可分割，为内置函数

### help():帮助
每个工具是如何使用的
```python
print(help(torch.cuda.is_available))
```


## pytorch加载数据认识
### Dataset
提供一种方式获取数据及其label
1. 如何获取每一个数据及其label
2. 告诉我们总共有多少个数据
### Dataloader
为网络提供不同的数据形式

# 蚂蚁和蜜蜂练手项目

## 加载数据集
查看learning.ipynb文件内容

# TensorBoard使用
## 查看训练过程中变化
详细见tensorboard.ipynb文件

## 注意
如果在vscode的cmd中无法成功打开
```bash
tensorboard --logdir=logs
```
则将logs的绝对路径进行复制
```bash
tensorboard --logdir=E:\Study\hexoBlog\blog-demo\blog-01\source\_posts\Pytorch\logs
```

还有可能打不开，更换端口地址
```bash
tensorboard --logdir=E:\Study\hexoBlog\blog-demo\blog-01\source\_posts\Pytorch\logs --port=6007
```

## 加载图片
add_image()

# transforms图片进行变换
见transforms.ipynb文件
使用transforms的工具对图片进行转换处理，输出结果

1. transforms如何使用
创建具体的工具，使用工具（输入、输出）

2. 为什么需要Tensor数据类型
其包括神经网络理论的基础参数

## 常见transforms
1. ToTensor

2. Normalize归一化

$input[channel]=(input[channel] - mean[channel])/std[channel]$

3. Resize 大小重新设置

4. Compose - resize -2 串联多个图片变换的操作

5. RandomCrop

### 总结
输入和输出类型、方法需要什么参数
使用print 和 print(type())

# torchvision数据集使用
见torchvision_dataset.ipynb
与transforms进行结合使用

# dataloader数据加载器
见dataloader.ipynb文件
位于torch.utils.data.DataLoader()包

如果在getitem()中返回return img , target
若dataloader(batch_size = 4)，则有
```python
img0, target0 = dataset[0]
img1, target1 = dataset[1]
img2, target2 = dataset[2]
img3, target3 = dataset[3]
```

# 神经网络基本骨架，nn.Module使用
见nn_module.ipynb文件

# 非线性激活Non-linear Activations
见nn_relu.ipynb文件

## Linear

## flatten

# torch.nn中的Containers--Sequential
序列容器
见nn_seq.ipynb文件

# 线性层
![线性层神经网络](https://s2.loli.net/2024/04/16/kZpOd6ejABm19qH.png)
见nn_linear.ipynb

## reshape的含义
```python
import torch
a=torch.tensor([[[1,2,3],[4,5,6]],
                [[7,8,9],[10,11,12]]])
print("a的shape:",a.shape)
b=torch.reshape(a,((4,3,1)))
print("b:",b)
print("b的shape:",b.shape)
```
如果reshape的值有-1的话，那么Numpy会根据所给的新的shape的信息（newshape），自动计算补足shape缺的值

https://blog.csdn.net/weixin_47156261/article/details/116596490

## inplace (true OR false)
需不需要对原来的变量进行变换

# 损失函数loss

1. 计算实际输出与目标之间的差距
2. 更新输出提供依据（反向传播），backward自动计算梯度

## L1Loss
$$
\ell(x,y)=L=\{l_1,\ldots,l_N\}^\top,\quad l_n=|x_n-y_n|
$$


## torch.nn.MSELoss() 均方误差损失
$$\ell(x,y)=L=\{l_1,\ldots,l_N\}^\top,\quad l_n=\left(x_n-y_n\right)^2,$$
MSE = (0+0+2^2)/3

## CrossEntropyLoss() 交叉熵损失

$$\mathrm{loss}(x,class)=-\log\left(\frac{\exp(x[class])}{\sum_j\exp(x[j])}\right)=-x[class]+\log\left(\sum_j\exp(x[j])\right)$$

### 例子(损失函数和反向传播)
分类问题

# 优化器optimizer
优化器和梯度对参数调整，使得损失或误差降低
见nn_optim，注意基本结构，优化器算法

# 网络模型的预训练和使用修改
见model_pretrained.py文件
加载现有的网络和数据集

# 网络模型保存和读取
见model_save.ipynb 和 model_read.ipynb文件
# 完整模型训练过程
1. 准备数据集
2. 数据集长度
3. 利用DataLoader加载数据集
4. 搭建神经网络（新建一个网络，直接调用model.py----from model import *），main函数中测试网络的完整性
5. 创建网络模型
6. 损失函数
7. 优化器
8. 设置训练网络参数
添加tensorboard
9. 测试步骤开始


## 正确率分析
二分类问题
```python
import torch

outputs = torch.tensor([[0.1, 0.2],
                        [0.3, 0.4]])
# 横向行最大
print(outputs.argmax(1))
# 纵向列最大
print(outputs.argmax(0))
preds = outputs.argmax(1)
targets = torch.tensor([0, 1])
print((preds == targets).sum())
```

## 细节问题
训练和测试模型的函数，并不一定调用。仅对特定的层有作用
```python
lucien.train() 
lucien.eval()
```

# 利用GPU训练
见train_gpu_1.ipynb文件
1. 网络模型
2. 数据(input，label)
3. 损失函数loss（一般不需要）

## .cuda
```python
if torch.cuda.is_available()
```
```python
import time
start_time = time.time()
```

## .to(device)
定义训练的设备
```python
device = torch.device("cpu")
device = torch.device("cuda:0") #0为第一张显卡
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

导入torch库以后，调用下面的函数，如果打印True就说明安装成功。

```python
import torch
print(torch.cuda.is_available())
```

定义一个cuda设备

```python
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
print(device)
```

将网络的参数设置为cuda张量

```python
net = Net().to(device)
```

将输入和输出设置为cuda张量

```python
input, label = input.to(device), label.to(device)
```

# 完整模型验证过程
见test.ipynb文件（图像）
利用已经训练好的模型，给它提供输入

1. 获取路径
2. ToTensor类型，且进行Resize
3. 网络模型
4. 加载训练后的模型

## 注意报错信息
Input type (torch.FloatTensor) and weight type (torch.cuda.FloatTensor) should be the same
输入类型是CPU（torch.FloatTensor），而参数类型是GPU（torch.cuda.FloatTensor）

cpu或gpu训练后的参数类型，则验证时需要使用对应的类型。比如gpu训练后的模型，使用cpu验证时：

```python
model = torch.load("lucien_x_gpu.pth", map_location=torch.device('cpu'))
```

## pytorch工具函数

1. 工具函数 
```python
#虚拟测试数据
x = torch.randn(2, 3)
torch.nn.functional.relu(x)
```

2. Dropout层

以一定概率将神经网络中参数进行归零
```python
#虚拟测试数据
x = torch.randn(2, 3)
#特征概率归零,缓解神经网络过拟合
dropout = torch.nn.Dropout(p=0.5)
```

3. 保存模型参数
```python
#虚拟测试模型
model = torch.nn.Linear(3, 2)
#保存参数到磁盘
torch.save(model.state_dict(), 'temp.model')
#加载模型参数
model.load_state_dict(torch.load('temp.model'))
```

4. 手动初始化模型参数
```python
#虚拟测试模型
model = torch.nn.Linear(2, 3)
#手动初始化参数

#a-b之间的均匀分布
torch.nn.init.uniform_(model.state_dict()['bias'], a=5.0, b=6.0)
#正态分布
torch.nn.init.normal_(model.state_dict()['weight'], mean=7.0, std=0.0)

model.state_dict()
```

5. 调整学习率
```python
#虚拟测试模型
model = torch.nn.Linear(2, 3)

#虚拟优化器
optimizer = torch.optim.SGD(model.parameters(), lr=100.0)

#查看lr
optimizer.param_groups[-1]['lr']

#创建lr调整器,每2步把lr折半
scheduler = torch.optim.lr_scheduler.StepLR(optimizer, step_size=2, gamma=0.5)

#虚拟10次训练
for i in range(10):
    optimizer.step()
    scheduler.step()

    #查看lr
    print(i, optimizer.param_groups[-1]['lr'])
```

6. Norm层
```python
#虚拟文字数据,2句话,每句话2个词,每个词用5个数字表示
text = torch.arange(2 * 2 * 5).reshape(2, 2, 5).float()

#LayerNorm,一般用来处理序列数据,声音或者文字
torch.nn.LayerNorm(normalized_shape=5, elementwise_affine=False)(text)

#虚拟图像数据,2张图,单通道,5*5尺寸
image = torch.arange(2 * 1 * 5 * 5).reshape(2, 1, 5, 5).float()

#BatchNorm2d,一般用来处理图像数据
torch.nn.BatchNorm2d(num_features=1, affine=False)(image)
```