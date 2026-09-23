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
::                4. 【放置路徑與目錄結構範例】：
::                   本檔案必須固定存放於 C:\R2MS_Lite_Smart_Scheduler\Sync_RustDesk_ID\
::                   
::                   目錄完整結構如下：
::                   C:\Sync_RustDesk_ID\
::                   ├── Sync_RustDesk_ID_v20260923a.bat          (本核心腳本)
::                   ├── Install_Sync_RustDesk_ID_Task.bat        (自動部署排程)
::                   ├── Uninstall_Sync_RustDesk_ID_Task.bat      (自動移除排程)
::                   ├── RustDeskID_now.txt                       (自動生成: 最新擷取 ID)
::                   └── RustDeskID_old.txt                       (自動生成: 已上傳基準 ID)
::**************************************************************************

@echo off
setlocal enabledelayedexpansion

:: ==========================================
:: 設定檔與路徑定義
:: ==========================================
set "RUSTDESK_EXE=C:\Program Files\RustDesk\rustdesk.exe"
set "WORK_DIR=C:\R2MS_Lite_Smart_Scheduler\Sync_RustDesk_ID"

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

### 工作排程器中安裝腳本
```
::**************************************************************************
::   Name: Install_Sync_RustDesk_ID_Task.bat
::   Author: HsiupoYeh 
::   Version: v20260923a
::   Description: 自動部署 Sync_RustDesk_ID 批次檔至 Windows 工作排程器。
::                1. 自動提升至 Administrator 權限。
::                2. 綁定 SYSTEM 帳號，開機未登入亦可執行。
::                3. 設定每 1 小時輪詢一次，並自動觸發首次測試。
::                4. 【放置路徑與目錄結構範例】：
::                   本檔案必須固定存放於 C:\Sync_RustDesk_ID\
::                   
::                   目錄完整結構如下：
::                   C:\Sync_RustDesk_ID\
::                   ├── Sync_RustDesk_ID_v20260923a.bat          (核心邏輯腳本)
::                   ├── Install_Sync_RustDesk_ID_Task.bat        (本安裝腳本)
::                   ├── Uninstall_Sync_RustDesk_ID_Task.bat      (自動移除腳本)
::                   ├── RustDeskID_now.txt                       (自動生成: 最新擷取 ID)
::                   └── RustDeskID_old.txt                       (自動生成: 已上傳基準 ID)
::**************************************************************************

@echo off
setlocal enabledelayedexpansion

:: 1. 檢查並自動取得 Administrator 權限
net session >nul 2>&1
if %errorLevel% neq 0 (
    echo [提示] 正在取得系統管理員權限...
    powershell -Command "Start-Process '%~0' -Verb RunAs"
    exit /b
)

set "TASK_NAME=R2MS_RustDesk_ID_Sync"
set "BAT_PATH=C:\Sync_RustDesk_ID\Sync_RustDesk_ID_v20260923a.bat"
set "WORK_DIR=C:\Sync_RustDesk_ID"

echo ===================================================
echo   開始部署 RustDesk ID 自動同步工作排程
echo ===================================================

:: 2. 檢查目標批次檔是否存在
if not exist "%BAT_PATH%" (
    echo [錯誤] 找不到目標批次檔：%BAT_PATH%
    echo 請確認檔案已放置於 %WORK_DIR% 目錄下。
    pause
    exit /b 1
)

:: 3. 建立工作排程 (SYSTEM 帳號、每小時執行、最高權限)
schtasks /create /tn "%TASK_NAME%" /tr "\"%BAT_PATH%\"" /sc hourly /mo 1 /ru "SYSTEM" /rl HIGHEST /f >nul 2>&1

set "CREATE_RET=%ERRORLEVEL%"

if %CREATE_RET% neq 0 goto :FAIL

echo [成功] 工作排程 [%TASK_NAME%] 已成功建立！
echo [資訊] 執行帳號: SYSTEM (開機未登入亦可執行)
echo [資訊] 執行頻率: 每 1 小時
echo.
echo 正在進行首次手動觸發測試...
schtasks /run /tn "%TASK_NAME%" >nul 2>&1
echo [完成] 已觸發測試，請至 NAS 或工作目錄確認上傳結果。
echo.
echo ---------------------------------------------------
echo [狀態] 目前工作排程登錄資訊：
echo ---------------------------------------------------
schtasks /query /tn "%TASK_NAME%"
echo ---------------------------------------------------
goto :END

:FAIL
echo [錯誤] 工作排程建立失敗，錯誤碼：%CREATE_RET%

:END
echo ===================================================
echo 部署作業完成。
pause
endlocal
```

### 工作排程器中移除腳本
```
::**************************************************************************
::   Name: Uninstall_Sync_RustDesk_ID_Task.bat
::   Author: HsiupoYeh 
::   Version: v20260923a
::   Description: 安全刪除 R2MS RustDesk ID 自動同步工作排程與相關檔案。
::                1. 自動提升至 Administrator 權限。
::                2. 安全刪除工作排程器中的 R2MS_RustDesk_ID_Sync 工作。
::                3. 提供清理本機 ID 記錄檔 (now.txt / old.txt) 的選項。
::                4. 【放置路徑與目錄結構範例】：
::                   本檔案必須固定存放於 C:\Sync_RustDesk_ID\
::                   
::                   目錄完整結構如下：
::                   C:\Sync_RustDesk_ID\
::                   ├── Sync_RustDesk_ID_v20260923a.bat          (核心邏輯腳本)
::                   ├── Install_Sync_RustDesk_ID_Task.bat        (自動部署腳本)
::                   ├── Uninstall_Sync_RustDesk_ID_Task.bat      (本移除腳本)
::                   ├── RustDeskID_now.txt                       (自動生成: 最新擷取 ID)
::                   └── RustDeskID_old.txt                       (自動生成: 已上傳基準 ID)
::**************************************************************************

@echo off
setlocal enabledelayedexpansion

:: 1. 檢查並自動取得 Administrator 權限
net session >nul 2>&1
if %errorLevel% neq 0 (
    echo [提示] 正在取得系統管理員權限...
    powershell -Command "Start-Process '%~0' -Verb RunAs"
    exit /b
)

set "TASK_NAME=R2MS_RustDesk_ID_Sync"
set "WORK_DIR=C:\Sync_RustDesk_ID"

echo ===================================================
echo   開始移除 RustDesk ID 自動同步工作排程
echo ===================================================

:: 2. 檢查並刪除工作排程
schtasks /query /tn "%TASK_NAME%" >nul 2>&1
if %ERRORLEVEL% equ 0 (
    schtasks /delete /tn "%TASK_NAME%" /f >nul 2>&1
    if !ERRORLEVEL! equ 0 (
        echo [成功] 工作排程 [%TASK_NAME%] 已順利移除。
    ) else (
        echo [錯誤] 刪除工作排程失敗。
    )
) else (
    echo [提示] 系統中未發現 [%TASK_NAME%] 工作排程。
)

echo.
:: 3. 詢問是否清理暫存文字檔
set /p CHOICE="是否連同本機 ID 記錄檔 (now.txt / old.txt) 一併清除？ (Y/N): "
if /i "%CHOICE%"=="Y" (
    if exist "%WORK_DIR%\RustDeskID_now.txt" del /f /q "%WORK_DIR%\RustDeskID_now.txt"
    if exist "%WORK_DIR%\RustDeskID_old.txt" del /f /q "%WORK_DIR%\RustDeskID_old.txt"
    echo [成功] 已清理 ID 歷史記錄檔。
)

echo.
echo ---------------------------------------------------
echo [狀態] 目前工作排程狀態確認：
echo ---------------------------------------------------
schtasks /query /tn "%TASK_NAME%" 2>nul
if %ERRORLEVEL% neq 0 (
    echo [確認] 系統中已無此排程，工作已完全清除。
)
echo ---------------------------------------------------

echo ===================================================
echo 移除作業完畢。
pause
endlocal
```
