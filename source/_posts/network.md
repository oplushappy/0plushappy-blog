---
title: network manage
date: 2023-03-06 13:42:31
tags:
---
以下是我上網管的課摘要
#### 網路
##### Network vs Internet
- Network: 有連接能力
- Internet: 所有有連接能力串在一起

#### Unit
- K(Kilo) : $10^3$ 
- M(Mega) : $10^6$
- G(Giga) : $10^9$

* Ki(Kibi) : $2^{10}$
* Mi(Kibi) : $2^{20}$ 
* Gi(Kibi) : $2^{30}$ 

EX: 
1. 1Mb/s = $10^6$ bits/s
2. 100MiB/s = $100*8*2^{20}/10^3 $Kb/s

#### 分層網路架構
五層
- Application
- Transfer
- Network
- Link
- Physical

#### Protocols
定義設備間如何溝通

#### Encapsulation
Data
segement + data
package + segement + data
frame + package + segement + data

#### Application
提供網路服務
- https
- smtp
- ssh
- dhcp
- ftp
#### Transport
確保資料傳輸正確 (偵錯、錯誤處理)
將上層或下層資料組合或切割
- TCP
- UDP
  
#### Port
分為2種
- 物理
- 邏輯 : TCP/IP協議中Port

#### Network
一點傳到另一點
- IP