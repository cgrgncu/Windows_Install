# NUC15CRHC5 安裝說明

## 簡介
+ 主機名稱: NUC 15 Pro Mini PC
+ MODEL: NUC15CRH
+ CPU: Intel Core 5 210H
+ RAM: 16GB
+ SSD: 500GB
+ BIOS設定按鈕預設都會在開機畫面顯示。進入BIOS按F2，選擇開機磁碟按F10。


## 主機清單
### USER#1
+ 命名為USER，之後再改名字。
+ 電腦主機Serial Number: W1ARYZ00Z376SX4
+ 變壓器條碼: E82231019291H55213548HK
+ WINDOWS專業版啟用序號: 8BKN3-4RXJV-JYVJD-CXMQY-39MP4 

### USER#2
+ 命名為USER，之後再改名字。
+ 電腦主機Serial Number: 
+ 變壓器條碼: 

## 安裝步驟
### 安裝用USB製作
+ 使用工具: rufus-4.5.exe
  + 下載網址 https://rufus.ie/zh_TW/
  + 使用標準版即可，若有詢問是否要檢查線上更新可略過。
+ Wondows ISO: 用微軟工具下載的Windows.ISO -> 這是家用版
  + Win 10 22H2
  + 使用autounattend.xml
    + REF: https://github.com/memstechtips/UnattendedWinstall
    + REF2: https://schneegans.de/windows/unattend-generator/
+ ASUS驅動包: 
  + 網址: https://www.asus.com/tw/displays-desktops/nucs/nuc-mini-pcs/asus-nuc-15-pro/helpdesk_download?model2Name=ASUS-NUC-15-Pro-Mini-PC-NUC15CRH
    + 安裝檔名稱: NUC15CR_RPL-R_Driver_INF_Pack_ww45-2025.zip
+ INTEL網路卡驅動:
  + 網址: https://www.intel.com.tw/content/www/tw/zh/download/727998/intel-network-adapter-driver-for-microsoft-windows-11.html
    + 安裝檔名稱: Wired_driver_31.2.2_x64.zip
  

### 離線安裝
+ 安裝家用版
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
+ 之前沒安裝到「Chrome」的話記得裝一下。
  + 安裝「rustdesk」，並設定ID Server。自訂ID清單:
    + CGRG_PRINTER
+ 啟用Windows (可以先略過，之後找時間處理)
  + 啟用後重新命名電腦。設定>系統>關於>重新命名電腦。設為使用者帳號，但底線改為減號。
+ 安裝印表機軟體 (可以先略過，之後找時間處理)
