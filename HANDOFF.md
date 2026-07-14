# Handoff｜專業美股選股器 v2

最後更新：2026-07-14（Asia/Hong_Kong）

## 目前狀態

- 更新版位於 `C:\Users\User\Documents\Own\gkhaw_stock_app_v2`。
- 可部署壓縮檔位於 `C:\Users\User\Documents\Own\gkhaw_stock_app_v2_deploy.zip`。
- 原始程式沒有被修改。
- 37 個原始函式及全部原始文字常量均已保留。
- 同業批次取得已改為最多四線受控並行，回傳順序保持不變。
- Python 3.12.11 的鎖定依賴已實際安裝及驗證。
- Streamlit 健康檢查回傳 HTTP 200。
- 實際 Yahoo Finance 首頁測試完成，Streamlit 例外數為 0。
- 完整 80 行業長時間掃描尚未逐一執行；相關計算函式本體沒有改動。

## 2026-07-14 修正

- 使用者在完整行業排名載入期間遇到 Streamlit `layout block created outside the function` 錯誤。
- 根因是 `get_industry_average_scores()` 同時接受外部建立的進度條物件及套用 `st.cache_data`；快取重播時原 layout block 已失效。
- 已移除該函式的 `st.cache_data` decorator，恢復安全的即時進度更新。
- 股票、歷史行情及每個行業批次資料的一小時底層快取仍保留，因此不影響既有功能和資料結果。
- `README.md`、`HANDOFF.md` 及部署 ZIP 已同步更新。

## 2026-07-14 手機及排名效能更新

### 手機版

- `st.set_page_config()` 的側欄狀態由 `expanded` 改成 `auto`。
- 新增 768px 手機 breakpoint。
- 手機版所有 `st.columns()` 對應的 horizontal block 改為垂直排列，每欄寬度 100%。
- 手機側欄寬度最多 300px；實測收合狀態為 `x=-300, width=300`，不再覆蓋主內容。
- 390×844 實測：主畫面 390px、內容約 358px、側欄可正常展開及收合。
- Logo、標題、總分、表格、圖片及 caption 均有手機專用尺寸或 overflow 規則。

### 行業排名

- `get_industry_average_scores()` 已由逐行業串行改為全域唯一股票並行。
- 完整 80 行業共 800 個股票位置，去重後為 752 支唯一股票。
- 新增 `GKHAW_RANKING_WORKERS`，預設 8、程式上限 12。
- 所有股票仍呼叫原有 `fetch_robust_data()`，評分公式及欄位沒有改動。
- 完成股票資料後，再按原行業成員聚合平均分及樣本數。
- 確定性回歸測試：80 個行業結果與舊演算法完全一致。
- 模擬效能：舊版 1.151 秒、新版 0.436 秒，約 2.64 倍；實際速度受 Yahoo Finance 影響。
- 進度條前 90% 顯示已完成股票數，後 10% 顯示行業聚合進度，最後值為 100%。

### 驗證

- Streamlit 實際首頁測試：0 個例外，8 個按鈕、1 個資料表。
- 37 個函式全部保留。
- RSI、MACD、DMI、交易評分、交易計畫及所有 PDF 報告函式本體保持不變。
- 只改動 `fetch_batch_data()` 與 `get_industry_average_scores()` 的效能實作。

## 上傳規則

使用者不需要上傳 `.venv`。該資料夾只供本機測試，體積大且不適合部署。

使用者需要上傳 `.streamlit`，包括 `.streamlit/config.toml`。

建議流程：

1. 解壓 `gkhaw_stock_app_v2_deploy.zip`。
2. 把解壓後的全部內容放進同一個私人 GitHub repository 根目錄。
3. 不要上傳 `Site.txt`、`.venv`、`.env` 或任何帳密資料。
4. Streamlit Community Cloud 的入口檔選擇 `app.py`。

## 主要文件

- `app.py`：更新版完整程式。
- `requirements.txt`：鎖定依賴。
- `.python-version`：Python 3.12.11。
- `.streamlit/config.toml`：Streamlit 正式設定。
- `render.yaml`：Render Blueprint。
- `Dockerfile`：容器部署。
- `FEATURE_PARITY.md`：功能保留核對表。
- `README.md`：使用及部署說明。

## 下一步建議

1. 建立私人 GitHub repository。
2. 上傳部署 ZIP 解壓後的內容。
3. 優先在 Streamlit Community Cloud 進行第一次部署。
4. 依序測試：個股、同業、行業排名、個股 PDF、每日／每週／每月 PDF。
5. 部署成功後再設定 `app.gkhaw.com`，避免先修改正式 DNS。

## 持續維護要求

使用者要求：此任務之後每次回覆都要同步更新 `README.md` 及 `HANDOFF.md`，記錄最新決定、狀態、驗證結果及下一步。

## 2026-07-14｜原始版桌面排版實測

- 原始來源：`C:\Users\User\Downloads\app (1).py`；SHA-256 為 `45AC905ACE3A79644537C840E53BCD79533583D709897BFFDAE4BBBE7CC5E87D`，與最初保存的原始檔一致。
- 原始版與更新版分別以 localhost 啟動，並在同一個瀏覽器分頁、同一 viewport 下先後載入，避免不同分頁尺寸造成誤判。
- 1440×900 實測完全一致：側欄 `380px`、主區 `1060px`、內容 padding `96px 80px 160px`；Logo、標題、分數卡、兩欄／三欄區塊及按鈕幾何皆相同。
- 1280×720 第二次實測也完全一致：兩版內容容器均為 `x=380、width=890`，關鍵 computed style 的 JSON 比對結果相同。
- 結論：本輪不修改 `app.py`，因為桌面排版沒有偏差；再改會反而偏離完成品。更新版僅於 `max-width: 768px` 啟用手機響應式版面，屬先前需求的必要修正。
- 若部署畫面仍看似不同，優先確認瀏覽器縮放為 100%、視窗 CSS 寬度相同、側欄狀態一致，並強制重新整理清除舊快取。
- Plotly 僅為啟動原始版而安裝到本機 `.venv`；原程式未實際使用，部署 `requirements.txt` 不增加此依賴。
