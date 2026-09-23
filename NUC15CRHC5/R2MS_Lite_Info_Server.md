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
  curl.exe -u R2MS_Lite_Info_Server:4500 "ftp://cgrg.synology.me:10021/home/"
  ```
  + 測試2(上傳檔案以時間當檔名，hello為內容):
  ```
  echo hello | curl.exe -u R2MS_Lite_Info_Server:4500 -T - "ftp://cgrg.synology.me:10021/home/%date:~0,4%%date:~5,2%%date:~8,2%_%time:~0,2%%time:~3,2%%time:~6,2%.txt"
  ``` 
