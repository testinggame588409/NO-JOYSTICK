# NO-JOYSTICK 遊戲 Railway 部署指南

這份指南將協助你將 NO-JOYSTICK 遊戲部署到 Railway 平台上，讓你的同事可以進行測試。

## 為什麼選擇 Railway？

Railway 是一個非常適合 Node.js 應用的部署平台，它支援自動構建和部署，並且提供免費的額度供測試使用。你的項目採用了 Node.js + Express + SQLite 的架構，這種單體架構非常適合在 Railway 上快速部署。

## 部署步驟

### 1. 準備 GitHub 倉庫

Railway 最推薦的部署方式是連接 GitHub 倉庫。

1. 在 GitHub 上創建一個新的私有倉庫（Private Repository）。
2. 將你解壓後的項目文件夾（包含 `server`、`js`、`images` 等目錄）上傳到這個 GitHub 倉庫。
3. 確保項目根目錄下有我們為你準備的 `railway.json` 文件。這個文件告訴 Railway 如何構建和啟動你的應用。

### 2. 在 Railway 上創建項目

1. 登入 [Railway](https://railway.app/)。
2. 點擊右上角的 **New Project**。
3. 選擇 **Deploy from GitHub repo**。
4. 選擇你剛剛創建的 GitHub 倉庫。
5. 點擊 **Deploy Now**。

### 3. 配置持久化存儲 (Volume)

由於你的項目使用了 SQLite 數據庫（`game.db`），如果沒有配置持久化存儲，每次 Railway 重新部署時，數據庫都會被重置，測試數據會丟失。

1. 在 Railway 項目面板中，點擊你的服務（Service）。
2. 進入 **Settings** 標籤頁。
3. 找到 **Volumes** 區塊，點擊 **Add Volume**。
4. 設置 Volume 的掛載路徑（Mount Path）為 `/app/server/data`。
5. 進入 **Variables** 標籤頁，添加一個環境變量：
   - **Variable Name**: `DB_PATH`
   - **Value**: `/app/server/data/game.db`

這樣設置後，SQLite 數據庫文件將保存在持久化存儲中，即使重新部署也不會丟失數據。

### 4. 配置公共域名

1. 在 Railway 項目面板中，點擊你的服務。
2. 進入 **Settings** 標籤頁。
3. 找到 **Networking** 區塊。
4. 點擊 **Generate Domain**，Railway 會為你生成一個免費的公共域名（例如 `your-app.up.railway.app`）。
5. 你的同事現在可以通過這個域名訪問並測試遊戲了！

## 注意事項

- **環境變量**：你的 `server.js` 已經支援通過 `process.env.PORT` 獲取端口，Railway 會自動注入 `PORT` 環境變量，所以不需要額外配置端口。
- **數據庫初始化**：`server.js` 中的 `initDatabase` 函數會在啟動時自動創建數據庫表，所以你不需要手動執行 SQL 腳本。
- **前端 API 請求**：你的前端代碼（`js/api.js`）使用了 `window.location.origin` 來構建 API 請求路徑，這意味著前端會自動向當前域名發送請求，無需修改任何前端代碼即可適應 Railway 的域名。

祝測試順利！如果有任何問題，隨時可以調整代碼並推送到 GitHub，Railway 會自動為你重新部署。
