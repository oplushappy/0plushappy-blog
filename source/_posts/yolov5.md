---
title: YOLOv5
tags: YOLO
katex: true
abbrlink: c532002f
date: 2023-03-20 22:44:44
---
## 原理
yolo v5 分為 s, m, l, x 四種大小，對應預測目標由小到大，由快到慢
- 模型結構


## model 實現
code 實現分為4個部分
- 輸入
```py
class Model(nn.Module):
  def __init__(self, cfg='yolov5s.yaml', ch=3, nc=None, anchors=None):  # model, input channels, number of classes
```
- 加載傳入的配置文件
yaml 是一種描述設定的文字格式
```py
if isinstance(cfg, dict): 
    self.yaml = cfg 
else:  # is *.yaml
    import yaml  # for torch hub
    self.yaml_file = Path(cfg).name
    with open(cfg) as f:
        self.yaml = yaml.load(f, Loader=yaml.SafeLoader)  
```
- 利用配置文件，搭建網路的每一層
```py
ch = self.yaml['ch'] = self.yaml.get('ch', ch)  # input channels
if nc and nc != self.yaml['nc']:
  logger.info(f"Overriding model.yaml nc={self.yaml['nc']} with nc={nc}")
  self.yaml['nc'] = nc  # override yaml value
if anchors:
  logger.info(f'Overriding model.yaml anchors with anchors={anchors}')
  self.yaml['anchors'] = round(anchors)  # override yaml value
self.model, self.save = parse_model(deepcopy(self.yaml), ch=[ch])  # model, savelist
self.names = [str(i) for i in range(self.yaml['nc'])]  # default names
```

- 求網路的stride(步長)，和對anchor做專門處理
```py
m = self.model[-1]  # Detect()
if isinstance(m, Detect):
  s = 256  # 2x min stride
  m.stride = torch.tensor([s / x.shape[-2] for x in self.forward(torch.zeros(1, ch, s, s))])  # forward
  m.anchors /= m.stride.view(-1, 1, 1)
  check_anchor_order(m)
  self.stride = m.stride
  self._initialize_biases()  # only run once
  # print('Strides: %s' % m.stride.tolist())
```
- 對網路參數初始化，以及打印一些工作
```py
initialize_weights(self)
self.info()
logger.info('')
```

- parse_model 
持續更新...