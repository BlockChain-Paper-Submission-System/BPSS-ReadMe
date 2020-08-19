# BPSS

- Repo網址：
> 注意！正式版尚未被丟到該Repo中。
[GitHub](https://github.com/BlockChain-Paper-Submission-System)
- BPSS包含了四個子項目，分別是：
    - BPSS-WebPage
    - BPSS-Blockchain-System
    - BPSS-Paper-Verification-System
    - BPSS-Relayer (沿用) 
- 環境設定
> 為了確保你能順利啟動整個系統，請確保你的電腦的作業系統為Linux/Ubuntu並安裝了以下軟體：
1. Node.js LTS
2. Python ^3.7
3. Docker
4. MongoDB
## BPSS-WebPage
> 主要使用Vue.js開發，該項目作為實際環境中論文上傳的入口。
### 如何使用
1. 前往Repo連結將該專案clone或是下載下來。
2. 打開CMD下指令：
```
cd yourRepoPath
npm install
npm run serve
```
3. 下完指令後，我們可以看到以下畫面：
![](https://i.imgur.com/IouNe2i.png)
此時只要打開瀏覽器並輸入`localhost:8080/`便可以進入該系統。
4. 成功。
![](https://i.imgur.com/BS6gEtK.jpg)

## BPSS-Blockchain-System
> 該項目為Fabric區塊鏈主體，智能合約主要以Node.js搭配Fabric官方所提供的SDK所開發。
> 注意！此項目需要在Ubuntu/Linux環境下執行。
### 如何使用
> 首先，我們必須先啟動區塊鏈網路，再啟動Restful API,等到一切就緒，`BPSS-WebPage`才能成功提交論文/評論...等操作。
1. 前往Repo連結將該專案clone或是下載下來。
2. 打開CMD下指令：
```
cd Fabric-1Org1Channel-Network/basic-network
./start.sh
```
這樣一來，你便成功啟動了區塊鏈網路（撒花）。

3. 接下來，我們要啟動`Restful API`，這邊一樣打開CMD（請開啟新的視窗）下指令：
```
cd Fabric-1Org1Channel-Network/application
npm install //第一次使用請下該指令。
node rest.js
```
接下來，如果你看到視窗印出`node.js應用執行中...`，就代表你成功了。

4. 其他
> To regenerate this material, simply run generate.sh.
> To start the network, run start.sh.
> To stop it, run stop.sh.
> To completely remove all incriminating evidence of the network on your system, run teardown.sh.

## BPSS-Paper-Verification-System
### 執行環境
* 需要安裝Python 3, MongoDB
* 第一次建置環境需要執行`pip install -r requirements.txt`確保系統有所需套件
### 如何使用
1. 開啟系統, `python3 flask_server.py`,預設port為58088
2. 如果需要做資料庫初始化, `python3 mongo_inital.py`

## 總結
- 在暸解每個系統要如何初始化/啟動之後，幫大家總結整個啟動流程的順序：
1. 啟動`BPSS-Paper-Verification-System`
2. 啟動`BPSS-Blockchain-System`
3. 啟動`BPSS-WebPage`
- 補充
> 要暫停任何Node.js應用，請在該CMD視窗按下`Ctrl`+`Ｃ`。

## 詳細交易流程
> 下方中提到的`入口`皆代表`BPSS-WebPage`。
> 以下每個部分進行提交後，皆會產生`transaction id`。
- 提交論文

1. 論文的通訊作者使用`Relayer`加密後，進入`入口`上傳論文檔案讓`驗證系統`進行驗證。
2. 不論驗證結果是否通過，都必須繼續填寫提交資訊進行提交，以利區塊鏈記錄下交易訊息。
3. 送出後，通訊作者會在Email收到通知訊息，訊息包含：`transaction id`以及作者所填寫的重要資料、是否通過`驗證系統`的結果。

- 分派Reviewer

> 研討會的編輯會在BPSS收到新的論文提交時收到通知Email訊息，訊息包含：`transaction id`以及作者所填寫的重要資料、是否通過`驗證系統`的結果。

1. 若新提交的論文通過`驗證系統`的檢驗，編輯需要進入`入口`指派該篇論文的審查委員。
2. 提交後，編輯同樣會收到`transaction id`，以便日後進行查詢。

- 對論文進行評論

> 在指派審查委員後，系統會自動以Email通知審查委員需要審查論文的相關訊息。
1. 經過審查後，審查委員需要進入`入口`提交評論，評論送出後系統會以Email通知編輯，讓編輯將審查結果通知該篇論文的通訊作者。

- 提交審查結果

1. 在收到所有審查委員對於受審論文的評論後，編輯需要進入`入口`填寫相關訊息，並決定投稿論文是否通過審核。
2. 送出結果後，系統便會自動以Email通知通訊作者該投稿是否通過審核。
## BPSS-Paper-Verification-System 的運作流程
> 此系統與WebPage連通, 以http request/response做交互

> 使用者經由WebPage提交論文, 論文會在轉為base64 code並傳送給此系統
> 此系統會需要將base64轉為pdf file, 並做防抄襲驗證, 細部過程如下：
1. base64轉pdf。
2. 對pdf檔做hash, 初判是否已經存在於資料庫。
3. 拆分句子，逐句hash, 產生一個hash list。
4. 與資料庫現有論文“逐篇”比對其hash list的交集。
5. 判定結果並回傳給WebPage。

## 重點：
1. Paper-Verification-System是這個系統最耗時的部分。
2. 由於Fabric網路不需要工作量證明，系統信任授權過的身份所提交的資料因此資料上鏈的速度並不慢。
3. 先前的防抄襲系統是比對資料庫內的所有句子，現改為逐篇比對。
