---
title: How to use NodeJs with Typescript and ESB module
date: 2020-11-13T17:11:57+08:00
categories: 
- Node.js
tags:
- aaa
- bbb
top_img:
---
## 各元件介紹
先介紹各個元件是什麼
1. vscode(Visual Studio Code)：多功能的IDE
2. npm：套件管理工具？(目前認知是這樣，後續補充)
3. nodemon：監視原始碼是否有變更，有變更則依照腳本自動重新啟動
4. node.js；採用V8引擎讓Javascript成為後端語言的強大開發工具
5. typescript：由Microsoft主導，將Javascript加入強型別特性的新語言，一般需要將.ts檔案藉由typescript編譯為.js，讓瀏覽器或Node.js能夠辨識
6. ts-node：讓typescript檔案無須編譯為.js檔案，即可直接使用.ts檔

Node.js原生是沒有支援Typescript的，並且在14版才正式以ESM取代CSM，因此網路上到處都是CSM的教學，是Typescript且是ESM的文章稀少，初學Node.js的我為此燒透了腦

VSCode提供一個方便的除錯器讓開發者除錯，但要怎麼把這6個東西串再一起呢？

## 流程
1. vscode啟動nodemon
2. 依照nodemon.json所寫的內容，自動透過npm執行node.js
3. node.js載入ts-node
4. ts-node會自動使用typescript去動態的編譯.ts檔案並且執行(這個寫在套件裡，我們不用去管)
---
## 範例

### 操作步驟
1. 在launch.json添加
123
```C#
using System.IO;
```