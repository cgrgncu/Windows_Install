# NUC12WSH 安裝說明

## 簡介
+ 主機名稱: ASUS NUC PRO
+ MODEL: NUC12WSH
+ CPU: INTEL 1260P
+ RAM:16GB(2024-08買的這批，10台)
+ SSD: 500GB(2024-08買的這批，10台)
+ BIOS設定按鈕預設都會在開機畫面顯示。進入BIOS按F2，選擇開機磁碟按F10。


## 主機清單
### CGRG_PRINTER
+ CGRG 印表機專用主機
+ 電腦主機Serial Number: S4ARAC01F4655TF
+ 變壓器S/N: B3VW43N02D6 ，側邊條碼: 0432-05MF200412002627 ，電線條碼: 1411-028K000X1239S0210
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40097

### R2MS_Lite_S000
+ R2MS Lite 儀器生產測試專用主機
+ 電腦主機Serial Number: S4ARAC01F483FWW
+ 變壓器S/N: B3VW43N02DN ，側邊條碼: 0432-05MF200412002644 ，電線條碼: 1411-028K000X1239S0544
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40095

### TAIP_DataCenter
+ 太平山地電阻法連續觀測站資料分析專用主機
+ 電腦主機Serial Number: S4ARAC01F468BEX
+ 變壓器S/N: B3VW43N02D7 ，側邊條碼: 0432-05MF200412002625 ，電線條碼: 1411-028K000X1243C1267
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40091

### R2MS_Lite_S003
+ R2MS Lite S003專用主機
+ 電腦主機Serial Number: S4ARAC01F4794S5
+ 變壓器S/N: B3VW43N0294 ，側邊條碼: 0432-05MF200412002613 ，電線條碼: 1411-028K000X1243C0508
+ 2025-09-03重灌
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40094

### R2MS_Lite_S004
+ R2MS Lite S004專用主機
+ 電腦主機Serial Number: S4ARAC01F0239ZN
+ 變壓器S/N: B3VW43N0294 ，側邊條碼: 0432-05MF200412002507 ，電線條碼: 1411-028K000X1243C1698
+ 2025-08-07重灌
+ 2026-05-18重灌
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40092

### CGRG_S006
+ CGRG S006專用主機
+ 電腦主機Serial Number: S4ARAC01F4928JK
+ 變壓器S/N: B3VW43N02ED ，側邊條碼: 0432-05MF200412002743 ，電線條碼: 1411-028K000X1243C1282
+ 2026-05-25重灌
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40093
+ RUSTDESK弄好。
  + 修改「設定>一般」:
    + 「啟動時檢查更新」取消勾選。這會修改%AppData%\RustDesk\config\RustDesk_local.toml檔案。會增加一個「enable-check-update = 'N'」的文字。
  + 修改「設定>安全」:
    + 「允許遠端使用者更改設定」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「allow-remote-config-modification = 'Y'」的文字。
    + 「啟用IP直接存取」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「direct-server =14 'Y'」的文字。
  + 修改「設定>網路」:
    + 「ID伺服器」填「140.115.21.20」。這會修改%AppData%\RustDesk\config\RustDesk.toml檔案。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。這會修改C:\Windows\ServiceProfiles\LocalService\AppData\Roaming\RustDesk\config\RustDesk2.toml檔案。
+ 安裝XAMPP(xampp-windows-x64-7.4.27-2-VC15-installer.exe):
  + 我們需要用他的HTTP SERVER，占用HTTP的80與HTTPS的443。要自己啟用為服務。
  + 我們需要用他的FTP SERVER。占用FTP的21。要自己啟用為服務。
  + 預設登入管理者維持不設定密碼，僅有本機可以連線到。
  + 建立登入的使用者「CGRG_S007」及密碼「4500」，根目錄「C:\R2MS_Lite_Smart_Scheduler\Local」，權限唯讀。
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
    + 建立使用者的「Username」填「CGRG_S006」，「Password」填「4500」，「File system>Root directory」填「C:\R2MS_Lite_Smart_Scheduler\Local」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「CGRG_S006」登入進去。登進去會擁有所有的讀寫權限。
    + 找到剛剛建立的使用者「CGRG_S006」，然後最右邊下拉選單選擇「Edit」。
    + 修改「ACLs>Permissions」移除原有的「*」，用下拉選單選出「list」與「download」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「CGRG_S006」登入進去。登進去只有唯讀權限。
+ 在桌面上放一個README.txt
```
FTP已經設定用戶:
USERNAME: CGRG_S006
PASSWORD: 4500
唯讀
根目錄為Local資料夾

SFTP已經設定管理員:
USERNAME: admin
PASSWORD: 1234

SFTP已經設定用戶:
USERNAME: CGRG_S006
PASSWORD: 4500
唯讀
根目錄為Local資料夾
```

### CGRG_S007
+ CGRG S007專用主機
+ 電腦主機Serial Number: S4ARAC01F4928JK
+ 變壓器S/N: B3VW43N02ED ，側邊條碼: 0432-05MF200412002743 ，電線條碼: 1411-028K000X1243C1282
+ 2026-06-04重灌
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40098
+ 用指定的autounattend.xml安裝，裡面有寫要創建的帳號。
+ 遇到「讓我們將您連線到網路」問題時:
  + 按下「shift」+「F10」。
  + 輸入:
    ```
    cd oobe
    msoobe.exe /bypassnro
    ```
  + 等他大約5分鐘，會一直卡在「請稍後...」的轉圈圈畫面中。我不確定背景跑的東西要跑多久會跑完，但保險起見讓他轉一下。
  + 休息一下回來，應該還在轉圈圈。接著短按主機的電源鍵，使他變成登出狀態，螢幕會黑掉。
  + 再按一次主機的電源鍵會喚醒，此時會出現「defaultuser0」這個臨時帳號，提示你密碼不正確，按確定多次之後，都登不進去，就會顯示其他帳號的清單給你選。
  + 選擇那個用autounattend.xml創建的帳號，開始等他跑「歡迎」之類的初始化設定。然後螢幕可能就整個黑掉了。黑掉很久沒反應就拔掉電源，重新開機。
  + 自動登入後就出現熟悉的桌面了。臨時帳號也不會存在。
  + 後續照標準方式繼續設定。
+ 安裝LITE相關軟體(Driver)、(R2MS_Lite_Smart_Scheduler_v20260525a)及(R2MS_Lite_CSV_Viewer_v20251027a):
  + 安裝到:「C:\R2MS_Lite_Smart_Scheduler\R2MS_Lite_Smart_Scheduler.exe」。建立應用程式的捷徑到桌面。
  + 安裝到:「C:\R2MS_Lite_Smart_Scheduler\R2MS_Lite_CSV_Viewer.exe」。建立應用程式的捷徑到桌面。
  + 建立Local資料夾: 「C:\R2MS_Lite_Smart_Scheduler\Local」。
+ RUSTDESK弄好。
  + 修改「設定>一般」:
    + 「啟動時檢查更新」取消勾選。這會修改%AppData%\RustDesk\config\RustDesk_local.toml檔案。會增加一個「enable-check-update = 'N'」的文字。
  + 修改「設定>安全」:
    + 「允許遠端使用者更改設定」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「allow-remote-config-modification = 'Y'」的文字。
    + 「啟用IP直接存取」勾選，連接埠保持預設不修改。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。會增加一個「direct-server =14 'Y'」的文字。
  + 修改「設定>網路」:
    + 「ID伺服器」填「140.115.21.20」。這會修改%AppData%\RustDesk\config\RustDesk.toml檔案。這會修改%AppData%\RustDesk\config\RustDesk2.toml檔案。這會修改C:\Windows\ServiceProfiles\LocalService\AppData\Roaming\RustDesk\config\RustDesk2.toml檔案。
+ 安裝XAMPP(xampp-windows-x64-7.4.27-2-VC15-installer.exe):
  + 我們需要用他的HTTP SERVER，占用HTTP的80與HTTPS的443。要自己啟用為服務。
  + 我們需要用他的FTP SERVER。占用FTP的21。要自己啟用為服務。
  + 預設登入管理者維持不設定密碼，僅有本機可以連線到。
  + 建立登入的使用者「CGRG_S007」及密碼「4500」，根目錄「C:\R2MS_Lite_Smart_Scheduler\Local」，權限唯讀。
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
    + 建立使用者的「Username」填「CGRG_S007」，「Password」填「4500」，「File system>Root directory」填「C:\R2MS_Lite_Smart_Scheduler\Local」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「CGRG_S007」登入進去。登進去會擁有所有的讀寫權限。
    + 找到剛剛建立的使用者「CGRG_S007」，然後最右邊下拉選單選擇「Edit」。
    + 修改「ACLs>Permissions」移除原有的「*」，用下拉選單選出「list」與「download」。記得要SAVE。
    + 到這裡，8080網頁可以開啟，可以用使用者「CGRG_S007」登入進去。登進去只有唯讀權限。
+ 在桌面上放一個README.txt
```
FTP已經設定用戶:
USERNAME: CGRG_S007
PASSWORD: 4500
唯讀
根目錄為Local資料夾

SFTP已經設定用戶:
USERNAME: CGRG_S007
PASSWORD: 4500
唯讀
根目錄為Local資料夾
```
### USER (晶晶使用)
+ 晶晶個人使用主機
+ 電腦主機Serial Number: S4ARAC01F455BPN
+ 變壓器S/N: B3VW43N02E5 ，側邊條碼: 0432-05MF200412002610 ，電線條碼: 1411-028K000X1243C0569
+ 財產單據: 11309A3071
+ 財產編號: 3140101-03-40096
  
## 安裝步驟
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
  + 要從WIN11下載，WIN10照樣可以用WIN11的這個安裝包，但是必須使用管理員身分執行BATCH檔。

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
+ 之前沒安裝到「Chrome」的話記得裝一下。
  + 安裝「rustdesk」，並設定ID Server。自訂ID清單:
    + CGRG_PRINTER
+ 啟用Windows (可以先略過，之後找時間處理)
  + 啟用後重新命名電腦。設定>系統>關於>重新命名電腦。設為使用者帳號，但底線改為減號。
+ 安裝印表機軟體 (可以先略過，之後找時間處理)


    
