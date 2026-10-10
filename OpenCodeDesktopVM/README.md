# OpenCodeDesktopVM 安裝說明
+ 虛擬機中的安裝
+ 更新日期: 2026-10-10


## 簡介
+ 虛擬機軟體: Oracle VM VirtualBox v6.1.38
+ 新增:
  + 名稱: Win10_Home_OpenCodeDesktop
  + 機器資料夾: (用預設)
  + 類型: MicroSoft Windows
  + 版本: Windows 10(64-bit)
  + 記憶體大小: 4096[MB]
  + 立即建立虛擬硬碟，選VDI，選動態分配，選100[GB]。
+ 設定:
  + 系統>處理器>數量: 2

## 安裝步驟
### 安裝用的ISO製作
+ 使用工具: NTLite 2026.9.12311 (64-bit)
+ Wondows ISO: 用微軟工具下載的Windows.ISO (這是家用)
  + 加工製作後，塞了一些安裝軟體，做成: Win10Home_VM_ISO.ISO
  + Win 10 22H2
  + 使用autounattend.xml
    + REF: https://github.com/memstechtips/UnattendedWinstall
    + REF2: https://schneegans.de/windows/unattend-generator/

### 離線安裝
+ 安裝家用版:
  + 啟動後選擇ISO檔案:Win10Home_VM_ISO.ISO
+ 不要連接網路
+ 隱私權關閉

### 停止WINDOWS更新
+ 用REG檔案暫停更新直到2100年

### 安裝Guest Additions
+ 安裝好要重開機。

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
+ 工作排程器釘選到工作列。
+ 安裝「VirtualBox6.1」。(非必要)
+ https://recordscreen.io/ 加到我的最愛(非必要)
+ 安裝「office 2016」。(非必要)


