---
title: Network Introduction (1)
tags: Network
katex: true
abbrlink: a90bfb7f
date: 2023-03-06 20:32:49
cover: https://i.pinimg.com/564x/0d/a8/0c/0da80c00d396748e93fdece8fcadaf74.jpg
description: 本文將總結網路相關基礎知識
swiper_index: 75
categories:
- Network
---

#### 設備
core router -> router -> switch -> patch panel -> key stone 

#### Router
Ruckus ICX-7250 , ICX-7750
乙太網路 : 平接或下接
SFP+(光纖) : 上接
- WiFi : 由switch 接出來，因此光纖有可能上接switch、WiFi AP(類似天線，將網路變成電波船撥出去)
#### Switch
Ruckus ICX-7250
乙太網路 : 平接switch 或 下接key stone
SFP+(光纖) : 上接 router

#### Err-Disable
port發生錯誤，需手動enable
1. Link-flap : 在10秒內跳8下
2. BPDU guard : 開分享器

#### DHCP Snooping
設備 透過 switch 向 DHCP Server要 IP 
DHCP Spoofing : 某Server給予違法IP 

DHCP Snooping
- port 分為 trust untrusted
- 僅允許trusted port的DHCP封包
- 建立 IP - MAC - Port

#### IP source Guard
限制只能用某DHCP 配發的 IP
1. 防止 冒用IP 
2. 防止手動更改 繞過網路流量、中毒 

- 透過比對DHCP Snooping Database

#### Dynamic ARP Inspection
ARP : IP - Mac
ARP Spoofing
- 中間人 : 將自己Mac回復
- 阻斷式 : 將被攻擊者的Mac回覆給所有人

查看arp
```
show arp
show arp [ip || MAC]
```
設定
```
configure terminal
arp ip MAC ethernet 1/1/x
```
#### Port-Security
port - MAC
查看switch 指令
```
show port security statistics unit 1
show port security ethernet 1/1/x
show running-config interface ethernet 1/1/x
```
進入設定
```
configure terminal
interface ethrenet 1/1/x
port security (以下前面加 no 就可解除)
```
更改
```
maximum 數量
violation 處理發式
sec-mac-address MAC號
enable
end (退出)
```

#### Acces Control List (ACL)
port - IP
設定ACL
```
configure terminal
ip access-list extended <ACL name>
設定規則
permit ip host xxx.xxx.xxx.xxx any
permit udp any any eq bootps
deny ip any
```
進入設定套用
```
configure terminal 
interface ethernet 1/1/x
ip access-group <ACL name> in
end
```

