---
title: Transformer
tags: NLP
katex: true
abbrlink: e0d2147a
date: 2023-03-09 17:09:22
---
### 簡單來說
輸入 -> Encoding -> Decoding -> 輸出

### 原理
#### 總覽
![this is jpg](transformer/model.jpg)

#### Self-Attention

![this is jpg](transformer/20150622OMcuB9Q8Zl.jpg)


Q : 原物
K : 相似參數
Q dot K : 相似度(attan)

#### Add & Norm

![](transformer/20129616BHmGH4ukte.png)

1. Residual
2. LayerNorm

#### 補充

- Embedding
  例子 : 描述哆啦A夢
  原本 : 藍色的、白色的、長得像狸貓、手和腳是圓形
  Embedding : [123,234] 這代表上面所有形容詞
