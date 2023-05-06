---
title: Intern_2
tags: Intern
abbrlink: c6e57a8d
date: 2023-04-24 19:43:28
---

<!-- - 時間 : 2023/4/24
- 指導人 : GodenFP -->

## case

### \# 220713 無法使用有線網路

流程 :

1. 宿網管理系統 查學號看switch ip 和 port
2. putty 登入
3. sh int b  -> 看到 down
4. sh log | include 1/1/x -> 看到port security

    ``` txt
    Apr 21 15:47:59:W:Security: Port Security violation at interface  ethernet 1/1/x,  address 7cc2.c643.7a84, vlan 224
    ```

    >結合之前案件狀況，懷疑使用者使用新的網卡，綁定的卡號可能達到上限，因此需要檢查綁定的卡號
5. 查宿網管理系統，發現網卡已綁三張
6. 致電沒接
7. OSTicket 請後續網管追蹤

#### 協助綁定mac的方法

- 電話協助 :
  登入中正宿網
  -> 告知在網路卡號那一欄，刪除不常用裝置
  -> 再點擊自動檢測，以綁上新裝置

- 網管(我們)的協助
  1. 從網管系統綁 :
    網管系統查詢學號
    -> 在網卡卡號那一欄點擊新增
    -> 輸入卡號
  2. 從機器綁 :
    登入使用者使用的switch
    -> 輸入以下指令以新加網卡卡號
      ```sh
      confugure terminal
      interface ethernet [port]
      port security
      secure-mac-adress [Mac] (加 no 解綁)
      enable
      ```

- 補充 :
  - show port security mac eth [port]
    >看到綁定的網卡卡號

---

## 教學

### Winoc

- 帳密 : switch 一樣
- 主要看 WIFI log
- 流程 :
  - 輸入學號，勾選包含子組織
<!-- ![image|690x79](upload://oLeVwAzH6H6PVBNSQcI7bAJl4qS.png) -->

  - 看網卡、詳細log
- 查看 :
  - 網路存取節點名稱 : WIFI漫遊、訊號不好
  - 連線終止原因
  - 連線時間 : 如果非常短(例如1秒)，AP問題
  - 裝置類型
<!-- - 補充 :
[連上宿網Wi-Fi 後無法登入](https://osticket.dorm.ccu.edu.tw/scp/faq.php?id=5) -->

- WIFI 漫遊 :
  - 當使用者在一個 Wifi 範圍內移動時，自動切換到訊號更好的 AP（Access Point）的過程。這樣使用者就可以在無線網路範圍內自由移動，而不需要手動重新連接到不同的AP，從而確保連接的穩定性和連線的連續性。
  - 可能出現的問題 :
    - 使用者頻繁在不同的WIFI AP間切換，導致無法使用網路 → 可請使用者連不同頻段的wifi(ex : 2.4GHz改連5GHz) → 若依舊無法解決需通知BOSS處理

### VSZ

- 帳密 : switch 一樣
- 主要看 WIFI AP
- 流程 :
  - 搜尋輸入 : MAC (連接 WIFI)  
    ex: `40:EC:99:CD:4b:F0`
  - Troubleshooting : 時間軸+問題發生點
  - Events : log
  - Alarms : 錯誤訊息

### Graylog

- 帳密 : switch一樣
- 主要看 log (停電導致log被刷掉)
- 流程 :
  - 先選搜尋時間
  - 再輸IP
  - 可以查看switch 的所有 log
  - ex : `[switch ip] AND "[port]"`

### LibreNMS

- 帳密 : switch一樣
- 機房檢測流程 :
  1. 先選哪一棟
  2. 時間間隔1個月或2個月
  3. Temprature 有沒有超過90度，波型正不正常
<!-- ![image|690x340, 50%](upload://eail92Gh7c6qekYLMkJF4qw6Wck.jpeg) -->

  4. Memory 有無超過90% (Router)
  5. CPU 有沒有Peak
  6. Port Error 看自己負責的機器是某出現在上面，如果有進機器看是否真的有錯誤 (Description)
<!-- ![image|690x291, 75%](upload://a3bxIwzjI6Q4vUo7Yo7qSg24okE.png) -->


- 補充:
  可以特別查看個別機器
  例如:
  - 機器溫度
  - Recent Events顯示機器近期活動
    >2023-04-24 21:47:11	GigabitEthernet1/1/7	ifOperStatus: down -> up

## 機房

1. 有穩壓器AVR 或 不斷電系統UPS (通常UPS故障會替換成AVR)
<!-- ![image|432x499, 50%](upload://2N8uK078usAh1hRl1BLo0NchFmR.jpeg) -->

  **注意** : 不可直接放地上，需放箱子或椅子上

<!-- - 補充 :
![image|505x259, 75%](upload://xLlL5GKmM0VVFuPsgefbFhWX3ld.png)
節錄之前[回覆](https://discourse.dorm.ccu.edu.tw/t/topic/272/9?u=ken) -->
2. 溫度、濕度
<!-- ![image|339x358, 50%](upload://vF6z4y9fQaQIWvlyXRBeyMY51HK.png) -->


3. 冷氣定時器(藍色外圈為冷氣開啟時間，內圈為關閉時間，灰色時間是1~24)
<!-- ![image|471x500, 50%](upload://lSAE6vXG8kwwUoSFWxVuRz9cwrQ.jpeg) -->

  **注意** : 如果代表現在時間的鐘(白色圓形)誤差10分鐘要調
4. Switch 
數字代表Switch IP(末兩碼)
<!-- ![image|683x377, 50%](upload://5XYIPaWnbfkrcLRfwhbjioz9mN9.jpeg) -->
WIFI AP
<!-- ![image|676x106, 50%](upload://gO8NQTJKx1bWt8pNbhZ8DFCFEtV.png) -->
(光纖介面 : (RJ45 接 網路線) 或 (光纖線) )
<!-- ![image|518x429, 50%](upload://cS0nvrcSR94LoUCYAxs0UW9AaHQ.jpeg) -->
RJ45 接 網路線
5. Patch Panel (理線用)
6. Router(每棟3樓)

---

## 心得

感謝GodenFP教學，今天學習4個系統、去機房看了一下、還有複習查看Switch設定的流程，又是充實一天。
