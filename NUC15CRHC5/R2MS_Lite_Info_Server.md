# R2MS_Lite遠端資訊伺服器

### 選用PHP Server
+ 開發最簡單，可用Synology NAS當主機，或使用XAMPP。
+ 語法需要相容PHP 7.0。
+ 製作方式:
  + 在「web」目錄下建立一個「R2MS_Lite_Info_Server」資料夾
  + 建立「index.php」
  ```
  <!DOCTYPE html>
  <html lang="zh-TW">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>R2MS_Lite 狀態查詢</title>
      <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
      <style>
          body {
              background-color: #f8f9fa;
              font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
          }
          .test-card {
              border: none;
              border-radius: 10px;
              box-shadow: 0 4px 12px rgba(0,0,0,0.05);
              background: #ffffff;
          }
          /* 針對 iOS Safari 最佳化的正圓形問號按鈕樣式 */
          .btn-help-circle {
              width: 26px;
              height: 26px;
              min-width: 26px;
              min-height: 26px;
              padding: 0;
              border-radius: 50% !important;
              display: inline-flex !important;
              align-items: center !important;
              justify-content: center !important;
              font-size: 14px;
              font-weight: 600;
              line-height: 1 !important;
              -webkit-appearance: none; /* 消除 iOS 按鈕預設圓角變形 */
              box-sizing: border-box;
              flex-shrink: 0;
          }
      </style>
  </head>
  <body>
  
  <div class="container py-5">
      <div class="row justify-content-center">
          <div class="col-12 col-md-10 col-lg-8">
              <div class="card test-card p-4 p-sm-5">
  
                  <!-- 標題區塊與重置按鈕 -->
                  <div class="d-flex justify-content-between align-items-center border-bottom pb-3 mb-4">
                      <h2 class='fw-bold text-dark m-0'>
                          <span class='fs-5 text-muted fw-normal d-block mb-1'>設備狀態同步系統</span>R2MS_Lite 狀態查詢
                      </h2>
                      <!-- 重置按鈕 -->
                      <a href="<?php echo strtok($_SERVER["PHP_SELF"], '?'); ?>" class="btn btn-outline-secondary btn-sm text-nowrap">
                          重置
                      </a>
                  </div>
  
                  <?php
                  // 1. 讀取同目錄下的 devices.json 取得裝置清單與各自的 web_auth_code
                  $json_file = __DIR__ . '/devices.json';
                  $device_map = array();
                  $json_exists = file_exists($json_file);
  
                  if ($json_exists) {
                      $json_data = file_get_contents($json_file);
                      if ($json_data !== false) {
                          $decoded = json_decode($json_data, true);
                          if (is_array($decoded)) {
                              foreach ($decoded as $item) {
                                  if (is_array($item) && isset($item['id'])) {
                                      $dev_id = $item['id'];
                                      $device_map[$dev_id] = array(
                                          'web_auth_code' => isset($item['web_auth_code']) ? $item['web_auth_code'] : ''
                                      );
                                  } elseif (is_string($item)) {
                                      $device_map[$item] = array('web_auth_code' => '');
                                  }
                              }
                          }
                      }
                  }
  
                  $device_list = array_keys($device_map);
                  $has_devices = !empty($device_list);
                  
                  // 取得表單提交的參數 (支援 GET 與 POST 混合)
                  $custom_id = isset($_REQUEST['custom_id']) ? trim($_REQUEST['custom_id']) : '';
                  $query_date = isset($_REQUEST['query_date']) ? trim($_REQUEST['query_date']) : '';
                  $input_web_auth_code = isset($_POST['web_auth_code']) ? trim($_POST['web_auth_code']) : '';
                  $is_post_action = ($_SERVER['REQUEST_METHOD'] === 'POST');
  
                  // 驗證自訂 ID 是否合法
                  if ($custom_id !== '' && !in_array($custom_id, $device_list, true)) {
                      $custom_id = '';
                  }
  
                  // 取得今天日期 (YYYY-MM-DD)
                  $today_str = date("Y-m-d");
  
                  // 若未指定日期則預設為今天
                  if (empty($query_date)) {
                      $query_date = $today_str;
                  }
                  
                  // 驗證日期格式是否正確
                  $d_obj = DateTime::createFromFormat('Y-m-d', $query_date);
                  if (!$d_obj || $d_obj->format('Y-m-d') !== $query_date) {
                      $query_date = $today_str;
                  }
  
                  $is_submitted = ($custom_id !== '');
                  $auth_error = false;
                  $is_authorized = false;
  
                  // 驗證網頁認證碼邏輯
                  if ($is_submitted) {
                      $required_auth_code = isset($device_map[$custom_id]['web_auth_code']) ? $device_map[$custom_id]['web_auth_code'] : '';
                      
                      if ($required_auth_code !== '') {
                          // 如果該裝置有設定認證碼
                          if ($is_post_action && $input_web_auth_code !== '') {
                              // 使用者是透過 POST 送出認證碼
                              if ($input_web_auth_code === $required_auth_code) {
                                  $is_authorized = true;
                              } else {
                                  $auth_error = true;
                              }
                          } else {
                              // 第一次透過 GET 選擇裝置，尚未輸入認證碼
                              $is_authorized = false;
                          }
                      } else {
                          // 該裝置沒有設定認證碼，直接放行
                          $is_authorized = true;
                      }
                  }
                  ?>
  
                  <!-- 查詢表單：支援選擇裝置與指定日期 -->
                  <form method="GET" action="" class="row g-3 mb-4 p-3 bg-light rounded border">
                      <div class="col-12 col-sm-6">
                          <label for="custom_id" class="form-label fw-bold text-secondary">請選擇要查詢的自訂裝置 ID：</label>
                          <select class="form-select" id="custom_id" name="custom_id" <?php echo !$has_devices ? 'disabled' : ''; ?>>
                              <?php if (!$has_devices): ?>
                                  <option value="">(無可用的裝置清單)</option>
                              <?php else: ?>
                                  <option value="" disabled <?php echo $custom_id === '' ? 'selected' : ''; ?>>-- 請選擇裝置 --</option>
                                  <?php foreach ($device_list as $dev): ?>
                                      <option value="<?php echo htmlspecialchars($dev, ENT_QUOTES, 'UTF-8'); ?>" <?php echo ($custom_id === $dev) ? 'selected' : ''; ?>>
                                          <?php echo htmlspecialchars($dev, ENT_QUOTES, 'UTF-8'); ?>
                                      </option>
                                  <?php endforeach; ?>
                              <?php endif; ?>
                          </select>
                      </div>
  
                      <div class="col-12 col-sm-3">
                          <label for="query_date" class="form-label fw-bold text-secondary">查詢日期：</label>
                          <input type="date" class="form-control" id="query_date" name="query_date" value="<?php echo htmlspecialchars($query_date, ENT_QUOTES, 'UTF-8'); ?>" <?php echo !$has_devices ? 'disabled' : ''; ?>>
                      </div>
  
                      <div class="col-12 col-sm-3 d-flex align-items-end">
                          <button type="submit" class="btn btn-primary w-100" <?php echo !$has_devices ? 'disabled' : ''; ?>>立即查詢</button>
                      </div>
                  </form>
  
                  <?php
                  if (!$has_devices) {
                      echo "<div class='alert alert-danger text-center py-4' role='alert'>";
                      echo "<h5 class='alert-heading mb-1'>找不到 devices.json 檔案</h5>";
                      echo "<p class='mb-0 text-muted small'>請確認同目錄下是否存在 <code>devices.json</code> 且內容格式正確。</p>";
                      echo "</div>";
                  } elseif (!$is_submitted) {
                      echo "<div class='alert alert-info text-center py-4' role='alert'>";
                      echo "<h5 class='alert-heading mb-1'>請從上方選擇裝置並點擊「立即查詢」</h5>";
                      echo "<p class='mb-0 text-muted small'>預設將直接讀取該裝置今日的所有回報紀錄，您也可以自由切換指定日期。</p>";
                      echo "</div>";
                  } elseif ($auth_error) {
                      // 輸入錯誤的認證碼後送出，顯示錯誤提示與輸入表單
                      echo "<div class='alert alert-danger mb-3' role='alert'>網頁認證碼錯誤，請重新輸入裝置 <strong>[" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "]</strong> 的認證碼。</div>";
                      
                      echo "<form method='POST' action='' class='p-4 bg-light rounded border'>";
                      echo "<input type='hidden' name='custom_id' value='" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "'>";
                      echo "<input type='hidden' name='query_date' value='" . htmlspecialchars($query_date, ENT_QUOTES, 'UTF-8') . "'>";
                      
                      echo "<div class='mb-3'>";
                      echo "<label for='web_auth_code' class='form-label fw-bold text-secondary'>請輸入裝置 <strong>[" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "]</strong> 的網頁認證碼：</label>";
                      echo "<input type='text' class='form-control' id='web_auth_code' name='web_auth_code' autocomplete='off' required>";
                      echo "</div>";
                      echo "<button type='submit' class='btn btn-danger w-100'>確認驗證並查詢</button>";
                      echo "</form>";
  
                  } elseif (!$is_authorized) {
                      // 第一次選擇該裝置，直接請使用者輸入網頁認證碼
                      echo "<form method='POST' action='' class='p-4 bg-light rounded border'>";
                      echo "<input type='hidden' name='custom_id' value='" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "'>";
                      echo "<input type='hidden' name='query_date' value='" . htmlspecialchars($query_date, ENT_QUOTES, 'UTF-8') . "'>";
                      
                      echo "<div class='mb-3'>";
                      echo "<label for='web_auth_code' class='form-label fw-bold text-secondary'>裝置 <strong>[" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "]</strong> 需要網頁認證碼才能查詢：</label>";
                      echo "<input type='text' class='form-control' id='web_auth_code' name='web_auth_code' placeholder='請輸入網頁認證碼' autocomplete='off' required>";
                      echo "</div>";
                      echo "<button type='submit' class='btn btn-primary w-100'>送出認證碼查詢</button>";
                      echo "</form>";
  
                  } else {
                      // 認證成功或無須認證，開始讀取日誌檔案
                      $year  = date("Y", strtotime($query_date));
                      $month = date("m", strtotime($query_date));
                      $day   = date("d", strtotime($query_date));
                      $file_date_tag = $year . $month . $day;
  
                      // 防禦性路徑檢查：確保 custom_id 不包含危險字元
                      $safe_custom_id = preg_replace('/[^a-zA-Z0-9_\-]/', '_', $custom_id);
                      $target_log_file = __DIR__ . '/R2MS_Lite_Info_log/' . $safe_custom_id . '/' . $year . '/' . $month . '/' . $file_date_tag . '_R2MS_Lite_Info_log.txt';
  
                      if (!file_exists($target_log_file)) {
                          echo "<div class='alert alert-warning mt-3' role='alert'>指定日期 (<strong>$query_date</strong>) 尚無裝置 <strong>[$custom_id]</strong> 的回報紀錄檔案。</div>";
                      } else {
                          // 讀取原始檔案內容供 Log Modal 使用
                          $raw_log_content = file_get_contents($target_log_file);
  
                          $lines = file($target_log_file, FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
                          if ($lines === false || empty($lines)) {
                              echo "<div class='alert alert-warning mt-3' role='alert'>指定日期 (<strong>$query_date</strong>) 的日誌檔案內容為空。</div>";
                          } else {
                              $all_records = array();
                              $malformed_count = 0;
  
                              foreach ($lines as $line) {
                                  $line = trim($line);
                                  if ($line === '') {
                                      continue;
                                  }
  
                                  if (preg_match('/^\[(.*?)\](.*?): (.*)$/', $line, $matches)) {
                                      $client_ip = trim($matches[1]);
                                      $timestamp = trim($matches[2]);
                                      $json_str = trim($matches[3]);
                                      
                                      $decoded_json = json_decode($json_str, true);
                                      
                                      if (json_last_error() === JSON_ERROR_NONE && is_array($decoded_json)) {
                                          $all_records[] = array(
                                              'ip' => $client_ip,
                                              'time' => $timestamp,
                                              'data' => $decoded_json
                                          );
                                      } else {
                                          $malformed_count++;
                                      }
                                  } else {
                                      $malformed_count++;
                                  }
                              }
  
                              if ($malformed_count > 0) {
                                  echo "<div class='alert alert-secondary py-2 px-3 small mb-3' role='alert'>提示：在此日誌中自動略過了 <strong>$malformed_count</strong> 筆格式異常或無法解析的無效資料行。</div>";
                              }
  
                              if (empty($all_records)) {
                                  echo "<div class='alert alert-warning mt-3' role='alert'>指定日期 (<strong>$query_date</strong>) 的日誌中找不到任何符合格式的有效紀錄。</div>";
                              } else {
                                  usort($all_records, function($a, $b) {
                                      return strcmp($b['time'], $a['time']);
                                  });
  
                                  echo "<div class='p-3 mb-3 rounded' style='background-color: #e8f5e9;'><span style='color:green; font-weight:bold;'>成功讀取 [<strong>" . htmlspecialchars($custom_id, ENT_QUOTES, 'UTF-8') . "</strong>] 在 <strong>$query_date</strong> 的所有回報紀錄，共計 <strong>" . count($all_records) . "</strong> 筆：</span></div>";
  
                                  // 標題旁邊加上「Log 按鈕」與「iOS 相容的圓形問號按鈕」
                                  echo "<div class='d-flex justify-content-between align-items-center mb-3'>";
                                  echo "<h4 class='fw-bold text-dark m-0'>$query_date 完整回報紀錄與狀態：</h4>";
                                  echo "<div class='d-flex align-items-center gap-2'>";
                                  echo "<button type='button' class='btn btn-outline-dark btn-sm text-nowrap' data-bs-toggle='modal' data-bs-target='#rawLogModal'>Log</button>";
                                  echo "<button type='button' class='btn btn-outline-secondary btn-help-circle' data-bs-toggle='modal' data-bs-target='#infoModal' title='帳戶差異說明'>?</button>";
                                  echo "</div>";
                                  echo "</div>";
  
                                  echo "<ul class='list-group mb-4'>";
  
                                  $count = 1;
                                  foreach ($all_records as $rec) {
                                      $time = $rec['time'];
                                      $ip = $rec['ip'];
                                      $d = $rec['data'];
  
                                      $comp_name = isset($d['computer_name']) ? $d['computer_name'] : 'N/A';
                                      $username = isset($d['user_name']) ? $d['user_name'] : 'N/A';
                                      $rd_now = isset($d['rustdesk_id_now']) ? $d['rustdesk_id_now'] : 'N/A';
                                      $rd_old = isset($d['rustdesk_id_old']) ? $d['rustdesk_id_old'] : 'N/A';
                                      $disk_now = isset($d['disk_c_free_now']) && is_numeric($d['disk_c_free_now']) ? $d['disk_c_free_now'] : 0;
                                      $disk_old = isset($d['disk_c_free_old']) && is_numeric($d['disk_c_free_old']) ? $d['disk_c_free_old'] : 0;
  
                                      $disk_now_gb = number_format($disk_now / (1024 * 1024 * 1024), 2);
                                      $disk_diff_bytes = $disk_now - $disk_old;
                                      $disk_diff_mb = number_format($disk_diff_bytes / (1024 * 1024), 1);
                                      
                                      if ($disk_diff_bytes < 0) {
                                          $disk_trend = "<span class='text-danger'>減少 " . abs($disk_diff_mb) . " MB (空間縮減)</span>";
                                      } elseif ($disk_diff_bytes > 0) {
                                          $disk_trend = "<span class='text-success'>增加 " . $disk_diff_mb . " MB (空間釋放)</span>";
                                      } else {
                                          $disk_trend = "<span class='text-muted'>無變動</span>";
                                      }
  
                                      echo "<li class='list-group-item py-3'>";
                                      echo "<div class='d-flex justify-content-between align-items-center mb-2'>";
                                      echo "<div><span class='badge bg-secondary me-2'>#$count</span><strong>回報時間: " . htmlspecialchars($time, ENT_QUOTES, 'UTF-8') . "</strong></div>";
                                      echo "<span class='badge bg-light text-dark border'>IP: " . htmlspecialchars($ip, ENT_QUOTES, 'UTF-8') . "</span>";
                                      echo "</div>";
  
                                      echo "<div class='row small text-secondary mt-2'>";
                                      echo "<div class='col-sm-6'>電腦名稱: <strong>" . htmlspecialchars($comp_name, ENT_QUOTES, 'UTF-8') . "</strong></div>";
                                      echo "<div class='col-sm-6'>登入使用者: <strong>" . htmlspecialchars($username, ENT_QUOTES, 'UTF-8') . "</strong></div>";
                                      echo "<div class='col-sm-6 mt-1'>RustDesk ID (Now): <code class='text-primary fw-bold'>" . htmlspecialchars($rd_now, ENT_QUOTES, 'UTF-8') . "</code></div>";
                                      echo "<div class='col-sm-6 mt-1'>RustDesk ID (Old): <code>" . htmlspecialchars($rd_old, ENT_QUOTES, 'UTF-8') . "</code></div>";
                                      echo "<div class='col-12 mt-2 pt-2 border-top'>C 槽剩餘空間: <strong class='text-dark'>$disk_now_gb GB</strong> (比上次: $disk_trend)</div>";
                                      echo "</div>";
  
                                      echo "</li>";
                                      $count++;
                                  }
                                  echo "</ul>";
                              }
                          }
                      }
                  }
                  ?>
  
                  <hr class='my-4'>
                  <div class='d-flex justify-content-between align-items-center small'>
                      <div class='text-muted'>系統運作正常。</div>
                      <a href="https://example.com" target="_blank" class='text-decoration-none'>官方介紹頁面</a>
                  </div>
  
              </div>
          </div>
      </div>
  </div>
  
  <!-- Log 檢視 Modal 視窗 -->
  <?php if (isset($raw_log_content)): ?>
  <div class="modal fade" id="rawLogModal" tabindex="-1" aria-labelledby="rawLogModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
              <div class="modal-header bg-dark text-white">
                  <h5 class="modal-title fw-bold" id="rawLogModalLabel">Log 內容 (<?php echo htmlspecialchars($query_date, ENT_QUOTES, 'UTF-8'); ?>)</h5>
                  <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="Close"></button>
              </div>
              <div class="modal-body bg-light">
                  <!-- 保持不換行，超出部分透過水平卷軸檢視 -->
                  <pre class="m-0 p-3 bg-white border rounded text-dark small" style="white-space: pre; overflow-x: auto; max-height: 500px;"><?php echo htmlspecialchars($raw_log_content, ENT_QUOTES, 'UTF-8'); ?></pre>
              </div>
              <div class="modal-footer">
                  <button type="button" class="btn btn-secondary btn-sm" data-bs-dismiss="modal">關閉</button>
              </div>
          </div>
      </div>
  </div>
  <?php endif; ?>
  
  <!-- 說明 Modal 視窗 -->
  <div class="modal fade" id="infoModal" tabindex="-1" aria-labelledby="infoModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
              <div class="modal-header">
                  <h5 class="modal-title fw-bold" id="infoModalLabel">登入使用者名稱判讀說明</h5>
                  <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
              </div>
              <div class="modal-body small text-secondary">
                  <p>在紀錄中看到的<strong>登入使用者</strong>名稱，會因執行方式不同而有所差異：</p>
                  <ul class="mb-3">
                      <li class="mb-2"><strong>一般名稱（例如 <code>S006</code>）：</strong><br>代表這是你手動點兩下執行批次檔時，以當前桌面互動登入的使用者身分執行的紀錄。</li>
                      <li><strong>結尾帶錢字號（例如 <code>R2MS_LITE_S006$</code>）：</strong><br>代表這是透過 Windows <strong>工作排程器</strong>在背景自動觸發執行的紀錄。此時系統沒有對應到桌面的真人互動登入，因此會抓到該主機本身的系統機器帳戶身分。</li>
                  </ul>
                  <p class="mb-0 text-muted">兩者皆能正常回報裝置狀態與硬碟空間，純粹代表「手動執行」與「背景自動排程」的系統身分差異。</p>
              </div>
              <div class="modal-footer">
                  <button type="button" class="btn btn-secondary btn-sm" data-bs-dismiss="modal">關閉</button>
              </div>
          </div>
      </div>
  </div>
  
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  </body>
  </html>
  ```
  + 建立「devices.json」
  ```
  [
    {
      "id": "MyPC_01",
      "description": "測試",
      "owner": "NCU",
      "asset_number": "不詳",
      "web_auth_code": "1234"
    },
    {
      "id": "R2MS_Lite_S006",
      "description": "202609購買，保固一年。",
      "owner": "工研院",
      "asset_number": "不詳",
      "web_auth_code": "4500"
    }
  ]
  ```
  + 建立「write_R2MS_Lite_Info.php」
  ```
  <?php
  //**************************************************************************
  //   Name: write_R2MS_Lite_Info.php
  //   Copyright: 
  //   Author: HsiupoYeh 
  //   Version: v20260924c
  //   Description: 接收遠端裝置透過 GET 傳遞的 R2MS_Lite_Info 資料。
  //                1. 自動檢查並取得來源端 IP 與伺服器當前時間。
  //                2. 解析並驗證是否為合法 JSON，且 custom_device_id 必須有值。
  //                3. 依 custom_device_id 與「年/月」自動建立層級資料夾。
  //                4. 採用每日日誌歸檔機制，將資料安全附加寫入對應文字檔中。
  //                5. 放置位置: /web/R2MS_Lite_Info_Server
  //                6. 測試呼叫: https://cgrg.synology.me/R2MS_Lite_Info_Server/write_R2MS_Lite_Info.php?R2MS_Lite_Info={"computer_name":"R2MS_LITE_S006","user_name":"S006","custom_device_id":"R2MS_Lite_S006","rustdesk_id_now":"R2MS_Lite_S006","rustdesk_id_old":"R2MS_Lite_S006","disk_c_free_now":"468282679296","disk_c_free_old":"468283994112"}
  //**************************************************************************
  
  // 1. 取得網址列傳遞的 R2MS_Lite_Info 參數
  $R2MS_Lite_Info = isset($_GET['R2MS_Lite_Info']) ? $_GET['R2MS_Lite_Info'] : '';
  
  // 2. 基礎檢查與安全過濾
  if (empty($R2MS_Lite_Info)) 
  {
      http_response_code(400);
      echo "ERROR: R2MS_Lite_Info is empty";
      exit();
  } 
  else 
  {
      // [防禦一] 限制字串長度（避免惡意灌入超長垃圾資料）
      if (strlen($R2MS_Lite_Info) > 2000) {
          http_response_code(400);
          echo "ERROR: Data too long";
          exit();
      }
  
      // [防禦二] 防止 CRLF 換行注入 (Log Injection)
      $R2MS_Lite_Info = str_replace(array("\r", "\n"), "", $R2MS_Lite_Info);
  
      // [防禦三] 解析並確認傳入內容確實為合法 JSON
      $data_array = json_decode($R2MS_Lite_Info, true);
      if (json_last_error() !== JSON_ERROR_NONE || !is_array($data_array)) {
          http_response_code(400);
          echo "ERROR: Invalid JSON format";
          exit();
      }
  
      // [防禦四] 檢查 custom_device_id 是否存在且不為空
      if (!isset($data_array['custom_device_id']) || trim($data_array['custom_device_id']) === '') {
          http_response_code(400);
          echo "ERROR: Missing or empty custom_device_id";
          exit();
      }
  
      $custom_device_id = trim($data_array['custom_device_id']);
      
      // [防禦五] 安全過濾 device_id，僅允許英代數字、底線與連字號，防止路徑走訪 (Directory Traversal)
      $custom_device_id = preg_replace('/[^a-zA-Z0-9_\-]/', '_', $custom_device_id);
  
      // 3. 取得目前伺服器時間與拆解年月日時
      $today = date("Y-m-d H:i:s"); 
      $temp_year_str = substr($today, 0, 4);    // 例如：2026
      $temp_month_str = substr($today, 5, 2);   // 例如：09
      $temp_day_str = substr($today, 8, 2);     // 例如：24
      
      // 4. 即時輸出回應畫面
      echo '['.$_SERVER["REMOTE_ADDR"].']'.$today.': '.htmlspecialchars($R2MS_Lite_Info, ENT_NOQUOTES);
  
      // 5. 設定並建立依「自訂裝置名稱 / 年 / 月」分類的儲存資料夾路徑
      // 實際儲存結構例如：./R2MS_Lite_Info_log/R2MS_Lite_S006/2026/09/
      $output_path = './R2MS_Lite_Info_log/'.$custom_device_id.'/'.$temp_year_str.'/'.$temp_month_str.'/';
      if (!file_exists($output_path)) 
      {
          mkdir($output_path, 0777, true);
      }
      
      // 6. 定義每日的日誌檔檔名
      $file = $output_path.$temp_year_str.$temp_month_str.$temp_day_str.'_R2MS_Lite_Info_log.txt';
      
      // 7. 將過濾後的安全資料附加寫入檔案中
      file_put_contents($file, '['.$_SERVER["REMOTE_ADDR"].']'.$today.'(UTC+8): '.$R2MS_Lite_Info."\n", FILE_APPEND | LOCK_EX);
  }
  ?>
  ```


### 同步 RustDesk_ID 
+ Sync_RustDesk_ID_v20260923a.bat  !!!請注意填寫正確FTP連線資訊!!!以及自訂的名稱!!!
```






















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
+ Sync_RustDesk_ID_v20260923a.bat  !!!請注意填寫正確FTP連線資訊!!!以及自訂的名稱!!!
```
::**************************************************************************
::   Name: Sync_RustDesk_ID_v20260923a.bat
::   Copyright:  
::   Author: HsiupoYeh 
::   Version: v20260923a
::   Description: 自動擷取本機 RustDesk ID 並比對異動，僅在 ID 變更或首次執行時
::                將時間戳記檔名上傳至 Synology NAS FTP 伺服器。
::**************************************************************************

@echo off
setlocal enabledelayedexpansion

:: ==========================================
:: 【自定義設定區】
:: 請在此處手動指定這台電腦的專屬資料夾名稱（建議對應機殼貼紙或資產編號）
:: 檔案將會上傳至 NAS 的： /home/[CUSTOM_ID]/ RustDesk_[年月日]_[時分秒].txt
:: ==========================================
set "CUSTOM_ID=R2MS_Lite_S006"

:: ==========================================
:: 設定檔與路徑定義
:: ==========================================
set "RUSTDESK_EXE=C:\Program Files\RustDesk\rustdesk.exe"
set "WORK_DIR=C:\Sync_RustDesk_ID"

set "NOW_FILE=%WORK_DIR%\RustDeskID_now.txt"
set "OLD_FILE=%WORK_DIR%\RustDeskID_old.txt"

set "FTP_USER=R2MS_Lite_Info_Server"
set "FTP_PASS=45002931"
set "FTP_HOST=ftp://cgrg.synology.me:10021/home/%CUSTOM_ID%/"

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

:: 6. 產生時間戳記檔名
set "YYYY=%date:~0,4%"
set "MM=%date:~5,2%"
set "DD=%date:~8,2%"
set "HH=%time:~0,2%"
if "%HH:~0,1%"==" " set "HH=0%HH:~1,1%"
set "MIN=%time:~3,2%"
set "SS=%time:~6,2%"

set "REMOTE_FILENAME=RustDesk_%YYYY%%MM%%DD%_%HH%%MIN%%SS%.txt"

:: 7. 透過 curl 上傳標準輸入，並指定完整的遠端目標 URL 包含檔名
type "%NOW_FILE%" | curl.exe -sS --ftp-create-dirs --connect-timeout 2 -m 4 --no-keepalive -u %FTP_USER%:%FTP_PASS% -T - "%FTP_HOST%%REMOTE_FILENAME%"

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
