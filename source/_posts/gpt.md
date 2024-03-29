---
title: GPT
tags: NLP
katex: true
abbrlink: 5c298aee
date: 2023-03-08 08:43:28
description: 本文將初步介紹GPT
cover: https://i.pinimg.com/564x/5d/b8/79/5db879e14829d1559e00f70638098c15.jpg
categories:
- NLP
---

## NLP 兩大模型

- Bert : 基於上下文預測 (完型填空)
- Gpt : 基於上文預測 (字回歸模型)
難度 : Gpt > Bert

### Gpt-1

transformer decoder
論文公式
$L_{1}(u) = \sum_{i}^{}logP(u_{i}|u_{i-k},...u_{i-1};\theta )$

將模型接全連接層分類進行什麼動作，再進行微調

### Gpt-2

不接全連接層不微調，採 zero-shot
透過擴充上文暗示，讓模型知道做什麼
模型輸入 = 自行輸入 + 擴充暗示
ex: 

1. 二分類 - 高興 vs 不高興
我今天晚上吃大餐，通過這句話我是高興還不高興(暗示)
2. 實體辨識(Ner)
我今天晚上吃鍋貼，上句話哪個是食物  

以上為訓練時所做

困境 : 實際說話，我們是不會加提示，因此預測結果並不會表現很好

### Gpt-3

一言以蔽之 文字接龍
得到生成每個字機率

![](gpt/gpt_how.png)

持續更新中...