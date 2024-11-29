

## 基于torchvision的CV迁移学习

### 定义数据集的初始化部分
```python
import torchvision
import torch

#定义计算设备,优先使用gpu
device = 'cuda' if torch.cuda.is_available() else 'cpu'
#定义数据集
class Dataset(torch.utils.data.Dataset):

    def __init__(self, train):

        #在线加载数据集
        #更多数据集:https://pytorch.org/vision/stable/datasets.html
        self.data = torchvision.datasets.CIFAR100(root='data',
                                                  train=train,
                                                  download=True)

        #更多数据增强:https://pytorch.org/vision/stable/transforms.html
        self.compose = torchvision.transforms.Compose([

            #原本是32*32的,缩放到300*300,这是为了适应预训练模型的习惯,便于它抽取图像特征
            torchvision.transforms.Resize(300),

            #随机左右翻转,这是一种图像增强,很显然,左右翻转不影响图像的分类结果
            torchvision.transforms.RandomHorizontalFlip(p=0.5),

            #图像转矩阵数据,值域是0-1之间
            torchvision.transforms.ToTensor(),

            #让图像的3个通道的数据分别服从3个正态分布,这3分数据是从一个大的数据集上统计得出的
            #投影也是为了适应预训练模型的习惯
            torchvision.transforms.Normalize(mean=[0.485, 0.456, 0.406],
                                             std=[0.229, 0.224, 0.225]),
        ])

    def __len__(self):
        return len(self.data)

    def __getitem__(self, i):
        #取数据
        x, y = self.data[i]

        #应用compose,图像转数据
        x = self.compose(x)

        return x, y


dataset = Dataset(train=True)

x, y = dataset[0]

len(dataset), x.shape, y
```

### 定义loader数据加载器
```python
import torch


#每次从loader获取一批数据时回回调,可以在这里做一些数据整理的工作
#这里写的只是个例子,事实上这个回调函数什么也没干..
def collate_fn(data):
    #取数据
    x = [i[0] for i in data]
    y = [i[1] for i in data]

    #比如可以手动转换数据格式
    x = torch.stack(x)
    y = torch.LongTensor(y)

    return x, y


#数据加载器
loader = torch.utils.data.DataLoader(dataset=dataset,
                                     batch_size=8,
                                     shuffle=True,
                                     drop_last=True,
                                     collate_fn=collate_fn)

x, y = next(iter(loader))

len(loader), x.shape, y
```

### 迁移学习

核心思想：复用以前训练好的模型，负责图像数据的特征抽取

![迁移学习过程](https://s2.loli.net/2024/05/14/1tmClFgHyYaEpwd.png)

### 定义神经网络模型

```python
#定义模型
class Model(torch.nn.Module):

    def __init__(self):
        super().__init__()

        #加载预训练模型
        #更多模型:https://pytorch.org/vision/stable/models.html#table-of-all-available-classification-weights
        pretrained = torchvision.models.efficientnet_v2_s(
            weights=torchvision.models.EfficientNet_V2_S_Weights.IMAGENET1K_V1)

        #重新组装模型,只要特征抽取部分
        pretrained = torch.nn.Sequential(
            pretrained.features,
            pretrained.avgpool,
            torch.nn.Flatten(start_dim=1),
        )

        #锁定参数,不训练
        for param in pretrained.parameters():
            param.requires_grad_(False)

        pretrained.eval()
        self.pretrained = pretrained

        #线性输出层,这部分是要重新训练的
        self.fc = torch.nn.Sequential(
            torch.nn.Linear(1280, 256),
            torch.nn.ReLU(),
            torch.nn.Linear(256, 256),
            torch.nn.ReLU(),
            torch.nn.Linear(256, 100),
        )

    def forward(self, x):
        #调用预训练模型抽取参数,因为预训练模型是不训练的,所以这里不需要计算梯度
        with torch.no_grad():
            #[8, 3, 300, 300] -> [8, 1280]
            x = self.pretrained(x)

        #计算线性输出
        #[8, 1280] -> [8, 100]
        return self.fc(x)


model = Model()

x = torch.randn(8, 3, 300, 300)

model.pretrained(x).shape, model(x).shape
```

### 训练模型
```python
def train():
    #注意这里的参数列表,只包括要训练的参数即可
    optimizer = torch.optim.Adam(model.fc.parameters(), lr=1e-3)
    loss_fun = torch.nn.CrossEntropyLoss()
    model.fc.train()

    model.to(device)

    print('device=', device)

    for i, (x, y) in enumerate(loader):
        #如果使用gpu,数据要搬运到显存里
        x = x.to(device)
        y = y.to(device)

        out = model(x)
        loss = loss_fun(out, y)

        loss.backward()
        optimizer.step()
        optimizer.zero_grad()

        if i % 500 == 0:
            acc = (out.argmax(dim=1) == y).sum().item() / len(y)
            print(i, loss.item(), acc)

    #保存模型,只保存训练的部分即可
    torch.save(model.fc.to('cuda'), 'model/8.model')


train()
```

### 测试
```python
@torch.no_grad()
def test():

    #加载保存的模型
    model.fc = torch.load('model/8.model')
    model.fc.eval()

    #加载测试数据集,共10000条数据
    loader_test = torch.utils.data.DataLoader(dataset=Dataset(train=False),
                                              batch_size=8,
                                              shuffle=True,
                                              drop_last=True)

    correct = 0
    total = 0
    for i in range(100):
        x, y = next(iter(loader_test))
        x = x.to(device)
        y = y.to(device)
        out = model(x).argmax(dim=1)

        correct += (out == y).sum().item()
        total += len(y)

    print(correct / total)


test()
```




