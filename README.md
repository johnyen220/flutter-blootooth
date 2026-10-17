# flutter_bluetooth

一個用於示範和測試 Bluetooth（BLE）功能的 Flutter 範例專案。

## 專案說明

本專案包含簡單的 BLE 掃描與連線控制器（例如 `ble_scanner.dart`、`ble_controller.dart`），可以作為整合藍牙功能到你的 Flutter 應用的起點。

## 先決條件

- Flutter SDK（建議使用最新穩定版）
- 支援的作業系統：Android、iOS、macOS 等（依專案需求）
- 開發機上已安裝 Android SDK / Xcode（若要在模擬器或實機上測試）

## 快速啟動

1. 取得專案：

   git clone <your-repo-url>

2. 進入專案並安裝套件：

   flutter pub get

3. 在裝置或模擬器上執行：

   flutter run

## 平台權限（必要設定）

要使用藍牙功能，請務必在各平台加入必要權限：

- Android（`android/app/src/main/AndroidManifest.xml`）：
  - `BLUETOOTH`、`BLUETOOTH_ADMIN`（舊版）、`BLUETOOTH_SCAN`、`BLUETOOTH_CONNECT`（Android 12+）
  - 若需要掃描附近裝置的位置信息，可能還要 `ACCESS_FINE_LOCATION` / `ACCESS_COARSE_LOCATION`（取決於目標 SDK 與使用的 plugin）
  - 範例權限宣告（請根據目標 SDK 與 plugin 文件調整）：

    ```xml
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
    <!-- Android 12+ -->
    <uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    ```

- iOS（`ios/Runner/Info.plist`）：
  - 加入描述字串，說明為何需要藍牙或附近裝置權限，例如：

    <key>NSBluetoothAlwaysUsageDescription</key>
    <string>應用需要使用藍牙來掃描與連接裝置。</string>
    <key>NSBluetoothPeripheralUsageDescription</key>
    <string>應用需要使用藍牙來掃描與連接裝置。</string>
    <!-- 如果需要定位權限，也一併加入說明 -->
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>應用需要存取位置以搜尋附近的藍牙裝置。</string>

- macOS：視情況在 `Info.plist` 中加入相對應的使用描述。

請依你使用的 BLE plugin（例如 flutter_blue_plus、flutter_reactive_ble 等）文件，確認與補齊權限與權限請求流程。

## 主要檔案（範例）

- `lib/main.dart` - 應用進入點與 UI。
- `lib/ble_scanner.dart` - 負責掃描附近 BLE 裝置的邏輯。
- `lib/ble_controller.dart` - 負責與裝置建立連線、讀寫特徵值等操作。

## 常見問題 & 排錯

- 無法掃描到裝置：
  - 確認藍牙已開啟，並且裝置在可被掃描的狀態。
  - 在 Android 上檢查是否已授予定位權限（取決於 plugin）。
  - 在 iOS 上，確認 Info.plist 已加入描述並且使用者已允許權限。

- 連線不穩定或頻繁斷線：
  - 檢查目標裝置的藍牙實作、RSSI 與距離。
  - 請求合適的 MTU 或更改訂閱/讀取策略（視 plugin 支援）。

## 測試

- 專案內已包含基本的 widget 測試 `test/widget_test.dart`。
- 可以根據需要新增單元測試或整合測試來覆蓋 BLE 邏輯（注意真實 BLE 操作在 CI 上通常需 mock）。

## 如何貢獻

歡迎提出 Issue 或 Pull Request。請在 PR 中描述變更目的與測試方式。

## 版權與授權

此專案的授權資訊請放在 LICENSE 檔案中（若沒有，請補上合適授權）。

---

如果你想要我針對 README 裡某段進一步本地化（例如用中文/英文混合、加入專案範例截圖、或展示範例程式碼片段），告訴我你想要的語言與內容，我會繼續編輯與優化。
