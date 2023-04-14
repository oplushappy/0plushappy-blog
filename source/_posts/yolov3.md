---
title: YOLOv3
date: 2023-03-06 10:13:32
tags: YOLO
katex: true
---
### yolo演進
<!-- <img src="source\_posts\yolov3\evolve.png"> -->
<img src="..\yolov3\evolve.png" alt="演進" title="這是演進" >

### yolo v3 原理
**Backbone + Neck + Predict** 
<img src="..\yolov3\structure.png" alt="架構圖" title="這是架構" >

#### Backbone
Darknet 53
- all 捲積層
  <img src="../yolov3/Screen_Shot_2020-06-24_at_12.53.56_PM_QQoF5AO.png" width=300pt height=400pt title="darknet_53">

#### Neck
1. 產生19x19
2. 透過 [上採樣](#top) 19x19 然後 合併38x38 
3. 透過 上採樣 38x38 然後 合併76x76
   
<a id="top">上採樣</a>
濃縮特徵並提取出來

#### Prediction
n x n x 255 
255 = 3 x (4 + 1 + 80)
- 3: (3個[anchor](#anchor)) 
- 85: 
  - 4(x,y,w,h) ([grid cell](#grid) 中心點, 長寬)
  - 1 (confidence置信度)
  - 80 (80個類別 coco數據集)

三個類別:(以輸入608 x 608圖片為例)
1. 19 x 19 感受野76x76 : 預測大物體
2. 38 x 38 感受野38x38 : 預測中物體
3. 76 x 76 感受野19x19 : 預測小物體


#### anchor

持續更新中...
