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
- 每支股票及每個行業資料保留一小時快取；排名進度控制函式本身不快取，避免 Streamlit 重播已失效的進度條區塊。
- 新聞、價格變化及市場報告加入對應時效快取。
- 加入 Streamlit Community Cloud、Render 及 Docker 部署設定。

## 修正記錄

### 2026-07-14｜行業排名進度條錯誤

- 修正載入完整行業排名時，快取函式重播已失效 Streamlit 進度條所造成的 `layout block created outside the function` 錯誤。
- `get_industry_average_scores()` 不再直接套用 `st.cache_data`。
- 底層 `fetch_robust_data()`、`fetch_batch_data()` 及歷史行情快取仍然保留。
- 80 個行業、評分公式、進度顯示、取消按鈕及排名結果沒有刪減或改變。

### 2026-07-14｜手機排版及行業排名效能

- 頁面側欄狀態改為 `auto`，Streamlit 在窄螢幕會先顯示主內容。
- 手機側欄寬度固定為最多 300px，與 Streamlit 的 300px 收合位移一致，避免殘留 40px 覆蓋主畫面。
- 768px 以下將所有 Streamlit 欄位改成 100% 寬垂直排列。
- 手機版縮小 Logo、頁面標題、區塊標題、總分及副標題字級。
- 表格保留水平捲動，圖片限制在螢幕寬度內。
- 行業排名先建立 752 支唯一股票清單，以最多 8 條受控執行緒一次取得，再聚合回完整 80 個行業。
- 舊版是 80 個行業逐個處理，每個行業內最多 4 條執行緒；新版移除此串行瓶頸及重複股票請求。
- 確定性效能測試中，新舊 80 行業結果完全一致，模擬速度約提升 2.64 倍。實際時間仍取決於 Yahoo Finance 回應及限流。

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

完整行業排名預設使用八條受控執行緒。若平台網路穩定可維持預設；如 Yahoo Finance 頻繁限流，可降低至 4：

```powershell
$env:GKHAW_RANKING_WORKERS = "4"
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

## 2026-07-14｜原始版 localhost 排版基準核對

已直接用 `C:\Users\User\Downloads\app (1).py` 啟動原始版 localhost，並以相同 Python 環境、相同瀏覽器分頁及相同 viewport 與更新版逐項比較。

- Downloads 原檔與最初分析用原檔的 SHA-256 均為 `45AC905ACE3A79644537C840E53BCD79533583D709897BFFDAE4BBBE7CC5E87D`，確認比較來源正確。
- 在 1440×900 下，兩版側欄皆為 `x=0、width=380`；主區塊皆為 `x=380、width=1060`；內容容器 padding 皆為 `96px 80px 160px`。
- Logo、報告標題、100px 大分數、評分／診斷欄、進階分析三欄及五個按鈕的位置與尺寸完全一致。
- 在 1280×720 下再次用同一分頁先後載入兩版，關鍵 DOM 幾何與 computed style 仍完全一致；內容容器皆為 `x=380、width=890`。
- 因此更新版沒有改變桌面排版。肉眼差異通常來自瀏覽器視窗寬度不同、縮放不是 100%、側欄開合狀態不同，或瀏覽器仍使用舊快取。
- 只有螢幕寬度不超過 768px 時，更新版會啟用手機響應式排列；這是為修正原版手機排版而刻意加入，桌面基準不受影響。
- 原版雖 import Plotly，但程式沒有使用它；Plotly 只安裝在本機測試環境以便啟動原版，未加入部署依賴。
