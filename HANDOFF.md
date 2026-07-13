# Handoff｜專業美股選股器 v2

最後更新：2026-07-13（Asia/Hong_Kong）

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
