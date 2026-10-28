# Rutedesk 專案進度與接手說明

更新日期：2026-10-28（Asia/Taipei）

## 目前狀態

本專案以 RustDesk 原始碼為基礎，目前已將主要視窗與平台產品資訊的**顯示名稱改為 `Rutedesk`**。改名已提交並推送到 GitHub，但尚未完成編譯、執行或安裝驗證，也尚未產出經驗證的安裝包。

這是顯示名稱調整，並非完整品牌替換。接手時不要假設所有 `RustDesk` 字樣或內部名稱都已改名。

| 項目 | 內容 |
| --- | --- |
| 專案倉庫（origin） | https://github.com/waysolve-tw/rutedesk.git |
| 開發分支 | `master` |
| 上游原始碼 | https://github.com/rustdesk/rustdesk.git |
| 改名提交 | `89b282aee06c89e8c639cb44b41a81e659377561` |
| 提交訊息 | `Rename application display name to Rutedesk` |
| 改名前基底 | `350966b33`，`Add trusted-device V2 with legacy upgrade compatibility (#16548)` |
| 原電腦專案路徑 | `C:\Users\user\Documents\rutedesk` |

本文件記錄上述改名提交的狀態；後續提交可能改變專案內容，請以新電腦的 Git 紀錄與實際檔案為準。

## 已完成的實作與影響範圍

改名提交修改 16 個檔案，共新增 33 行、刪除 21 行。各檔案均為顯示名稱調整所需，沒有更動遠端連線、編解碼或檔案傳輸邏輯。

下列路徑相對於專案根目錄：

| 檔案 | 變更及必要原因 |
| --- | --- |
| `src/common.rs` | 新增 `get_app_display_name()`；將預設 `RustDesk` 映射為 `Rutedesk`，保留其他自訂名稱，避免修改內部名稱來源。 |
| `src/flutter.rs` | Windows 啟動器取得顯示名稱，讓初始視窗標題與 Flutter 標題一致；此名稱也參與啟動器的既有視窗查找，需實測重複啟動。 |
| `src/ui.rs` | 舊版 Sciter 主視窗標題使用顯示名稱；只改這個標題呼叫，未重構舊版流程。 |
| `flutter/lib/common.dart` | 新增 `appDisplayName`，讓各類桌面視窗標題使用顯示名稱；原 `appName` 仍保留內部用途。 |
| `flutter/lib/main.dart` | Flutter 應用標題及 Web Client 標題使用顯示名稱。 |
| `flutter/lib/desktop/pages/desktop_home_page.dart` | 新版本提示中的應用名稱改用顯示名稱。 |
| `flutter/lib/mobile/pages/home_page.dart` | 手機與 Web 首頁標題改用顯示名稱。 |
| `Cargo.toml` | Windows Rust 建置的產品名稱與檔案描述改名。 |
| `flutter/windows/runner/Runner.rc` | Windows Flutter 執行檔的產品名稱與檔案描述改名。 |
| `flutter/windows/runner/main.cpp` | Windows 啟動器的預設顯示名稱改名。 |
| `flutter/android/app/src/main/res/values/strings.xml` | Android `app_name` 改名。 |
| `flutter/ios/Runner/Info.plist` | iOS `CFBundleDisplayName` 與 `CFBundleName` 改名。 |
| `flutter/macos/Runner/Info.plist` | macOS `CFBundleName` 改名；未更改建置用 `PRODUCT_NAME` 或 `.app` 檔名。 |
| `flutter/linux/my_application.cc` | Linux 初始視窗及標題列名稱改名。 |
| `res/rustdesk.desktop` | Linux 應用程式選單名稱改名。 |
| `res/rustdesk-link.desktop` | Linux URI 處理程式的顯示名稱改名。 |

## 刻意保留的內容與已知限制

- 執行檔名稱仍為 `rustdesk.exe`／既有平台名稱，未改為 `rutedesk.exe`。
- 內部應用名稱、設定目錄、系統服務名稱、套件識別與 `rustdesk://` 協定保留原值，以沿用既有設定與系統整合。
- `hbb_common` 子模組沒有修改或升版；沒有執行 Flutter Rust Bridge codegen。
- 原始圖示、Logo、版權聲明、上游網址與翻譯內容尚未全面更換；部分對話框、托盤、安裝介面及說明文字仍可能顯示 RustDesk。
- 更新來源與更新機制未改成 Rutedesk 專屬發佈流程；正式發佈前需另行確認。
- 改名映射保留既有自訂客戶端名稱，不會把所有自訂名稱強制改成 Rutedesk。
- Web 執行核心位於未納入此 Git 倉庫的 `flutter/web/js/`；只 clone 此倉庫並不代表能完整建置 Web 版。

## 已完成與尚未完成的驗證

已完成：

- 檢閱全部改名差異，確認沒有混入其他本機文件。
- `git diff --check` 與提交前的暫存區差異檢查通過。
- Android strings XML、iOS plist、macOS plist 的 XML 語法解析通過。
- 確認受影響的 Dart 頁面已匯入 `common.dart`。
- 推送後以 `git ls-remote` 確認遠端 `master` 當時等於改名提交。

尚未完成：

- Rust／Flutter 編譯、靜態分析及自動化測試。
- 各平台實機執行、安裝、升級、解除安裝及重複啟動測試。
- GitHub Actions 結果確認；成功推送不代表 CI 已通過。

原因：原電腦的執行環境找不到 `cargo`、`rustc`、`flutter`、`dart` 命令。因此目前只能宣稱靜態檔案檢查通過，不能宣稱程式已成功建置或執行。

## 在另一部電腦取得專案

先安裝 Git，在要存放專案的父資料夾開啟 PowerShell，執行：

```powershell
git clone --recurse-submodules https://github.com/waysolve-tw/rutedesk.git
cd rutedesk
git status --short
git log -5 --oneline
git submodule status --recursive
```

若先前已 clone，但未下載子模組，在該專案內執行：

```powershell
git submodule update --init --recursive
```

`libs/hbb_common` 是 Git 子模組，不能只下載主倉庫的 ZIP 就視為依賴齊全。Git 忽略的本機檔案、產生檔與編譯工具也不會因 clone 而移轉。

新電腦的專案路徑可自行選擇，不必與原電腦一致。clone 通常只建立 `origin`；本文件所列的上游網址不代表新電腦已自動設定 `upstream`。

原電腦另有未追蹤的 `OPEN_SOURCE_ASSESSMENT.zh-TW.md`，不屬於改名提交，也未隨此次交接文件上傳；從 GitHub clone 不會取得它。

## 建議接續工作

1. 先閱讀根目錄 `AGENTS.md`，再閱讀本文件與改名提交差異。
2. 依目標平台檢查 `README.md`、`docs/README-ZH.md`、`flutter/README.md` 與 `.github/workflows/flutter-build.yml`，準備 Rust、Flutter、平台編譯工具與原生依賴。以倉庫指定版本為準，不要直接假設最新版本相容。
3. Windows 接手時優先完成桌面版建置，記錄實際工具版本、命令、產物位置與錯誤。處理缺失的橋接產生檔時遵守 `AGENTS.md`：不要自行執行 `flutter_rust_bridge_codegen`；專案 CI 使用指定版本產生原生橋接檔。
4. 建置成功後，確認主視窗、連線視窗與 Windows 檔案內容中的產品名稱；再測試重複啟動、讀取既有設定與基本遠端連線。
5. 將測試結果更新到本文件。若要進一步更換執行檔、服務、安裝包、所有品牌文字或更新來源，先明確定義範圍，獨立於這次顯示名稱改名處理。

目前最適合的下一步是「建立可重現的 Windows 建置並驗證改名」，不是再次全域取代 `RustDesk`。

## 可貼給新電腦上開發助理的接手訊息

> 請先閱讀專案根目錄的 AGENTS.md 與 PROJECT_HANDOFF.zh-TW.md，並用繁體中文與我互動。此專案已在提交 89b282aee 中把主要顯示名稱改成 Rutedesk，但尚未完成編譯與實機驗證。請先確認目前分支、工作目錄變更、子模組與建置環境，再協助完成 Windows 桌面版建置及改名驗證。保留既有設定目錄、服務名稱、內部識別與版權資訊，不要全域取代 RustDesk，不要自行執行 flutter_rust_bridge_codegen，也不要把未驗證項目宣稱為已完成。
