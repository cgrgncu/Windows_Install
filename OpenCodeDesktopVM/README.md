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
+ 安裝Chrome
  + 使用線上安裝最新版。 
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
+ 安裝tailscale
  + 官網: https://tailscale.com/
  + 管理頁面: https://console.tailscale.com/admin/machines
  + 申請並登入管理帳號，這裡用cgrg.service帳號。
  + 安裝tailscale-setup-1.104.1.exe
  + 安裝好按下「Get Started」。再按下「Sign in to your metwork」。會被引導到網頁登入請選用GMAIL登入。這裡應該會被引導到Edge，用完可以清除所有快取。
  + 登入成功後會引導到網頁，請按下Connect。然後就可以關閉網頁。軟體中也按下「Close」，安裝完成畫面也按下「Close」。
  + 回到管理頁面進行設定:
    + 注意，VM中的瀏覽器很舊，可能管理頁面會有一些問題，請使用Chrome瀏覽器。建議不要用VM中的瀏覽器設定，避免殘留帳號密碼資料。
    + 使用可信任的非虛擬機電腦瀏覽器登入管理頁面，找到虛擬機電腦(OpenCodeDesktopVM1)對應的裝置，把IP改為:「100.80.0.x」。改好就可以關閉網頁。
  + 可用有安裝tailscale的手機測試，能否利用100.80.0.x登入虛擬機電腦的RustDesk。若成功表示設定正確。
+ 安裝opencode-desktop-win-x64
  + 安裝版本: opencode-desktop-win-x64_v2.0.26.0
  + 手動建立資料夾: 「C:\OpenCode_Projects\000\」
  + 開啟桌面版，新增一個專案並指定剛剛建立的資料夾「C:\OpenCode_Projects\000\」。
  + 選擇模型「Muse Spark 1.3 Free」。
  + 選擇模型速度從「Default」改為「Low」。
  + 跟他對話，「請使用繁體中文回答」，他有回話就表示正常。
  + 可修改「設定>偏好設定>自訂代理程式」為啟用，這樣對話還可以選擇Plan或Build。
  + 用命令提示字元開放伺服器能支援配對功能(其實就是本來只有綁定127.0.0.1不受防火牆限制，現在要改綁定0.0.0.0):
  ```
  set "CLI=%LOCALAPPDATA%\Programs\@opencodedesktop\resources\opencode-cli.exe"
  "%CLI%" service set hostname 0.0.0.0
  "%CLI%" service start
  ```
  + 過程中第一次運作會觸發防火牆問題，請都打勾並允許。
  + 這預設就會占用PORT:49374。
+ 安裝XAMPP
  + 安裝版本: xampp-windows-x64-7.4.27-2-VC15-installer.exe
  + 最精簡安裝，可以取消勾選的都取消掉不要裝。
  + 安裝好之後全都不要設定，整個關閉，包含右下角也要檢查有沒有關掉。
  + 重新用管理者權限開啟「XAMPP Control Panel」。
    + 按下Apache的「Start」，過程中第一次運作會觸發防火牆問題，請都打勾並允許。
    + 按下Apache的「Stop」，再按下Apache前面的紅色叉叉。會跳出安裝服務成功的提示。
    + 重新啟動電腦。
  + 這預設就會占用HTTP的PORT:80。
  + 可用有安裝tailscale的手機測試，能否利用100.80.0.x訪問虛擬機電腦的網頁。若成功表示設定正確。
  + 部屬OpenCode配對的php程式:
    + 複製PHP檔案到「C:\xampp\htdocs」中:
      + C:\xampp\htdocs\index.php  --> 覆蓋舊的
      + C:\xampp\htdocs\OpenCodePair.php  --> 新建立的
      + C:\xampp\htdocs\OpenCodeUpload.php  --> 新建立的
  + 可用有安裝tailscale的手機測試，能否利用100.80.0.x訪問虛擬機電腦的網頁，並依照指示配對。若成功就可以在手機網頁上同步操作
