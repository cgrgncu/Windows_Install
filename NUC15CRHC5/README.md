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
+ 安裝時先把使用者名稱設為「USER」，之後再改名字。
  + 電腦名稱改為「R2MS_Lite_S006」。
  + 使用者名稱改為「S006」。
+ 電腦主機Serial Number: W1ARYZ00Z376SX4
+ 變壓器條碼: E82231019291H55213548HK
+ WINDOWS專業版啟用序號: 8BKN3-4RXJV-JYVJD-CXMQY-39MP4
  + 記得啟用。
+ 2026-09-22重灌
+ 安裝LITE相關軟體(Driver)、(R2MS_Lite_Smart_Scheduler_v20260525a)及(R2MS_Lite_CSV_Viewer_v20251027a):
  + 安裝到:「C:\R2MS_Lite_Smart_Scheduler\R2MS_Lite_Smart_Scheduler.exe」。建立應用程式的捷徑到桌面。
  + 安裝到:「C:\R2MS_Lite_Smart_Scheduler\R2MS_Lite_CSV_Viewer.exe」。建立應用程式的捷徑到桌面。
  + 建立Local資料夾: 「C:\R2MS_Lite_Smart_Scheduler\Local」。
+ RUSTDESK弄好。
  + 如果遇到不能安裝，運行以下「停用管理員核准模式.bat」:
  ```
  ::**************************************************************************
  ::   Name: 停用管理員核准模式.bat
  ::   Author: HsiupoYeh 
  ::   Version: v20260923a
  ::   Description: 自動修改 Windows 登錄檔以停用 UAC 管理員核准模式。
  ::                1. 自動提升至 Administrator 權限。
  ::                2. 修改 HKLM 系統機碼，將 EnableLUA 設為 0。
  ::                3. 停用後需要手動重新開機才能完整套用變更。
  ::                4. 【放置路徑與注意事項】：
  ::                   本檔案可放置於任意目錄執行，執行後會強制關閉系統管理員核准模式。
  ::**************************************************************************
  @echo off
  :: 自動取得管理員權限
  >nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
  if '%errorlevel%' NEQ '0' (
      echo 正在要求系統管理員權限...
      goto UACPrompt
  ) else ( goto gotAdmin )
  :UACPrompt
      echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
      echo UAC.ShellExecute "%~s0", "", "", "runas", 1 >> "%temp%\getadmin.vbs"
      "%temp%\getadmin.vbs"
      exit /B
  :gotAdmin
      if exist "%temp%\getadmin.vbs" ( del "%temp%\getadmin.vbs" )
      pushd "%~dp0"
  
  :: -----------------------------------------
  :: 核心：修改登錄檔（停用管理員核准模式）
  :: -----------------------------------------
  echo 正在停用「所有系統管理員均以管理員核准模式執行」...
  reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /t REG_DWORD /d 0 /f
  
  echo.
  echo ===================================================
  echo  設定已完成！請【手動重新開機】以套用變更。
  echo ===================================================
  echo.
  pause
  ```  
  + 基本上去改好「RustDesk.toml」等等相關檔案並覆蓋到目錄中，重新啟動就好了。 
  + 修改「設定>一般」:
    + 「啟動時檢查更新」取消勾選。這會修改%AppData%\RustDesk\config\RustDesk_local.toml檔案。會增加一個「enable-check-update = 'N'」的文字。
  + 修改「設定>安全」:
    + 「允許遠端使用者更改設定」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「allow-remote-config-modification = 'Y'」的文字。
    + 「啟用IP直接存取」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「direct-server =14 'Y'」的文字。
  + 修改「設定>網路」:
    + 「ID伺服器」填「140.115.21.20」。這會修改%AppData%\RustDesk\config\RustDesk.toml檔案。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。這會修改C:\Windows\ServiceProfiles\LocalService\AppData\Roaming\RustDesk\config\RustDesk2.toml檔案。
+ 安裝「R2MS_Lite遠端資訊伺服器」。
+ 安裝XAMPP(xampp-windows-x64-7.4.27-2-VC15-installer.exe):
  + 我們需要用他的HTTP SERVER，占用HTTP的80與HTTPS的443。要自己啟用為服務。
  + 我們需要用他的FTP SERVER。占用FTP的21。要自己啟用為服務。
  + 預設登入管理者維持不設定密碼，僅有本機可以連線到。
  + 建立登入的使用者「R2MS_Lite_S006」及密碼「4500」，根目錄「C:\R2MS_Lite_Smart_Scheduler\Local」，權限唯讀。
  + 要開放應用程式「C:\xampp\FileZillaFTP\FileZillaServer.exe」通過防火牆。
+ 安裝SFTPGO(sftpgo_v2.7.3_windows_x86_64.exe):
  + 我們只要用他的SFTP，占用SFTP的2022。另外其管理介面會占用8080。
  + 安裝好後，第一次登入進入管理介面:
    + http://localhost:8080/web/admin
    + 會被要求設定管理員帳號密碼。建議管理員帳號為「admin」密碼為「1234」。
  + 目前為止我不確定原因但似乎我沒有被防火牆擋住，因此區域網路的人就可以連接8080網頁。
  + 立刻進去檢查「Admin」設定:
    + http://localhost:8080/web/admin/managers
    + 找到剛剛建立的使用者「admin」，然後最右邊下拉選單選擇「Edit」。
    + 要把這個帳號限制只有本機才能登入成功:
      + 在「Allowed IP/Mask」填入「127.0.0.1/32,::1/128」。
      + 到這裡，雖然8080網頁可以開啟，但是管理員帳號只有本機才登入的進去。
  + 進去「Users」設定:
    + http://localhost:8080/web/admin/user
    + 按右上的「add」。
    + 建立使用者的「Username」填「R2MS_Lite_S006」，「Password」填「4500」，「File system>Root directory」填「C:\R2MS_Lite_Smart_Scheduler\Local」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「R2MS_Lite_S006」登入進去。登進去會擁有所有的讀寫權限。
    + 找到剛剛建立的使用者「R2MS_Lite_S006」，然後最右邊下拉選單選擇「Edit」。
    + 修改「ACLs>Permissions」移除原有的「*」，用下拉選單選出「list」與「download」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「R2MS_Lite_S006」登入進去。登進去只有唯讀權限。
+ 在桌面上放一個README.txt
```
FTP已經設定用戶:
USERNAME: R2MS_Lite_S006
PASSWORD: 4500
唯讀
根目錄為Local資料夾

SFTP已經設定管理員:
USERNAME: admin
PASSWORD: 1234

SFTP已經設定用戶:
USERNAME: R2MS_Lite_S006
PASSWORD: 4500
唯讀
根目錄為Local資料夾
```

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
+ INTEL Thunderbolt 驅動:
  + DELL封裝的 Intel-Thunderbolt-Controller-Driver:
    + _TBTC4_WIN_1.41.1335.0_A14_02
    + 來源: https://www.dell.com/support/home/zh-tw/drivers/driversdetails?driverid=tbtc4
    + 檔案名稱: Intel-Thunderbolt-Controller-Driver_TBTC4_WIN_1.41.1335.0_A14_02.exe    

### 離線安裝
+ 安裝家用版
+ 不要連接網路
+ 隱私權關閉

### 停止WINDOWS更新
+ 用REG檔案暫停更新直到2100年

### 安裝驅動
+ 必須使用管理員身分執行BATCH檔。
+ 安裝完重新開機
+ 安裝網路卡驅動。
+ 安裝完重新開機
+ 安裝Thunderbolt驅動
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
+ 安裝「office 2016」。

### 設定BIOS
+ 「Power > After Power Failure」 = 「Last State」

### 連接網路後開始部分設定
+ 之前沒安裝到「Chrome」的話記得裝一下。
  + 安裝「rustdesk」，並設定ID Server。自訂ID清單:
    + CGRG_PRINTER
+ 啟用Windows (可以先略過，之後找時間處理)
  + 啟用後重新命名電腦。設定>系統>關於>重新命名電腦。設為使用者帳號，但底線改為減號。
+ 安裝印表機軟體 (可以先略過，之後找時間處理)
