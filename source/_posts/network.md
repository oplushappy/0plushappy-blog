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

#### Physical
電線、光纖

---

#### Vlan
- PVID : 可以從哪個 Vlan 出去
- Tagged port: 出去時帶有vlan tag
- Untagged port: 出去時不帶有vlan tag

#### DHCP
由於要節省IP
我們設備並沒有IP只有Mac，因此當我們用時需請求IP
1. 探索: 透過Broadcast 發送 UDP 請求到 DHCP Server
2. 提供: DHCP 發送IP到我們設備
3. 回覆: 回傳我們接受採用
4. 確認: DHCP回傳訊息告知以保留

#### NAT
1. 公司或學校擁有IP有限，因此我們對外統一由一個IP來通訊
2. 透過 DHCP 得到的IP可能是subnet ，無法直接對外溝通

#### NAT + DHCP
WIFI盒 透過DHCP 動態分配 IP給電腦、手機
對外統一由一個IP來通訊

#### ARP
IP ---- MAC



