# R2MS_Lite遠端資訊伺服器

### 選用FTP Server
+ 開發最簡單，若有安全性需求之後可以移植到SFTP。
+ 用Synology NAS當主機:
  + 檔案服務>FTP:
    + 勾選啟動FTP服務(未加密)。
    + UTF-8編碼，選擇「停用」。
  + 使用者:
    + 名稱: R2MS_Lite_Info_Server
    + 密碼: 4500
  + IP/PORT: ftp://cgrg.synology.me:10021/
  + 依照防火牆限制，有支援ALG的情況下，支援被動式FTP連線。
  + 測試1(查看home目錄下的檔案清單):
  ```
  curl.exe -s -S --connect-timeout 2 -m 4 --no-keepalive -u R2MS_Lite_Info_Server:4500 "ftp://cgrg.synology.me:10021/home/"
  ```
  + 測試2(上傳檔案以時間當檔名，hello為內容):
  ```
  echo hello | curl.exe -s -S --connect-timeout 2 -m 4 --no-keepalive -u R2MS_Lite_Info_Server:4500 -T - "ftp://cgrg.synology.me:10021/home/%date:~0,4%%date:~5,2%%date:~8,2%_%time:~0,2%%time:~3,2%%time:~6,2%.txt"
  ```



### 取得 RustDesk_ID
+ 取得RustDesk ID
```
"C:\Program Files\RustDesk\rustdesk.exe" --get-id | more > "C:\R2MS_Lite_Smart_Scheduler\RustDeskID_now.txt" 
```

### 同步 RustDesk_ID 
+ Sync_RustDesk_ID_v20260923a.bat  !!!請注意填寫正確FTP連線資訊!!!
```
::**************************************************************************
::   Name: Sync_RustDesk_ID_v20260923a.bat
::   Copyright:  
::   Author: HsiupoYeh 
::   Version: v20260923a
::   Description: 自動擷取本機 RustDesk ID 並比對異動，僅在 ID 變更或首次執行時
::                將時間戳記檔名上傳至 Synology NAS FTP 伺服器。
::                1. 透過 PIPE 結合 | more 強制刷出 Console 緩衝區以精準取得 ID。
::                2. 本機維持 RustDeskID_now.txt 與 RustDeskID_old.txt 比對，避免重複上傳。
::                3. curl 上傳整合連線與總傳輸 TIMEOUT，防止網路異常或帳密錯誤時掛起。
::**************************************************************************

@echo off
setlocal enabledelayedexpansion

:: ==========================================
:: 設定檔與路徑定義
:: ==========================================
set "RUSTDESK_EXE=C:\Program Files\RustDesk\rustdesk.exe"
set "WORK_DIR=C:\R2MS_Lite_Smart_Scheduler"

set "NOW_FILE=%WORK_DIR%\RustDeskID_now.txt"
set "OLD_FILE=%WORK_DIR%\RustDeskID_old.txt"

set "FTP_USER=R2MS_Lite_Info_Server"
set "FTP_PASS=4500"
set "FTP_HOST=ftp://cgrg.synology.me:10021/home/"

:: 1. 確保工作目錄存在
if not exist "%WORK_DIR%" mkdir "%WORK_DIR%"

:: 2. 檢查 RustDesk 執行檔是否存在
if not exist "%RUSTDESK_EXE%" (
    echo [錯誤] 找不到 RustDesk 執行檔，請確認安裝路徑。
    exit /b 1
)

:: 3. 取得最新 ID 並寫入 RustDeskID_now.txt
"%RUSTDESK_EXE%" --get-id | more > "%NOW_FILE%"

:: 4. 驗證產生的檔案是否有效（非空檔）
for %%A in ("%NOW_FILE%") do set "FILE_SIZE=%%~zA"
if %FILE_SIZE% LEQ 2 (
    echo [錯誤] 無法取得有效的 RustDesk ID（檔案為空或 RustDesk 服務未啟動）。
    exit /b 1
)

:: 5. 比對 ID 是否變更
if exist "%OLD_FILE%" (
    fc /b "%NOW_FILE%" "%OLD_FILE%" >nul 2>&1
    if !ERRORLEVEL! equ 0 (
        echo [狀態] ID 未變更，無須上傳。
        exit /b 0
    )
)

echo [狀態] 偵測到 ID 變更或首次執行，開始上傳至 NAS...

:: 6. 產生時間戳記檔名並經由管道上傳至 FTP
set "YYYY=%date:~0,4%"
set "MM=%date:~5,2%"
set "DD=%date:~8,2%"
set "HH=%time:~0,2%"
if "%HH:~0,1%"==" " set "HH=0%HH:~1,1%"
set "MIN=%time:~3,2%"
set "SS=%time:~6,2%"

set "REMOTE_FILENAME=RustDesk_%COMPUTERNAME%_%YYYY%%MM%%DD%_%HH%%MIN%%SS%.txt"

type "%NOW_FILE%" | curl.exe -sS --connect-timeout 2 -m 4 --no-keepalive -u %FTP_USER%:%FTP_PASS% -T - "%FTP_HOST%%REMOTE_FILENAME%"

if %ERRORLEVEL% equ 0 (
    echo [成功] FTP 上傳完成：%REMOTE_FILENAME%
    copy /y "%NOW_FILE%" "%OLD_FILE%" >nul
    exit /b 0
) else (
    echo [失敗] FTP 上傳失敗，錯誤碼：%ERRORLEVEL%
    exit /b 1
)

endlocal
```
