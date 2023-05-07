---
title: RepVGG
tags: CV
katex: true
abbrlink: f5d80f0d
date: 2023-03-16 19:52:15
cover: https://i.pinimg.com/564x/31/b9/79/31b9794a0bfd77c3f4e1718901bb3fb1.jpg
description: 本文將總結RepVgg基礎知識
categories:
- CV
---
## RepVgg
### Vgg
2014 曇花一現 : 捲積可以用多層3 x 3
隔年被殘差(residuals): 分支打敗

RepVgg 改進:
1. 訓練 可以不等於 預測模型
2. Conv + Bn 融合
3. residual 不同分支融合
   
只使用一個主支，分支都融合到主支
詳細作法

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