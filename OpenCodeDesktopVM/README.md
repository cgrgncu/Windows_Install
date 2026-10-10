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
  + 使用者名稱: USER
  + 無密碼
+ 不要連接網路
+ 隱私權關閉

### 停止WINDOWS更新
+ 用REG檔案暫停更新直到2100年

### 安裝Guest Additions
+ 安裝好要重開機。
+ 重開機後設定:
  + 共用剪貼簿: 雙向
  + 拖放: 雙向

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
+ 安裝snippingtool，並釘選到工作列。
+ 家用版開啟gpedit功能。用管理員身分執行。
+ 啟用舊版Windows相片檢視器。

### 匯出一個初始但未完成的版本
+ 匯出前記得把ISO掛載移除。
+ 匯出格式: OVF 1.0
+ MAC位址原則: 刪除
+ 輸出檔名: Win10_Home_OpenCodeDesktop.ova


### 重新匯入到正確的名稱
+ 匯入名稱修改為 Win10_Home_OpenCodeDesktop_1

### 後續安裝步驟
+ 維持無網路
  + 第一次開機如果有硬體變更可能需要重開機。
  + 啟用Windows
+ 連接網路。有必要時請重開機等待網路生效。
+ 變更電腦名稱:「設定」＞「系統」＞「關於」＞「重新命名此電腦（進階）」，電腦名稱改為「OpenCodeDesktopVM1」。
  + 設定變更電腦名稱後重新啟動電腦。
+ 安裝RustDesk
  + 先用管理者權限運行:「停用管理員核准模式.bat」，完成後請手動重新啟動電腦。
  + 安裝rustdesk-1.3.1-x86_64.msi
  + 修改「設定>一般」:
    + 「啟動時檢查更新」取消勾選。
  +  修改「設定>安全」:
    + 「允許遠端使用者更改設定」勾選，連接埠保持預設不修改。
    + 「啟用IP直接存取」勾選，連接埠保持預設不修改。
  + 用命令提示字元修改固定密碼，這裡示範把密碼改為「1234」:
  ```
  "C:\Program Files\RustDesk\RustDesk.exe" "--password" "1234"
  ```

