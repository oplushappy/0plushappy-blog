---
title: CNN
date: 2023-03-07 16:17:53
tags: Deep learning
katex: true
---
- 捲積 : 濃縮特徵
- 最大池化 : 提取特徵
- 全連接層 : 分類

#### Convulation 捲積
```python
nn.Conv2d(in_channels, out_channels, kernel_size, stride, padding)
```
- in_channel : 一般圖像為 3 layer 
- out_channel : 
- kernel_size : 生成一個 n x n 大小的滑動窗口 與 
#### Max pool 最大池化
```python
nn.MaxPool2d(kernel_size, stride, padding)
```

#### Fulling 全連接