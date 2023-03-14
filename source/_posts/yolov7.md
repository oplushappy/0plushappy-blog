---
title: yolov7
date: 2023-03-10 21:52:02
tags: yolo
katex: true
---
### Vgg
2014 曇花一現 : 捲積可以用多層3 x 3
隔年被殘差(residuals): 分支打敗

RepVgg檢討:
1. 訓練 可以不等於 預測模型
2. Conv + Bn 融合
3. residual 不同分支融合

### Bn
$$
x_{i} = \gamma \frac{x_{i} - \mu }{\sqrt{\sigma ^{2}+\varepsilon }}+\beta 
$$
不適單純歸一化，$\gamma$ $\beta$ 可學習

$W =W_{conv}W_{Bn}$

### Resdiual
1. 1x1
  將 1x1 外圍多填充一圈0 $\rightarrow$ 3x3
  將 input 外圍多填充一圈0

2. 直接相加
  生成 3x3 中心為1或0 其餘皆為0

### YOLO V7
1. 中心點偏移
2. 採三個正樣本   