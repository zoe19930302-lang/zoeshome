# 家具家電採購管理網站（免費部署）

此專案以 GitHub Pages（免費靜態網站）+ Supabase（雲端資料庫與登入）組成。

## 功能
- 家具家電分類、名稱、品牌／店家、型號、尺寸、房間／位置、照片網址
- 購買日期、購買地點、金額、數量、運送／安裝費
- 未購買／已購買未送達／已購買待配送／已送達／已取消
- 預計及實際送達日期、保固到期日、訂單／發票資訊
- 現金、信用卡、轉帳等付款方式，訂金、其他已付款、尾款自動估算
- 預算統計、搜尋篩選、Excel（.xlsx）匯出
- 登入後共享雲端資料

## 重要的安全說明
此版採用簡化的「同一家庭共用」權限模型：任何能成功登入該 Supabase 專案的使用者，都能查看、新增、修改、刪除同一份清單。請只讓家人取得帳號，且不要公開提供註冊入口。若要嚴格邀請制，建議在 Supabase Authentication 關閉公開註冊，由管理者在 Dashboard 建立使用者，或另加邀請流程。
`anon` / publishable key 可放在前端，但絕對不可放 `service_role` / secret key。RLS SQL 已限制匿名訪客不能讀寫；已登入帳號可操作資料。這不是多家庭隔離架構。

## 部署步驟

### A. 建立 Supabase 專案
1. 前往 https://supabase.com/ 建立免費專案並記下資料庫密碼。
2. 專案建立後，開啟 **SQL Editor**，新增查詢，將本資料夾的 `schema.sql` 全文貼上執行。
3. 開啟 **Project Settings → API**（部分介面可能叫 API Keys），複製 Project URL 及 anon/public 或 publishable key。
4. 開啟 **Authentication → URL Configuration**，將 Site URL 設成之後 GitHub Pages 的網址（先部署後回來設定），Redirect URLs 加入：
   `https://你的GitHub帳號.github.io/你的Repository名稱/`
5. 開啟 **Authentication → Providers → Email**，確認 Email provider 啟用。若保留 Email confirmation，註冊後要先收信驗證。
6. 建議在 Authentication 設定中關閉公開註冊，改由管理者在 Dashboard → Authentication → Users → Add user 建立家庭成員帳號。不同介面版本的選項名稱可能略有差異。不要把管理 API 金鑰放在前端。

### B. 設定網站金鑰
1. 用文字編輯器開啟 `index.html`。
2. 搜尋：
   `const SUPABASE_URL = "YOUR_SUPABASE_URL";`
   `const SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_OR_PUBLISHABLE_KEY";`
3. 替換成 Supabase 專案的 Project URL 與 anon/public 或 publishable key。請勿使用 service_role 或 secret key。
4. 儲存檔案。

### C. 發佈到 GitHub Pages
1. 前往 https://github.com/ 建立一個新 Repository，例如 `home-inventory`。
2. 將 `index.html`、`schema.sql`、`README.md` 上傳到 Repository 根目錄（本 ZIP 也包含這些檔案）。
3. Repository → **Settings → Pages**。
4. 在 **Build and deployment** 選 `Deploy from a branch`；Branch 選 `main`、資料夾選 `/(root)`，按 Save。
5. 等待部署完成，網址通常為：
   `https://你的GitHub帳號.github.io/home-inventory/`
6. 把這個完整網址填回 Supabase 的 Authentication → URL Configuration 的 Site URL 與 Redirect URLs。

### D. 建立家庭成員帳號與測試
1. 在 Supabase Dashboard 的 Authentication → Users 建立每位家人的帳號，或在網站啟用註冊時建立後完成 Email 驗證。
2. 開啟網站網址，使用帳號登入。
3. 新增一筆測試品項，另一台裝置登入後確認看得到；再測試修改、刪除、匯出 Excel。
4. 將網站網址分享給家人，另行安全地提供各自帳號。不要共用同一組密碼。

## 照片網址
本版只存照片網址，不會把圖片上傳到網站或資料庫。可使用可公開瀏覽的圖片網址；請勿使用需要登入、短期失效或含私人資料的連結。匯出 Excel 會包含照片網址欄位。

## 資料備份
在網站按「匯出 Excel」下載 `.xlsx`。如要完整資料庫備份，可在 Supabase Dashboard 匯出資料表 CSV。GitHub Pages 是靜態網站，不會自行保存使用者的資料；採購紀錄存在 Supabase。

## 常見問題
- **顯示尚未設定資料庫**：確認 `index.html` 兩個設定值已換成正確內容，重新提交 GitHub。
- **登入成功但清單讀取失敗**：確認 `schema.sql` 已完整執行、表名正確、RLS policies 已建立。
- **登入後跳轉錯誤**：檢查 Supabase Authentication URL Configuration 的 Site URL / Redirect URLs 是否含完整 GitHub Pages 網址，包含結尾斜線。
- **家人看不到資料**：確認登入的是同一 Supabase 專案，且帳號已驗證。
- **Excel 無法匯出**：確認網路可載入 CDN；重新整理後再試。
