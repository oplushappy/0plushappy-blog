---
title: CNN 卷積神蹟網路
date: 2023-03-07 16:17:53
tags: CNN
katex: true
---
參考網站:
[卷积的in_channel与out_channel](https://blog.csdn.net/qq_51533157/article/details/122789563)
[Day 15. 深度學習模型 - CNN（一）](https://ithelp.ithome.com.tw/articles/10301321?sc=iThelpR)

- 卷積層 : 濃縮特徵
- 池化層 : 提取特徵
- 反向傳播 : 學習(調整參數)
- 全連接層 : 分類

以下以 Pytorch 舉例
#### Convulation layer 卷積層
藉由滑動窗口(kernel)提取特徵，產生特徵地圖(Feature Map)
<img src="..\cnn\1_D6iRfzDkz-sEzyjYoVZ73w.gif" width = "3000" >
<!-- <center></center> -->
```python
nn.Conv2d(in_channels, out_channels, kernel_size, stride, padding)
```
1. in_channel : 一般圖像為RGB, 因此有三層 
2. out_channel : Feature Map 大小
$$
\frac{inchannel + padding * 2 - kernel}{stride} + 1
$$
3. kernel_size : 生成一個 n x n 大小的滑動窗口 ， 深度和in_chanel一樣 
4. stride : 滑動窗口完成一次，向右移動n個單位
5. padding  : 向外填充n圈0

結論: 不管使用深度為多少圖片，經過kernel都將產生深度為1的Feature Map ， 有幾個kernel就有幾個Feature Map

#### Pooling layer 池化層
提取主要特徵，選擇最具代表特徵降低參數量，和避免過擬合

1. 最大池化 (Max Pooling) : 挑局部區域最大數
<img src="..\cnn\20150622c0sl0K9RkS.jpg">
```python
nn.MaxPool2d(kernel_size, stride, padding, dilation=1, return_indices=False, ceil_mode=False)
```
2. 平均池化 (Average Pooling) : 局部區域平均為代表
  <img src="../cnn/20150622kHFPeSwp6x.jpg">
```python
nn.AvgPool2d(kernel_size, stride, padding, ceil_mode=False, count_include_pad=True, divisor_override=None)
```
#### Fully-connected Layer 全連接層
1. 先將輸出拉平
```python
torch.flatten(input, start_dim=0, end_dim=- 1)
```
2. 將所有局部特徵(全連接)，經過權重組成新的圖

