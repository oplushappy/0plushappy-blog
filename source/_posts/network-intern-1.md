---
title: Intern_1
abbrlink: 2c7afbf5
date: 2023-04-14 19:11:04
description: 本文將分享網管工作經驗
cover: https://i.pinimg.com/564x/91/7f/15/917f15f940a67c5e8ded81a0836860d7.jpg
swiper_index: 70
tags: Intern
categories:
- Intern
---

- 時間 : 2023/4/14
- 指導人 : Firecodev

## Case

思考之前處理過case，指導員給予教學和建議

### \# 220715 連不上網

流程

1. 透過宿網管理系統處理
2. 查詢學生學號，在個人頁面會顯示斷線紀錄 (紀錄使用多少流量)，發現超流
3. 回復隔天凌晨就會恢復，但建議15分鐘後再去試

### \# 220712 都過隔天了，宿網還是斷線不能用

流程

1. 用宿網管理系統查看，在個人頁面確實超流當天沒有紀錄斷線，隔天凌晨才顯示斷線，造成斷線隔天依然不能使用網路
2. 可能系統Bug

### \# 220714 紅米Note 7無法連CCU_DormNet

心得

1. 這個case我沒有什麼頭緒，可能會請同學到辦公室處理
2. 指導員提示我手機之類問題，可以將問題丟到GOOGLE查詢或詢問別人

### \# 220713 無法使用有線網路

之前網管測試發現可能是網路線的問題，有請後續網管透過電話確認情況

流程

1. 打電話確認，使用者換線後仍無法使用有線網路，他表示顯示非私人連接
2. 透過宿網管理系統，到個人頁面點擊流量管理，查看使用者的有線網路和WIFI當日使用狀況，發現最近幾周有線網路流量確實皆為0
3. 透過PuTTY連線，查看使用者有線網路的switch (IP顯示在個人頁面)
4. show interface breif eth 1/1/X 是UP
5. show interface eth 1/1/X 發現不斷上下線， 
6. input error 和 CRC 皆為 0，應該不是線的問題(換過了，而且應該也不會造成顯示非私人連線)
  注意 : crc、input error有問題，要先清掉 crc、input error，一段時間再看
7. ... 時間不夠

#### crc、input error

input error : 通常指的是接收到的資料包中出現錯誤，這可能是由於噪音、干擾、電氣問題等原因導致的。
CRC : 則是一種資料包錯誤檢查機制，用於檢測在資料傳輸過程中可能出現的位元錯誤。

可能存在以下問題：

物理層問題：例如網路電纜品質不佳、連接不良、電磁干擾等。=>檢查介面的物理連接，確保連接穩固。

網路設備問題：網路設備（如交換機）本身的問題引起的，例如硬體故障、軟體設定不當等。
=>檢查相關網路設備的設定，確保其正確配置且運作正常。

source : ChatGPT

---

## 教學

### 上下班流程

1. 開門 → 翻牌 → 開冷氣
2. 簽到
3. OSTicket查看case
4. 查看電子繳費 (宿網系統 → 繳費 → 電子繳費)
5. 翻牌 → 關冷氣 → 關燈 → 鎖門

### OSTicket

使用者問題顯示在最上面標題
詳細顯示在明細的第1個對話，通常寫下使用者說出來狀況

藍色:為正式對話(使用者)

橘色:為正式對話(網管回覆)
  1. 有禮貌
  2. 狀況重述
  3. 講述解法
  4. 選擇罐頭訊息

淺黃色為備註，網管才看得到

### 電子繳費

- 透過宿網管理系統，點擊繳費電子繳費

有時因為入帳時間比較慢，電子繳費還沒顯示，可以請同學先上傳繳費證明

### 環境

1. 放書櫃子
   1. 借書記得寫借單簿
   2. 印章蓋換寢單
  
2. 書櫃旁櫃子
   1. 工作證 (可以刷全部的宿舍)
   2. 學士班機房鑰匙
   3. 簽到單

3. 出勤櫃子(裝線箱子旁)
   1. 外勤包
   2. 測線器
   3. 外勤筆電 x 2 (vpn 可連宿網 看宿網管理系統)

4. 地上
   1. 好的網路線 x 1箱
   2. 壞的網路線 x 1箱

## 機房

大學部 : 3樓(有Router)、7樓(沒Router)
電梯出來往後走，走到底
鑰匙 : 貼新機房標籤

碩博班 : 302 (E棟 : 3樓走廊中間)
鑰匙 : 到碩博宿管(B棟)借

之前日誌的補充
使用者電腦到機房順序 :
PC → Cable → Keystone → Cable → Patch Panel → Cable → Switch → Router

## 外勤包 (在一堆網路線旁櫃子)

1. usb 轉 rs232(灰色)
2. console線 x 2 (新版(天藍色)+舊版(黑色線)) (和1是一組)
3. 網路線
4. 外接網卡 (network(usb) adataptor)
5. 剝線器
6. 筆
7. sfp轉rj45
8. 螺絲
9. 兩把剪刀 + 十字起子 、 一字起子
10. keystone 、 殼子 (用完外勤櫃子中箱子補充)

## 刷機用到線(不小心把Switch設定改掉)

1. usb 轉 rs232

2. console線
   1. 黑色:
    舊switch (ICX-7250)
    正面左上角
   2. 天藍色:
  新switch (ICX-7150)
  認1010 (大台在插電源那側(背面)，小台在正面左上角)

## 測線器 (fluke)

開機 按下半部有一個開機紐

- Cable
  線兩端接機器上，測有沒有錯
  
  - 如果要測很長的線，例如從同學房間到 Patch panel
    loopback : 插在一端
    另一端線插在側線器

  - 會顯示多長距離的線都是正常(距離不一定準確)

- Switch
  看有沒有跟Switch正常接通

### 電話確認

電話 : 到宿網管理使用者個人頁面，查看寢號，
學士班u轉7，其他照上面顯示打，英文代表床號不用管
碩博班g轉8，英文A=1,B=2...，最後+50

說辭:

1. 先說這裡是網管辦公室，找某某同學 (if不在說之後還會打給他)
2. 之前反映某事
3. 解決問題後(如換線後)，使用狀況如何
4. if還有問題，先詢問是否能先使用另一個連線方式使用，確保使用者還有網路

工作相關電話:

1. cb1 : 73199
2. 學士班宿管 : 73399
3. 219 : 14151
4. 碩博宿管 : 82121

## KeyStone

流程 :

1. 剝線(不要太長)
2. 看是用A還B，上面會顯示圖案
  注意 : 實際現場要記得看之前Keystone連接方式
3. 將線橫躺先卡到每個正確位置
4. 用剝線器的頭(有小凹口)出力將線卡進去，有卡的感覺就對了
5. 將多餘線減掉

---

## 學到東西

1. 了解基本設備(線、測線器...)
2. 熟悉使用宿網管理系統、OSTicket
3. 複習遠端查看switch相關設定的指令
4. 判斷爛線、頻繁上下線問題(input errors、CRC、(state up 、 state down))
5. 實作Keystone

## 心得

我在解case的過程中學到了很多關於宿網管理系統和判斷問題知識，並且複習了相關指令。此外，Firecodev學長還與我分享了他解case的想法，讓我對解題的方法和思路有了更深刻的理解。

非常感謝Firecodev學長在我的第一天實習給予了很多指導和教學，以致於到了11點多才結束，我對今天的收穫感到非常滿意，謝謝。
