# 部署說明（GitHub Pages ＋ 個人 Google 雲端硬碟）

## 一、部署到 GitHub Pages

### 個人帳號
1. 建立 repository，例如 `b14-4-diary`，把本資料夾全部檔案上傳到**根目錄**，`.nojekyll` 也要一起上傳。
2. 進入 Settings → Pages → Build and deployment，Source 選「Deploy from a branch」，Branch 選 `main`，資料夾選 `/ (root)`。
3. 部署完成後，網址為 `https://<帳號>.github.io/b14-4-diary/`。

### 公司組織帳號
- 步驟同上，網址為 `https://<組織名>.github.io/<repo>/`。也可以在 Settings → Pages → Custom domain 設定公司網域，例如 `diary.example.com.tw`。
- **注意：免費方案只能從 public repository 發布 Pages**。要從 private repository 發布，需要 GitHub Pro、Team 或 Enterprise。不論 repository 是否公開，發布出去的網站本身都是**公開可瀏覽**的；要限制瀏覽對象，需要 GitHub Enterprise Cloud 的私人 Pages 功能。
- 這不影響資料安全：網站只有程式，沒有任何日誌資料。每位使用者的日誌只存在自己的手機和自己的 Google 雲端硬碟。

## 二、建立 Google OAuth 用戶端 ID

一個部署網址設一次。公司部署由管理者設定，個人部署由自己設定。

1. 到 Google Cloud Console 建立專案，啟用 **Google Drive API**。
2. 設定 OAuth 同意畫面：
   - **個人 Gmail**：使用者類型選「外部」。發布狀態維持「測試」時，必須把使用者的 Gmail 加進「測試使用者」。
   - **公司 Google Workspace**：使用者類型選「內部」，組織內所有帳號都可以使用，不必一一加入測試使用者。
   - 範圍只加 `.../auth/drive.file`。這是 Google 列為「建議／非敏感」的範圍，只需要基本的應用程式驗證。
3. 建立憑證：OAuth 用戶端 ID，類型選「**網頁應用程式**」。
4. **已授權的 JavaScript 來源**只填網域，**不要加路徑**，例如：
   - `https://<帳號或組織>.github.io`
   - 或自訂網域 `https://diary.example.com.tw`
   - 本機測試可加 `http://localhost:8080`
5. 「已授權的重新導向 URI」不用填，本系統用的是 token 模式（彈出授權視窗）。
6. **絕對不要**把 Client Secret 放在前端或 repository 裡。本系統不需要 Client Secret。

## 三、填入用戶端 ID

| 方式 | 做法 | 適用 |
|---|---|---|
| A. 公司預設（建議） | 編輯 `index.html`，把 `const PRESET_CLIENT_ID = '';` 填入用戶端 ID 後再推送。使用者完全不用設定 | 公司統一部署 |
| B. 使用者自行輸入 | 第一次按「同步」時，依提示貼上用戶端 ID。ID 只存在該手機的 localStorage，之後可以在「設定、備份與刪除」修改 | 個人部署、測試 |

## 四、手機安裝與驗收

1. 用手機瀏覽器開 HTTPS 網址。Android 用 Chrome 選「安裝應用程式」；iOS 用 Safari 選「分享 → 加入主畫面」。
2. 新增一筆日誌，加入一張照片，按「儲存」，再按「同步」，完成 Google 授權。
3. 到 Google 雲端硬碟確認有 `個人施工日誌／工程名稱／年／月` 資料夾，裡面有 JSON 和照片。
4. 開飛航模式，重新開啟 App，確認可以離線開啟、填報和儲存。恢復網路後再按同步。
5. 在第二台裝置按「讀取雲端日誌」並還原，確認附件也能下載。
6. 兩台裝置分別修改同一筆並同步，確認第二台會顯示「衝突」，不會自動覆寫。
7. 按「預覽／列印」並另存 PDF，對照 110 年版 B14-4 紙本版面。

## 五、發布新版本

1. 修改 `index.html` 等檔案。
2. **把 `sw.js` 裡的 `VERSION` 加一號**，例如 `v2.2.2`，舊快取才會被替換。
3. 推送到 GitHub。使用者下次開啟時會看到「有新版本可用」，按「立即更新」即可。
