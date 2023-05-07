---
title: Intern_3
tags: Intern
abbrlink: 4894284d
date: 2023-05-05 12:46:59
description: 本文將分享網管工作經驗
cover: https://i.pinimg.com/474x/fc/f8/63/fcf863e1f6e39cdbd7ca0c586b5d1333.jpg
categories:
- Intern
---

<!-- - 指導人 : Dino -->
<!-- - 時間 : 5/4 -->

## case

### \# 220716 網絡過於卡頓甚至斷連

- 流程 :

  1. 宿網管理系統 查學號看房號
  2. 致電
  3. 致電詢問改用CCU_DormNet_5G的使用情況
  4. 使用者反映有時會偵測不到CCU_DormNet_5G
  5. 在OSTicket備註 請後續網管追蹤

## LAB

### 使用者無法使用網路

流程 :

1. 先向使用者詢問學號、何時開始無法使用網路
  -> 使用者表示今天開始無法使用
2. 透過宿網管理系統 查學號檢查 :

   ```txt
   1. 是否申請宿網
   2. 繳費狀態
   3. 是否超流、中毒…等異常狀態
   4. MAC address有沒有綁
   5. 看Switch ip 和 port
   ```

3. putty登入

#### BPDU

4. `sh int b`

   ```txt
   看到Link ERR-DIS -> 懷疑 Link-flap 或 BPDU
   ```

5. `sh log | include 1/1/x`

   ```sh
   VLAN 239 BPDU-guard port 1/1/X detect (Received BPDU), putting into err-disable state
   -> 收到 BPDU，導致 port 被 disable 
   ```

   注意:
   可以使用以下檢查
   ```sh
   show errdisable summary
   show link-error disable
   ```

6. 致電使用者，詢問是否有開分享

    ```txt
   使用者表示有，但為初犯，所以口頭警告，告知如果再犯需要追繳並簽悔過書
   告訴使用者現在幫他恢復網路
    ```

7. 手動enable

    ```sh
    configure terminal
    interface ethernet 1/1/x
    enable
    ```
  
   sh int b 、 sh log | include 1/1/x

    ```sh
    Link down、log 並沒有顯示錯誤
    -> 解決BPDU 但 網卡並沒有UP，懷疑使用者那邊有問題
    ```


### 網卡停用 + IPV4未勾


8. 致電使用者 詢問使用者是否有網路
    ```txt
    使用者表示沒有
    ```

   開始詢問使用者網卡狀況

    ```txt
    1. 請使用者 window鍵 + R 輸入 cmd
    2. 輸入 getmac /v
    3. 使用者 反映 乙太網路的實體地址為 disconnect
      => 網卡停用
    ```

9. 協助使用者啟用網卡
    ```txt
    1. 請使用者 window鍵 + R 輸入 ncpa.cpl
    2. 請使用者在disconnect 的乙太網路左鍵點兩下或按右鍵選啟用
    3. 請使用者右鍵點內容，請使用者勾選IPV4
    ```

EX : 如果還是有問題才麻煩使用者帶設備來CBB檢查

---

## 學到東西 和 改善

1. 知道**網卡可手動停用** 或 **解除安裝** (網路介面沒東西)
2. 複習 ERR-DIS 相關指令 和 如何enable
  
    ```txt
   當初是用 errdisable recovery cause bpduguard
    ```
3. 在第一次詢問getmac /v 時，不知網卡可手動停用，而開始不斷比對`show running-config interface ethernet 1/1/x`

4. 用錯以為enable是要解除 violation 在重新restrict回去 
5. LAB時把 ACL也檢查

   ```txt
   其實應該先確認使用者網卡已連線，然後
    1. show ip dhcp snooping info 確認使用者ip取得狀況
    2. 無法取得ip、或取得ip仍然無法上網才去檢查 ACL
    ```

    <!-- 摘自[zoey(8)](https://discourse.dorm.ccu.edu.tw/t/topic/371?u=ken) -->

---

### 心得

感謝Dino指導，給我出一個可以複習很多觀念的LAB，並提示我很多次，讓我不要一直糾結port mac是否有綁錯，我得回去多加練習指令，和研究LAB。
