# 專業美股選股器 v2

這是原完成品的部署與效能整理版。原始介面、計算公式、80 個行業、全部圖表、CSV、個股 PDF，以及每日／每週／每月市場報告均保留。

## 要上傳哪些文件

最簡單的方法是解壓 `gkhaw_stock_app_v2_deploy.zip`，將解壓後的全部內容上傳至同一個私人 GitHub repository。

需要上傳：

- `app.py`
- `requirements.txt`
- `.python-version`
- `.streamlit/config.toml`（需要保留整個 `.streamlit` 資料夾）
- `render.yaml`（使用 Render 時需要）
- `Dockerfile` 及 `.dockerignore`（使用 Docker 時需要）
- `README.md` 及 `FEATURE_PARITY.md`
- `HANDOFF.md`

不需要上傳：

- `.venv`：只供本機測試，體積約數百 MB，而且不能可靠地跨作業系統使用。
- `Site.txt`：包含私人主機及帳戶資料，禁止上傳。
- `__pycache__`、`.env`、測試記錄及其他臨時文件。

`.gitignore` 已設定自動忽略 `.venv`、帳密檔案及 Python 快取。ZIP 亦已排除 `.venv`。

## 更新內容

- 補齊可重現的 Python 及套件版本。
- 移除未使用的依賴，降低 Build 大小。
- 同業股票以受控並行方式抓取，結果順序不變。
- 跨使用者重用一小時行業排名快取。
- 新聞、價格變化及市場報告加入對應時效快取。
- 加入 Streamlit Community Cloud、Render 及 Docker 部署設定。

完整功能核對請參閱 `FEATURE_PARITY.md`。

## 本機執行

需要 Python 3.12：

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
streamlit run app.py
```

預設同時抓取四支股票。如 Yahoo Finance 出現限流，可以在啟動前降低：

```powershell
$env:GKHAW_FETCH_WORKERS = "2"
streamlit run app.py
```

## Streamlit Community Cloud

1. 解壓部署 ZIP，將解壓後的全部內容上傳至私人 GitHub repository；不要上傳外層 ZIP 本身。
2. 不要上傳 `Site.txt` 或任何帳密文件。
3. 在 Community Cloud 選擇 repository、branch 及入口檔 `app.py`。
4. Python 選擇 3.12。
5. 部署後先測試 NVDA，再測試同業、排名及三種市場 PDF。

## Render

repository 根目錄已包含 `render.yaml`：

1. 在 Render 選擇 New > Blueprint。
2. 連接包含此資料夾的 repository。
3. Render 會使用 Python 3.12.11、安裝 `requirements.txt`，並啟動 Streamlit。
4. 健康檢查使用 `/_stcore/health`。

免費 Render 服務閒置後會休眠，下一次連線仍可能出現平台冷啟動；程式快取無法消除平台本身的喚醒時間。

## Docker

```powershell
docker build -t gkhaw-stock-screener .
docker run --rm -p 8501:8501 gkhaw-stock-screener
```

打開 `http://localhost:8501`。

## 自訂網域

建議使用 `app.gkhaw.com`，不要直接覆蓋現有主域名及郵件 DNS。部署平台會提供要加入的 CNAME 或 A record；只修改指定的 `app` 子網域。

## 安全事項

- 原 `Site.txt` 含明文控制台、FTP、資料庫及郵件密碼，不能放進 Git。
- 上線前應更換該文件內全部密碼。
- 程式不需要主機、FTP、MySQL 或郵件密碼；不要把它們加入 Streamlit secrets 或環境變數。
- Yahoo Finance 屬外部資料來源，偶爾可能限流或暫時缺少欄位；更新版保留原有錯誤處理行為。
