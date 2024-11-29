

## cnn卷积神经网络图像分类

## 以下可定义GPU设备进行处理训练和测试

### 加载数据集
```python
import torch

def load_data():
    import PIL.Image
    import numpy as np
    import os

    xs = []
    ys = []

    #遍历文件夹下的所有文件
    for filename in os.listdir('data/cifar10'):

        #只要图片,过滤一些无关的文件
        if not filename.endswith('.jpg'):
            continue

        #读取图片信息
        x = PIL.Image.open('data/cifar10/%s' % filename)

        #转矩阵,数值压缩到0-1之间
        x = torch.FloatTensor(np.array(x)) / 255

        #变形,把通道放前面
        #[32, 32, 3] -> [3, 32, 32]
        x = x.permute(2, 0, 1)

        #y来自文件名的第一个字符
        y = int(filename[0])

        xs.append(x)
        ys.append(y)

    return xs, ys


xs, ys = load_data()

len(xs), len(ys), xs[0].shape, ys[0]
```

### 封装数据集
```python
class Dataset(torch.utils.data.Dataset):

    def __len__(self):
        return len(xs)

    def __getitem__(self, i):
        return xs[i], ys[i]


dataset = Dataset()

x, y = dataset[0]

len(dataset), x.shape, y
```

### 定义loader
```python
#数据加载器
loader = torch.utils.data.DataLoader(dataset=dataset,
                                     batch_size=8,
                                     shuffle=True,
                                     drop_last=True)

x, y = next(iter(loader))

len(loader), x.shape, y
```

### 卷积层计算

![image.png](https://s2.loli.net/2024/05/13/IsejB8vm6hGYuAF.png)

### 池化层计算

![image.png](https://s2.loli.net/2024/05/13/DyASPtTVBaUREe1.png)

### 一般CNN模型

![image.png](https://s2.loli.net/2024/05/13/UErN74xXY5BzdZf.png)

通过卷积和降采样图像的二维数据，最后一层为全连接层，使得一个图像抽取成一个像素（一维向量），其通道数有几百上千

```python
#cnn神经网络
class Model(torch.nn.Module):

    def __init__(self):
        super().__init__()

        #520的卷积层
        self.cnn1 = torch.nn.Conv2d(in_channels=3,
                                    out_channels=16,
                                    kernel_size=5,
                                    stride=2,
                                    padding=0)

        #311的卷积层
        self.cnn2 = torch.nn.Conv2d(in_channels=16,
                                    out_channels=32,
                                    kernel_size=3,
                                    stride=1,
                                    padding=1)

        #710的卷积层
        self.cnn3 = torch.nn.Conv2d(in_channels=32,
                                    out_channels=128,
                                    kernel_size=7,
                                    stride=1,
                                    padding=0)

        #池化层
        self.pool = torch.nn.MaxPool2d(kernel_size=2, stride=2)

        #激活函数
        self.relu = torch.nn.ReLU()

        #线性输出层
        self.fc = torch.nn.Linear(in_features=128, out_features=10)

    def forward(self, x):

        #第一次卷积,形状变化可以推演
        #[8, 3, 32, 32] -> [8, 16, 14, 14]
        x = self.cnn1(x)
        x = self.relu(x)

        #第二次卷积,因为是311的卷积,所以尺寸不变
        #[8, 16, 14, 14] -> [8, 32, 14, 14]
        x = self.cnn2(x)
        x = self.relu(x)

        #池化,尺寸减半
        #[8, 32, 14, 14] -> [8, 32, 7, 7]
        x = self.pool(x)

        #第三次卷积,因为核心是7,所以只有一步计算
        #[8, 32, 7, 7] -> [8, 128, 1, 1]
        x = self.cnn3(x)
        x = self.relu(x)

        #展平,便于线性计算,也相当于把图像变成向量
        #[8, 128, 1, 1] -> [8, 128]
        x = x.flatten(start_dim=1)

        #线性计算输出
        #[8, 128] -> [8, 10]
        return self.fc(x)


model = Model()

model(torch.randn(8, 3, 32, 32)).shape
```


### 训练
```python
def train():
    optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
    loss_fun = torch.nn.CrossEntropyLoss()
    model.train()

    for epoch in range(5):
        for i, (x, y) in enumerate(loader):
            out = model(x)
            loss = loss_fun(out, y)

            loss.backward()
            optimizer.step()
            optimizer.zero_grad()

            if i % 2000 == 0:
                acc = (out.argmax(dim=1) == y).sum().item() / len(y)
                print(epoch, i, loss.item(), acc)

    torch.save(model, 'model/5.model')


train()
```

### 测试
```python

#测试
@torch.no_grad()
def test():
    model = torch.load('model/5.model')
    model.eval()

    correct = 0
    total = 0
    for i in range(100):
        x, y = next(iter(loader))

        out = model(x).argmax(dim=1)

        correct += (out == y).sum().item()
        total += len(y)

    print(correct / total)


test()
```


