# NUC12WSH 安裝說明

### 簡介
+ 主機名稱: ASUS NUC PRO
+ MODEL: NUC12WSH
+ CPU: INTEL 1260P
+ RAM:16GB(2024-08買的這批，10台)
+ SSD: 500GB(2024-08買的這批，10台)

#### CGRG_PRINTER_USER
+ CGRG 印表機專用主機
+ 電腦主機Serial Number: S4ARAC01F4655TF
+ 變壓器S/N: B3VW43N02D6 ，側邊條碼: 0432-05MF200412002627 ，電線條碼: 1411-028K000X1239S0210

#### R2MS_Lite_S000
+ R2MS Lite 儀器生產測試專用主機
+ 電腦主機Serial Number: S4ARAC01F483FWW
+ 變壓器S/N: B3VW43N02DN ，側邊條碼: 0432-05MF200412002644 ，電線條碼: 1411-028K000X1239S0544
+ 





### 安裝用USB製作
+ 使用工具: rufus-4.5.exe
  + 下載網址 https://rufus.ie/zh_TW/
  + 使用標準版即可，若有詢問是否要檢查線上更新可略過。
+ Wondows ISO: SW_DVD9_Win_Pro_10_22H2.19_64BIT_ChnTrad_Pro_Ent_EDU_N_MLF_X23-74684.ISO
  + Win 10 22H2
  + 使用autounattend.xml
    + REF: https://github.com/memstechtips/UnattendedWinstall
    + REF2: https://schneegans.de/windows/unattend-generator/
+ ASUS驅動包: NUC12WS_non-vPro_INF_Pack_6_28_2024.zip
  + 網址: https://www.asus.com/tw/displays-desktops/nucs/nuc-mini-pcs/nuc-12-pro-mini-pc-for-zoom-room/helpdesk_download/?model2Name=NUC-12-Pro-Mini-PC-for-Zoom-Room
  + 要從WIN 11下載，WIN10照樣可以用WIN11的這個安裝包，但是必須使用管理員身分執行BATCH檔。

### 離線安裝
+ 安裝企業版
+ 不要連接網路
+ 隱私權關閉

### 停止WINDOWS更新
+ 用REG檔案暫停更新直到2100年

### 安裝驅動
+ 必須使用管理員身分執行BATCH檔。
+ 安裝完重新開機

### 手動優化WINDOWS
+ 確認暫停更新
+ 確認時間同步(如果失敗就改伺服器)
+ 設定>裝置>自動播放>關閉
+ 設定>系統>電源與睡眠>永不
+ 工作列>右鍵>關閉新聞等不要的功能
  + Microsoft Store取消釘選
+ 資料夾>資料夾選項>關閉隱私
+ 資料夾>資料夾選項>顯示副檔名
+ Edge>設定>隱私權>網址列與搜尋>GoogleTW(google.com.tw)(https://www.google.com.tw/search?q=%s)
+ 安裝「Chrome」。釘選到工作列。有網路再裝。
+ 安裝「Notepad++」。釘選到工作列。關閉自動更新。
+ 小畫家釘選到工作列。
+ 小算盤釘選到工作列。
+ 安裝「VirtualBox6.1」。
+ https://recordscreen.io/ 加到我的最愛
+ 安裝「office 2019」。

### 設定BIOS
+ 「Power,Performance and Cooling > Secondary Power Settings > After Power Failure」 = 「Last State」

### 連接網路後開始部分設定
+ 啟用Windows (可以先略過，之後找時間處理)
  + 啟用後重新命名電腦。設定>系統>關於>重新命名電腦。設為使用者帳號，但底線改為減號。
+ 安裝印表機軟體 (可以先略過，之後找時間處理)
+ 之前沒安裝到「Chrome」的話記得裝一下。
+ 安裝「rustdesk」，並設定ID Server。
  + CGRG_PRINTER
