---
title: yolov7
date: 2023-03-10 21:52:02
tags: yolo
katex: true
---
參考網站:
1. [WongKinYiu/yolov7](https://github.com/WongKinYiu/yolov7)
2. [YOLOv7论文，网络结构，官方源码，超详细解析](https://www.bilibili.com/video/BV1Ld4y1W7VP/?p=2&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=eedb40643793ea7852c5f0638ff5932c)


# YOLO V7   

## 結構
### ELAN
<center>
<img src="..\yolov7\LpfaiRz.png" height=300>
</center>

實際實現

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

## Trainable bag-of-freebies
### 結構重參數化
構造一系列結構(用於訓練)，將其參數等架轉換為另一組參數(用於推理)，從而將一系列結構轉成另一系列
在vgg取得不錯結果

但直接在殘差模塊上套用，精度降低
本來就有橫等連接(identity connection)，會和RepConv起衝突
- 因此橫等連接不要用RepConv或必須使用RepConvN
<img src="../yolov7/repcov.png">

- ELAN中卷積直接傳給拼接層也不能直接用RepConv，得用RepConv

- 0.1 版 只有最後使用，且不是在拼接或殘差上使用，使用(b)
```python
[75, 1, RepConv, [256, 3, 1]],
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