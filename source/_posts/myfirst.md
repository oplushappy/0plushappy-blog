---
title: Markdown語法
date: 2023-3-5 
updated: 2023-3-5
description: 語法快速入門
katex: true
---

最近開始寫文章  
這是markdown的一些語法

#### 標題
## 標題二
###### 標題六


#### 換段落  
空一行

#### 加粗
`**加粗**`
強調**加粗**  
詞前後兩顆星星(*)

#### 有序排序
1. 輸入數字點(1.)+空格
2. 按Tab 縮進有序排列
   1. 縮進效果
   2. :)

#### 無序排列
`- 你想排的東西`
- 用"-"號
- 用"*"號 

今天要做的事:
- [ ] 寫點程式
- [x] 橫槓+空格+左方括+空格+右方括

#### Latex
兩個dollar符號放在開頭和結尾，可以使用Latex
$$
\lim_{x \to 0}\frac{sin(x)}{x}=1
$$
文字中 $\lim_{x \to 0}\frac{sin(x)}{x}=1$ 空格加前後一個dollar符號

#### 表格
字和字加|這個符號
要有一排 |---|
`| --- | --- | --- |`
| A   | B   | C   |
| --- | --- | --- |
| 10  | 20  | 30  |


#### Code block
反引號三個 ```放在開頭和結尾
```cpp
cout<<"hello world"
```

#### 橫線
三個橫線 - ,前後空一行(兼容)
`---`

---

#### 鏈接
這是[鏈接](https://github.com/oplushappy, "我的github")
將字選取然後貼上鏈接
```
[超連結文本] (url "tip")
```
封裝:一次改很多
[hello][la]
[hello][la] [hello][la]


[la]:google.com "我是菇狗"

```Markdown
[超連結文本] [label]

[label]:url "tip"
```

請參考[標題](#標題)

```Markdown
請參考[標題](#標題)
```
#### IMG
![我是圖片](../../themes/butterfly/source/img/favicon.png "HEXO")

#### HIGHLIGHT
==我很重要==
