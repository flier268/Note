---
title: How to use NodeJs with Typescript and ESM module
date: 2020-11-13T17:11:57+08:00
categories: 
    - Node.js
tags:
    - npm
    - nodemon
    - Node.js
    - Typescript
    - ts-node
    - vscode

top_img:
indexing: true #是否包含在搜索系統
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

## 順序流程
1. vscode啟動nodemon
2. 依照nodemon.json所寫的內容，自動透過npm執行node.js
3. node.js載入ts-node
4. ts-node會自動使用typescript去動態的編譯.ts檔案並且執行(這個寫在套件裡，我們不用去管)
---


## 實際操作

* Node.js 14
* ts-node >= 9.0
* nodemon >= 2.0.6

1. 安裝套件
```bash
npm install https://github.com/TypeStrong/ts-node
npm install nodemon
npm install typescript
npm install @types/node
```
install ts-node from github is because of author had fix some issue on github but npm(2020.11.15)

2. 在launch.json的configurations添加
    ```json launch.json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "type": "pwa-node",
                "request": "launch",
                "name": "Server",
                "runtimeExecutable": "npm",
                "restart": true,
                "runtimeArgs": [
                    "run",
                    "start:watch"
                ],
                "skipFiles": [
                    "<node_internals>/**"
                ]
            }
        ]
    }
    ```
3. 在package.json添加三個npm的腳本
    <a name="package.json">
    ```json package.json
    {
        //...
        "scripts":{
            // reference from [this](https://github.com/TypeStrong/ts-node/issues/1007) issue to add a experimental arg
            // add --experimental-specifier-resolution=node to let node ignore extension filename
            "start": "node --loader ts-node/esm --experimental-specifier-resolution=node ./app.ts",
            // use nodemon to debug.nodemon.json is the config of nodemon 
            "start:watch": "nodemon",
            // build typescript project to javascript
            "build": "tsc"
        },
        "type": "module",
        //... 
    }
    ```
    Note: scripts is using when someone call npm and add args like
    ```bash
    npm run build
    ```
4. 在根目錄新增nodemon.json，並填入會用到的參數
    ```json nodemon.json
    {
        "ignore": [
            ".git",
            "node_modules",
            "**/*.test.ts",
            "**/*.spec.ts",
            "dist",
            "temp"
        ],
        "watch": [
            "*"
        ],
        "exec": "npm start",
        "ext": "ts,js,json"
    }
    ```
    各節點的作用如下：
    * "ignore": ignore中的目錄就算變更了，debug也不會熱重啟
    * "watch": 監視除了ignore以外的所有目錄與檔案，如果有變更則熱重啟伺服器
    * "exec": 當執行nodemon後，接下來會進行的動作
    這邊是告訴nodemon要執行[package.json](#package.json)的start
    * "ext:" 監視這三種副檔名的檔案
5. tsconfig.json
    ```json
    {
        "compilerOptions": {
            "target": "ES2020",
            "module": "esNext",
            "lib": [
                "ES2020"
            ],
            "allowJs": true, 
            "strict": true, 
            "moduleResolution": "node", 
            "allowSyntheticDefaultImports": true, 
            "esModuleInterop": true, 
            "skipLibCheck": true, 
            "forceConsistentCasingInFileNames": true
        },
        "include": [
            "app.ts"
        ]
    }
    ```
    * 因為ts-node會把.ts編譯為.js，所以"allowJs"必須要開啟
    * 主要參考自ts-node作者的[issue](https://github.com/TypeStrong/ts-node/issues/1007)
