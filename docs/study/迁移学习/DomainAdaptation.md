

# 基本概念
将不同领域（如两个不同的数据集）的数据特征映射到同一个特征空间，这样可利用其它领域数据来增强目标领域训练。

## 域适应的引入
在经典的机器学习模型中，我们习惯性假设训练数据集和目标训练集有着相同的概率分布。而在现实生活中，这种约束性假设很难实现。当训练数据集和测试集有着巨大差异时，很容易出现过拟合的现象，使得训练的模型在测试集上表现不理想。

假设训练数据集是各种英短蓝猫，而想训练得到可以区分田园猫的模型，该模型相比于英短蓝猫识别情况性能会下降。当训练数据集和测试数据集分布不一致的情况下，通过在训练数据集上按经验误差最小准则训练得到的模型在测试数据集上性能不佳，因此，我们引入了域适应技术。

我们有时在一个感兴趣的领域中有一个分类任务，但是在另一个感兴趣的领域中我们只有足够的训练数据，在另一个领域中，后者可能位于不同的特征空间或遵循不同的数据分布。在这种情况下，如果成功地进行知识迁移，就可以避免昂贵的数据标记工作，从而大大提高学习性能。

## 域适应方法分类
域适应中有两个基础概念：源域(Source Domain)和目标域（Target Domain）。源域中有着丰富的监督学习信息；目标域表示测试集所在的领域，通常无标签或者只含有少量的标签。源域和目标域往往是同一类任务，但是分布不同。

几种不同的域适应方法：

1. 样本自适应：将源域中样本重采样，使其分布趋近于目标域分布；从源域中找出那些长的最像目标域的样本，让他们带着高权重加入目标域的数据学习。

2. 特征层面自适应：与一般的将源域映射到目标域方法不同，该类方法将源域和目标域投影到公共特征子空间，进而使得源域上的训练知识可以直接应用于目标域；

3. 模型层面自适应：对源域误差函数进行修改，考虑到目标与的误差。


见TransferLearning文件夹，以下为具体的应用例子。将训练集中10个分组的图片按一定的数量比例划分，训练集train、测试集test、验证集valid

```python
# 环境导入
import os
import torch
os.environ['KMP_DUPLICATE_LIB_OK'] = 'TRUE'
import torch, torchvision
from torchvision import datasets, models, transforms
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader
import time
from torchsummary import summary
import numpy as np
import matplotlib.pyplot as plt
import os
from PIL import Image
 
# Applying Transforms to the Data
# 对数据进行transforms处理，图片裁剪并变换相应图片格式和标准化
image_transforms = { 
    'train': transforms.Compose([
        transforms.RandomResizedCrop(size=256, scale=(0.8, 1.0)),
        transforms.RandomRotation(degrees=15),
        transforms.RandomHorizontalFlip(),
        transforms.CenterCrop(size=224),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406],
                             [0.229, 0.224, 0.225])
    ]),
    'valid': transforms.Compose([
        transforms.Resize(size=256),
        transforms.CenterCrop(size=224),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406],
                             [0.229, 0.224, 0.225])
    ]),
    'test': transforms.Compose([
        transforms.Resize(size=256),
        transforms.CenterCrop(size=224),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406],
                             [0.229, 0.224, 0.225])
    ])
}
# Load the Data
 
# Set train and valid directory paths
 
dataset = '"E:\Study\TransferLearning"'
 
train_directory = os.path.join(dataset, 'train')
valid_directory = os.path.join(dataset, 'valid')
test_directory = os.path.join(dataset, 'test')
 
# Batch size
bs = 32
 
# Number of classes
num_classes = len(os.listdir(valid_directory))  #10
print(num_classes)
 
# Load Data from folders
data = {
    'train': datasets.ImageFolder(root=train_directory, transform=image_transforms['train']),
    'valid': datasets.ImageFolder(root=valid_directory, transform=image_transforms['valid']),
    'test': datasets.ImageFolder(root=test_directory, transform=image_transforms['test'])
}
 
# Get a mapping of the indices to the class names, in order to see the output classes of the test images.
# 对各种类的进行切分，输出各种类的名称
idx_to_class = {v: k for k, v in data['train'].class_to_idx.items()}
print(idx_to_class)
 
# Size of Data, to be used for calculating Average Loss and Accuracy
# 训练、验证和测试数据集的大小，以计算平均损失和精确度
train_data_size = len(data['train'])
valid_data_size = len(data['valid'])
test_data_size = len(data['test'])
 
# Create iterators for the Data loaded using DataLoader module
# 使用DataLoader对加载数据的数据进行迭代处理
train_data_loader = DataLoader(data['train'], batch_size=bs, shuffle=True)
valid_data_loader = DataLoader(data['valid'], batch_size=bs, shuffle=True)
test_data_loader = DataLoader(data['test'], batch_size=bs, shuffle=True)

# 若存在Gpu则使用
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
print(train_data_size, valid_data_size, test_data_size)

# Load pretrained ResNet34 Model
# 加载预训练的ResNet34模型
resnet34 = models.resnet34(pretrained=True)
resnet34 = resnet34.to('cuda:0')

# Freeze model parameters
# 释放模型参数
for param in resnet34.parameters():
    param.requires_grad = False

# Change the final layer of ResNet34 Model for Transfer Learning
# 改变ResNet34模型的最后一层
fc_inputs = resnet34.fc.in_features
 
resnet34.fc = nn.Sequential(
    nn.Linear(fc_inputs, 256),
    nn.ReLU(),
    nn.Dropout(0.4),
    nn.Linear(256, 10), 
    nn.LogSoftmax(dim=1)
 ) # For using NLLLoss()

# Convert model to be used on GPU
resnet34 = resnet34.to('cuda:0')

# Define Optimizer and Loss Function
loss_func = nn.NLLLoss()
optimizer = optim.Adam(resnet34.parameters())

def train_and_validate(model, loss_criterion, optimizer, epochs=25):
    '''
    Function to train and validate
    Parameters
        :param model: Model to train and validate
        :param loss_criterion: Loss Criterion to minimize
        :param optimizer: Optimizer for computing gradients
        :param epochs: Number of epochs (default=25)
  
    Returns
        model: Trained Model with best validation accuracy
        history: (dict object): Having training loss, accuracy and validation loss, accuracy
    '''
    
    start = time.time()
    history = []
    best_loss = 100000.0
    best_acc = 0.0
    best_epoch = 0
 
    for epoch in range(epochs):
        epoch_start = time.time()
        print("Epoch: {}/{}".format(epoch+1, epochs))
        
        # Set to training mode
        model.train()
        
        # Loss and Accuracy within the epoch
        train_loss = 0.0
        train_acc = 0.0
        
        valid_loss = 0.0
        valid_acc = 0.0
        
        for i, (inputs, labels) in enumerate(train_data_loader):
            
            # 输入数据和标签定义在gpu设备上
            inputs = inputs.to(device)
            labels = labels.to(device)
            
            # Clean existing gradients
            optimizer.zero_grad()
            
            # Forward pass - compute outputs on input data using the model
            # 前向计算模型的输出
            outputs = model(inputs)
            
            # Compute loss
            loss = loss_criterion(outputs, labels)
            
            # Backpropagate the gradients
            loss.backward()
            
            # Update the parameters
            optimizer.step()
            
            # Compute the total loss for the batch and add it to train_loss
            train_loss += loss.item() * inputs.size(0)
            
            # Compute the accuracy
            ret, predictions = torch.max(outputs.data, 1)
            correct_counts = predictions.eq(labels.data.view_as(predictions))
            
            # Convert correct_counts to float and then compute the mean
            acc = torch.mean(correct_counts.type(torch.FloatTensor))
            
            # Compute total accuracy in the whole batch and add to train_acc
            train_acc += acc.item() * inputs.size(0)
            
            # 计算训练中每一batch下的训练损失和精确度
            print("Batch number: {:03d}, Training: Loss: {:.4f}, Accuracy: {:.4f}".format(i, loss.item(), acc.item()))
 
            
        # Validation - No gradient tracking needed
        # 验证处理，不需要梯度更新
        with torch.no_grad():
 
            # Set to evaluation mode
            model.eval()
 
            # Validation loop
            for j, (inputs, labels) in enumerate(valid_data_loader):
                inputs = inputs.to(device)
                labels = labels.to(device)
 
                # Forward pass - compute outputs on input data using the model
                outputs = model(inputs)
 
                # Compute loss
                loss = loss_criterion(outputs, labels)
 
                # Compute the total loss for the batch and add it to valid_loss
                valid_loss += loss.item() * inputs.size(0)
 
                # Calculate validation accuracy
                ret, predictions = torch.max(outputs.data, 1)
                correct_counts = predictions.eq(labels.data.view_as(predictions))
 
                # Convert correct_counts to float and then compute the mean
                acc = torch.mean(correct_counts.type(torch.FloatTensor))
 
                # Compute total accuracy in the whole batch and add to valid_acc
                valid_acc += acc.item() * inputs.size(0)

                # 验证中每一batch的训练损失和精确度
                print("Validation Batch number: {:03d}, Validation: Loss: {:.4f}, Accuracy: {:.4f}".format(j, loss.item(), acc.item()))

        # 保存最小损失下的epoch
        if valid_loss < best_loss:
            best_loss = valid_loss
            best_epoch = epoch 

        # Find average training loss and training accuracy
        avg_train_loss = train_loss/train_data_size 
        avg_train_acc = train_acc/train_data_size
 
        # Find average training loss and training accuracy
        avg_valid_loss = valid_loss/valid_data_size 
        avg_valid_acc = valid_acc/valid_data_size
 
        history.append([avg_train_loss, avg_valid_loss, avg_train_acc, avg_valid_acc])
                
        epoch_end = time.time()
    
        print("Epoch : {:03d}, Training: Loss: {:.4f}, Accuracy: {:.4f}%, \n\t\tValidation : Loss : {:.4f}, Accuracy: {:.4f}%, Time: {:.4f}s".format(epoch, avg_train_loss, avg_train_acc*100, avg_valid_loss, avg_valid_acc*100, epoch_end-epoch_start))
        
        # Save if the model has best accuracy till now
        # 保存模型
        torch.save(model, r'TransferLearning\_model_'+str(epoch)+'.pt')
            
    return model, history, best_epoch

# Print the model to be trained
# 打印整个被训练的模型
# summary(resnet34, input_size=(3, 224, 224), batch_size=bs, device='cuda')
 
# Train the model for 30 epochs
num_epochs = 30
trained_model, history , best_epoch = train_and_validate(resnet34, loss_func, optimizer, num_epochs)
# 保存模型训练后的历史记录(平均训练损失、精度和平均验证损失、精度)
torch.save(history, r'TransferLearning\_history.pt')

# 绘制训练后的损失和精度
history = np.array(history)
plt.plot(history[:,0:2])
plt.legend(['Tr Loss', 'Val Loss'])
plt.xlabel('Epoch Number')
plt.ylabel('Loss')
plt.ylim(0,1)
plt.savefig(r'TransferLearning\_loss_curve.png')
plt.show()
plt.plot(history[:,2:4])
plt.legend(['Tr Accuracy', 'Val Accuracy'])
plt.xlabel('Epoch Number')
plt.ylabel('Accuracy')
plt.ylim(0,1)
plt.savefig(r'TransferLearning\_accuracy_curve.png')
plt.show()

# 计算测试集的损失，输入参数为预训练的模型和损失函数
def computeTestSetAccuracy(model, loss_criterion):
    '''
    Function to compute the accuracy on the test set
    Parameters
        :param model: Model to test
        :param loss_criterion: Loss Criterion to minimize
    '''
    # 定义设备
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    
    # 初始测试精度和损失
    test_acc = 0.0
    test_loss = 0.0
 
    # Validation - No gradient tracking needed
    # 验证无梯度更新
    with torch.no_grad():
        # Set to evaluation mode
        model.eval()
 
        # Validation loop
        for j, (inputs, labels) in enumerate(test_data_loader):
            inputs = inputs.to(device)
            labels = labels.to(device)
 
            # Forward pass - compute outputs on input data using the model
            outputs = model(inputs)
 
            # Compute loss
            loss = loss_criterion(outputs, labels)
 
            # Compute the total loss for the batch and add it to valid_loss
            test_loss += loss.item() * inputs.size(0)
 
            # Calculate validation accuracy
            ret, predictions = torch.max(outputs.data, 1)
            correct_counts = predictions.eq(labels.data.view_as(predictions))
 
            # Convert correct_counts to float and then compute the mean
            acc = torch.mean(correct_counts.type(torch.FloatTensor))
 
            # Compute total accuracy in the whole batch and add to valid_acc
            test_acc += acc.item() * inputs.size(0)

            # 输出测试中每一batch的损失和精确度
            print("Test Batch number: {:03d}, Test: Loss: {:.4f}, Accuracy: {:.4f}".format(j, loss.item(), acc.item()))
 
    # Find average test loss and test accuracy
    # 找到平均测试损失和精确度
    avg_test_loss = test_loss/test_data_size 
    avg_test_acc = test_acc/test_data_size
 
    print("Test accuracy : " + str(avg_test_acc))


# 对测试集进行推理，输入模型和需测试的图片源数据
def predict(model, test_image_name):
    '''
    Function to predict the class of a single test image
    Parameters
        :param model: Model to test
        :param test_image_name: Test image
    '''
    # 测试集
    transform = image_transforms['test']
 
    test_image = Image.open(test_image_name)
    plt.imshow(test_image)
    
    # 测试集图片tensor形式
    test_image_tensor = transform(test_image)
 
    if torch.cuda.is_available():
        test_image_tensor = test_image_tensor.view(1, 3, 224, 224).cuda()
    else:
        test_image_tensor = test_image_tensor.view(1, 3, 224, 224)
    
    with torch.no_grad():
        model.eval()
        # Model outputs log probabilities
        start = time.time()
        out = model(test_image_tensor)
        stop = time.time()
    print('cost time', stop-start)
    # torch.exp()作用是对输入的张量进行按元素指数运算。
    ps = torch.exp(out)
    # topk:取数组的前k个元素进行排序。
    # 通常该函数返回2个值，第一个值为排序的数组，第二个值为该数组中获取到的元素在原数组中的位置标号。
    topk, topclass = ps.topk(3, dim=1)

    # 输出推理预测的结果
    for i in range(3):
        print("Predcition", i+1, ":", idx_to_class[topclass.cpu().numpy()[0][i]], ", Score: ", topk.cpu().numpy()[0][i])
        
# 加载最优模型
model = torch.load( dataset+'_model_'+str(best_epoch)+'.pt')

# 预测测试集上的图片分类的效果
predict(model, r'test\backpack\sketch_013_000002.jpg\sketch_013_000002.jpg')

# 计算模型下测试集上损失函数的精确度/预测分数
computeTestSetAccuracy(model, loss_func)
```