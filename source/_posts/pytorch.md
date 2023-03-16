---
title: pytorch
date: 2023-03-07 16:33:10
tags: deep learning
katex : true
---

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
`__init__` : 定義網路 每層結構(layer)
`forward` : 資料如何在網路中傳遞
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

### Optimizing(最佳化) Model 參數
訓練模型需要:
- Loss Function
- Optimizer

```python
loss_fn = nn.CrossEntropyLoss()
optimizer = torch.optim.SGD(model.paramet)
```
