---
title: pytorch (1)
date: 2023-03-07 16:33:10
tags: deep learning
katex : true
---
## Quick to start
### Pytorch vs Tensorflow
- Pytorch + Caffine2 : 
  1. FaceBook開發
  2. 一邊推一邊算
  3. 靈活，方便調適，主要學術界
- Tensorflow2.0+Karis : 
  1. Google開發
  2. 先寫公式，在代數字
  3. 開發時間早，生態好，主要工業界


### 創建 Models
繼承 nn.Module
`def __init__(self,)` : 定義網路 每層結構(layer)
`def forward(self)` : 資料如何在網路中傳遞
`super()` = `super(className,self)`，找到MOR裡，claaaName後，最先有`__init__`
ex:
```python
class MyNetWork(nn.Module):
  super().__init__()
  self.model = nn.Sequential(
    models...
  )
  def forward(self, ...):
```
查看
```
model = MyNetWork()
print(model)
```

### Optimizing(最佳化) Model Parameters
訓練模型需要:
- Loss Function
- Optimizer

```python
loss_fn = nn.CrossEntropyLoss()
optimizer = torch.optim.SGD(model.paramet)
```

在單個訓練循環中，模型對訓練數據集（分批輸入）進行預測，並反向傳播預測誤差以調整模型的參數。

```py
def train(dataloader, model, loss_fn, optimizer):
  size = len(dataloader.dataset)
  model.train()
  for batch, (X, y) in enumerate(dataloader):
    X, y = X.to(device), y.to(device)

    # Compute prediction error
    pred = model(X)
    loss = loss_fn(pred, y)

    # Backpropagation
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    if batch % 100 == 0:
        loss, current = loss.item(), (batch + 1) * len(X)
        print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")
```

訓練可在cpu或gpu
```py
device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"using {device}")
```
we can put instance to cpu or gpu
```
model = className().to(device)
```




對比模型在test dataset以確保學習
```py
def test(dataloader, model, loss_fn):
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    model.eval()
    test_loss, correct = 0, 0
    with torch.no_grad():
        for X, y in dataloader:
            X, y = X.to(device), y.to(device)
            pred = model(X)
            test_loss += loss_fn(pred, y).item()
            correct += (pred.argmax(1) == y).type(torch.float).sum().item()
    test_loss /= num_batches
    correct /= size
    print(f"Test Error: \n Accuracy: {(100*correct):>0.1f}%, Avg loss: {test_loss:>8f} \n")
```

每個epoch 模型 
我們會打印模型accuracy和loss
```py
epochs = 5
for t in range(epochs):
    print(f"Epoch {t+1}\n-------------------------------")
    train(train_dataloader, model, loss_fn, optimizer)
    test(test_dataloader, model, loss_fn)
print("Done!")
```

保存模型
```py
torch.save(model.state_dict(), "model.pth")
print("Saved PyTorch Model State to model.pth")
```

加載模型
```py
model = NeuralNetwork()
model.load_state_dict(torch.load("model.pth"))
```

