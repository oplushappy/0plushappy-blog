---
title: yolov7
date: 2023-03-10 21:52:02
tags: yolo
katex: true
---
參考網站:

1. [WongKinYiu/yolov7](https://github.com/WongKinYiu/yolov7)
2. [YOLOv7论文，网络结构，官方源码，超详细解析](https://www.bilibili.com/video/BV1Ld4y1W7VP/?p=2&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=eedb40643793ea7852c5f0638ff5932c)

<!-- # YOLO V7    -->

## 創新點

### ELAN

<center>
<img src="..\yolov7\LpfaiRz.png" height=300>
</center>

實際實現配置

```python
# [from, number, module, args]
[-1, 1, Conv, [128, 3, 2]], #160x160x128 
   [-1, 1, Conv, [64, 1, 1]], #4
   [-2, 1, Conv, [64, 1, 1]], #5
   [-1, 1, Conv, [64, 3, 1]], #6
   [-1, 1, Conv, [64, 3, 1]], #7
   [-1, 1, Conv, [64, 3, 1]], #8
   [-1, 1, Conv, [64, 3, 1]], #9
   [[-1, -3, -5, -6], 1, Concat, [1]], #10
   [-1, 1, Conv, [256, 1, 1]],  # 11
```
<img src="..\yolov7/Snipaste_2023-03-16_15-48-01.png">

### E-ELAN

<img src="../yolov7/Snipaste_2023-03-16_20-14-56.png" WIDTH=2000>
等價於並行兩個ELAN結構
實現
```PYTHON
   [-1, 1, DownC, [160]],  # 2-P2/4  
   [-1, 1, Conv, [64, 1, 1]],
   [-2, 1, Conv, [64, 1, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [[-1, -3, -5, -7, -8], 1, Concat, [1]],
   [-1, 1, Conv, [160, 1, 1]],  # 12
   [-11, 1, Conv, [64, 1, 1]],
   [-12, 1, Conv, [64, 1, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [-1, 1, Conv, [64, 3, 1]],
   [[-1, -3, -5, -7, -8], 1, Concat, [1]],
   [-1, 1, Conv, [160, 1, 1]],  # 22
   [[-1, -11], 1, Shortcut, [1]],  # 23
```
<img src="../yolov7/Snipaste_2023-03-16_20-20-49.png" width=3000>

### 模型縮放

因為ELAN涉及拼接，因此縮放需特別設計
對應關係 : 

1. yolov7 $\rightarrow$ yolov7x
   1. 額外多一條由兩個卷積構成支路 : 擴大模型深度
   2. 特徵輸入的數量，concat輸出的數量，過渡的卷積通道數都變成 1.25倍 : 擴大模型寬度
2. yolov7-w6 $\rightarrow$ yolov7-e6 $\rightarrow$ yolov7-e6e $\rightarrow$ yolov7-d6

### Trainable bag-of-freebies

### 結構重參數化

構造一系列結構(用於訓練)，將其參數等架轉換為另一組參數(用於推理)，從而將一系列結構轉成另一系列
在vgg取得不錯結果

但直接在殘差模塊上套用，精度降低
本來就有橫等連接(identity connection)，會和RepConv起衝突

- 因此橫等連接不要用RepConv或必須使用RepConvN
<img src="../yolov7/repcov.png">

- ELAN中卷積直接傳給拼接層也不能直接用RepConv，得用RepConv

- 0.1 版 只有最後使用，且不是拼接或殘差時使用

```python
[75, 1, RepConv, [256, 3, 1]]  #使用(b)
[88, 1, RepConv, [512, 3, 1]],
[101, 1, RepConv, [1024, 3, 1]],
```

### 新的標籤分類方法

Deep supervision : 

- 淺層額外加輔助頭幫忙計算loss，也會反向傳播更新參數
<img src="../yolov7/ds.png">

label assignmen:
<img src="../yolov7/dp2.png" width=600>

- hard label: 真實值(ground truth) 和 檢測值 做LOSS，再來給每個格子標籤

- soft label: ground truth 和 檢測值 先透過分配器，再跟 ground truth做 Loss來分配

分配器: YOLOX也有用到

```PY
compute_loss_ota = ComputeLossOTA(model)  # init loss class
compute_loss = ComputeLoss(model)  # init loss class
```

### 有用到訓練方法

1. BN層
2. Implicit knowledge
3. EMA

## 網路結構

分為三類

1. YOLOv7-tiny, YOLOv7-tiny-silu : 邊緣GPU
2. YOLOv7, YOLOv7x : 常規GPU
3. YOLOv7-W6, YOLOv7-d6, YOLOv7-e6, YOLOv7-e6e : 雲端GPU

YOLOv7
<img src="../yolov7/yolov7.png">

### BackBone
#### Conv

conv2d + bn + silu

```py
class Conv(nn.Module):
    # Standard convolution
    def __init__(self, c1, c2, k=1, s=1, p=None, g=1, act=True):  # ch_in, ch_out, kernel, stride, padding, groups
        super(Conv, self).__init__()
        self.conv = nn.Conv2d(c1, c2, k, s, autopad(k, p), groups=g, bias=False)
        self.bn = nn.BatchNorm2d(c2)
        self.act = nn.SiLU() if act is True else (act if isinstance(act, nn.Module) else nn.Identity())

    def forward(self, x):
        return self.act(self.bn(self.conv(x)))

    def fuseforward(self, x):
        return self.act(self.conv(x))
```

#### ELAN 

右邊卷積 構成2條支路
<img src="../yolov7/elan_4_11.png">

#### MP1

由於輸出是輸入1/4 , 類似MaxPool

<img src="../yolov7/mp1_12_16.png">

### Head

#### SPPCSPC

<img src="../yolov7/sppcspc_51.png">

```PY
class SPPCSPC(nn.Module):
    # CSP https://github.com/WongKinYiu/CrossStagePartialNetworks
    def __init__(self, c1, c2, n=1, shortcut=False, g=1, e=0.5, k=(5, 9, 13)):
        super(SPPCSPC, self).__init__()
        c_ = int(2 * c2 * e)  # hidden channels
        self.cv1 = Conv(c1, c_, 1, 1)
        self.cv2 = Conv(c1, c_, 1, 1)
        self.cv3 = Conv(c_, c_, 3, 1)
        self.cv4 = Conv(c_, c_, 1, 1)
        self.m = nn.ModuleList([nn.MaxPool2d(kernel_size=x, stride=1, padding=x // 2) for x in k])
        self.cv5 = Conv(4 * c_, c_, 1, 1)
        self.cv6 = Conv(c_, c_, 3, 1)
        self.cv7 = Conv(2 * c_, c2, 1, 1)

    def forward(self, x):
        x1 = self.cv4(self.cv3(self.cv1(x)))
        y1 = self.cv6(self.cv5(torch.cat([x1] + [m(x1) for m in self.m], 1)))
        y2 = self.cv2(x)
        return self.cv7(torch.cat((y1, y2), dim=1))
```

#### ELAN'

右邊卷積 構成4條支路
<img src="../yolov7/elan_pro.png">

#### MP2

和MP1幾乎一樣，只是多了左側會傳東西

<img src="../yolov7/mp2.png">

#### RepConv

```python
[75, 1, RepConv, [256, 3, 1]]  
[88, 1, RepConv, [512, 3, 1]],
[101, 1, RepConv, [1024, 3, 1]],
```

部署時 : 只用一個二維卷積
訓練時 : 三條支路(3x3,1x1,橫等連接)

```py
if deploy:
   self.rbr_reparam = nn.Conv2d(c1, c2, k, s, autopad(k, p), groups=g, bias=True)

else:
   self.rbr_identity = (nn.BatchNorm2d(num_features=c1) if c2 == c1 and s == 1 else None)

   self.rbr_dense = nn.Sequential(
         nn.Conv2d(c1, c2, k, s, autopad(k, p), groups=g, bias=False),
         nn.BatchNorm2d(num_features=c2),
   )

   self.rbr_1x1 = nn.Sequential(
         nn.Conv2d( c1, c2, 1, s, padding_11, groups=g, bias=False),
         nn.BatchNorm2d(num_features=c2),
   )
```

#### Detect

<img src="../yolov7/detect.png">

```py
self.m = nn.ModuleList(nn.Conv2d(x, self.no * self.na, 1) for x in ch)  # output conv
```