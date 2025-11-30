# LINE Bot + Google Cloud Run 部署教學

![Python](https://img.shields.io/badge/Python-3.11-blue.svg)
![Flask](https://img.shields.io/badge/Flask-3.0.0-green.svg)
![LINE Bot SDK](https://img.shields.io/badge/LINE--Bot--SDK-3.8.0-00B900.svg)
![Cloud Run](https://img.shields.io/badge/Google%20Cloud-Run-4285F4.svg)

完整的 LINE Bot 教學專案，直接部署到 Google Cloud Run 無伺服器平台

---

## 目錄

- [專案簡介](#專案簡介)
- [功能特色](#功能特色)
- [系統需求](#系統需求)
- [快速開始](#快速開始)
- [詳細教學](#詳細教學)
- [環境變數設定](#環境變數設定)
- [常見問題](#常見問題)

---

## 專案簡介

這是一個完整的 LINE Bot 教學專案，展示如何：

- 使用 Python Flask 建立 LINE Bot
- 透過 Docker 容器化應用程式
- 部署到 Google Cloud Run（無伺服器架構）
- 自動擴展和高可用性
- HTTPS 安全連線（LINE 必須）

## 功能特色

- 簡單回聲機器人 - 回覆使用者傳送的訊息
- 無伺服器部署 - 使用 Google Cloud Run，按需付費
- 安全性 - 使用環境變數管理敏感資訊
- 容器化 - Docker 標準化部署流程
- 自動擴展 - 根據流量自動調整資源
- 免費額度 - Cloud Run 提供慷慨的免費配額

## 系統需求

- Google Cloud 帳號（需要綁定信用卡，但有免費額度）
- LINE Developers 帳號
- Google Cloud SDK（gcloud CLI）

---

## 快速開始

### 1. Clone 專案

```bash
git clone https://github.com/HopeCCChen/LINEBotPythonforGoogleCloudRun.git
cd LINEBotPythonforGoogleCloudRun
```

### 2. 建立 LINE Bot 並取得憑證

前往 [LINE Developers Console](https://developers.line.biz/) 建立 Messaging API Channel，取得：
- Channel Secret
- Channel Access Token

### 3. 部署到 Google Cloud Run

```bash
gcloud run deploy linebot \
  --source . \
  --platform managed \
  --region asia-east1 \
  --allow-unauthenticated \
  --set-env-vars LINE_CHANNEL_SECRET=你的Channel_Secret,LINE_CHANNEL_ACCESS_TOKEN=你的Access_Token
```

### 4. 設定 LINE Webhook

將 Cloud Run 提供的 URL 設定到 LINE Developers Console 的 Webhook URL：

```
https://你的Cloud_Run_URL/callback
```

完成！你的 LINE Bot 已經上線了！

---

## 專案結構

```
LINEBotPythonforGoogleCloudRun/
├── app.py                  # Flask 主程式
├── requirements.txt        # Python 套件依賴
├── Dockerfile             # Docker 容器設定
├── .dockerignore          # Docker 忽略檔案
├── .gitignore            # Git 忽略檔案
└── README.md             # 專案說明文件
```

---

## 詳細教學

### 步驟 1：建立 LINE Bot

#### 1.1 註冊 LINE Developers

1. 前往 [LINE Developers Console](https://developers.line.biz/)
2. 使用你的 LINE 帳號登入
3. 如果沒有 Provider，點擊「Create」建立一個

#### 1.2 建立 Messaging API Channel

1. 在 Provider 頁面點擊「Create a Messaging API channel」
2. 填寫基本資訊：
   - Channel name: 你的 Bot 名稱
   - Channel description: Bot 描述
   - Category: 選擇適合的分類
3. 同意條款後點擊「Create」

#### 1.3 取得憑證

在 Channel 設定頁面：

**Channel Secret**
- 在「Basic settings」分頁找到
- 點擊「Show」查看
   
**Channel Access Token**
- 在「Messaging API」分頁
- 點擊「Issue」產生 token
- 複製並妥善保存

> 重要：這些金鑰很敏感，不要分享給他人！

---

### 步驟 2：設定 Google Cloud

#### 2.1 安裝 Google Cloud SDK

從 [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) 下載並安裝

#### 2.2 登入並設定專案

```bash
# 登入 Google Cloud
gcloud auth login

# 建立新專案（或使用現有專案）
gcloud projects create YOUR_PROJECT_ID --name="LINE Bot Project"

# 設定當前專案
gcloud config set project YOUR_PROJECT_ID
```

> 注意：需要在 Google Cloud Console 啟用計費，但有免費額度

#### 2.3 啟用必要的 API

```bash
gcloud services enable cloudbuild.googleapis.com
gcloud services enable run.googleapis.com
```

---

### 步驟 3：部署到 Google Cloud Run

#### 3.1 執行部署指令

```bash
gcloud run deploy linebot \
  --source . \
  --platform managed \
  --region asia-east1 \
  --allow-unauthenticated \
  --set-env-vars LINE_CHANNEL_SECRET=你的Channel_Secret,LINE_CHANNEL_ACCESS_TOKEN=你的Access_Token
```

參數說明：
- `--source .` 使用當前目錄的程式碼（Cloud Build 會自動建置）
- `--region asia-east1` 部署到台灣區域（延遲最低）
- `--allow-unauthenticated` 允許公開存取（LINE Webhook 需要）
- `--set-env-vars` 設定環境變數

#### 3.2 等待部署完成

部署過程大約需要 2-3 分鐘，你會看到：

```
Building using Dockerfile and deploying container to Cloud Run service [linebot]...
✓ Creating Revision...
✓ Routing traffic...
Done.
Service [linebot] revision [linebot-00001-xxx] has been deployed and is serving 100 percent of traffic.
Service URL: https://linebot-xxxxx-de.a.run.app
```

**複製這個 Service URL**，下一步會用到！

---

### 步驟 4：設定 LINE Webhook

#### 4.1 設定 Webhook URL

1. 回到 [LINE Developers Console](https://developers.line.biz/)
2. 選擇你的 Channel
3. 進入「Messaging API」分頁
4. 找到「Webhook settings」
5. 在「Webhook URL」填入：

```
https://你的Cloud_Run_URL/callback
```

例如：`https://linebot-xxxxx-de.a.run.app/callback`

#### 4.2 驗證 Webhook

1. 點擊「Verify」按鈕測試連線
2. 應該會顯示「Success」✅
3. 開啟「Use webhook」開關

#### 4.3 停用自動回覆訊息

為了避免干擾，建議關閉 LINE 的預設回覆：

1. 在「Messaging API」分頁
2. 找到「Auto-reply messages」
3. 點擊「Edit」
4. 關閉自動回覆功能

#### 4.4 測試你的 Bot

1. 在 Channel 頁面找到 QR Code
2. 用 LINE App 掃描加入好友
3. 傳送任何訊息
4. Bot 應該會回覆：「你說：[你的訊息]」

🎉 恭喜！你的 LINE Bot 已經成功部署！

---

## 環境變數設定

本專案使用以下環境變數：

| 變數名稱 | 說明 | 必填 |
|---------|------|------|
| LINE_CHANNEL_SECRET | LINE Channel 金鑰 | 是 |
| LINE_CHANNEL_ACCESS_TOKEN | LINE Channel 存取權杖 | 是 |
| PORT | 伺服器埠號（Cloud Run 自動設定） | 否 |

### 部署時設定

使用 `--set-env-vars` 參數：

```bash
gcloud run deploy linebot \
  --source . \
  --region asia-east1 \
  --set-env-vars LINE_CHANNEL_SECRET=xxx,LINE_CHANNEL_ACCESS_TOKEN=yyy
```

### 部署後更新環境變數

```bash
gcloud run services update linebot \
  --region asia-east1 \
  --set-env-vars LINE_CHANNEL_SECRET=新的值
```

### 使用 Secret Manager（進階，更安全）

```bash
# 建立 Secret
echo -n "your_secret" | gcloud secrets create line-channel-secret --data-file=-

# 部署時使用
gcloud run deploy linebot \
  --source . \
  --region asia-east1 \
  --set-secrets LINE_CHANNEL_SECRET=line-channel-secret:latest
```

---

## 擴展功能

### 加入關鍵字回覆

編輯 `app.py` 的 `handle_text_message` 函數：

```python
@handler.add(MessageEvent, message=TextMessage)
def handle_text_message(event):
    user_message = event.message.text
    
    if "你好" in user_message:
        reply = "你好！很高興認識你！😊"
    elif "天氣" in user_message:
        reply = "今天天氣很好喔！"
    elif "功能" in user_message:
        reply = "我可以回覆你的訊息，試試看傳訊息給我！"
    else:
        reply = f"你說：{user_message}"
    
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=reply)
    )
```

修改後重新部署：

```bash
gcloud run deploy linebot --source . --region asia-east1
```

### 加入圖片回覆

```python
from linebot.models import ImageSendMessage

line_bot_api.reply_message(
    event.reply_token,
    ImageSendMessage(
        original_content_url='https://example.com/image.jpg',
        preview_image_url='https://example.com/preview.jpg'
    )
)
```

### 加入按鈕選單

```python
from linebot.models import (
    TemplateSendMessage, ButtonsTemplate, MessageAction
)

buttons_template = ButtonsTemplate(
    title='請選擇',
    text='你想要什麼服務？',
    actions=[
        MessageAction(label='選項 1', text='選項1'),
        MessageAction(label='選項 2', text='選項2'),
    ]
)

line_bot_api.reply_message(
    event.reply_token,
    TemplateSendMessage(alt_text='選單', template=buttons_template)
)
```

---

## 監控與維護

### 查看日誌

```bash
# 查看最新日誌
gcloud run logs read linebot --region asia-east1

# 即時追蹤日誌
gcloud run logs tail linebot --region asia-east1

# 查看特定時間的日誌
gcloud run logs read linebot --region asia-east1 --limit 100
```

### 查看服務狀態

```bash
gcloud run services describe linebot --region asia-east1
```

### 更新服務

修改程式碼後，重新部署：

```bash
gcloud run deploy linebot --source . --region asia-east1
```

### 設定最小實例數（避免冷啟動）

```bash
gcloud run services update linebot \
  --min-instances=1 \
  --region asia-east1
```

> 注意：最小實例數會持續計費

### 刪除服務

```bash
gcloud run services delete linebot --region asia-east1
```

---

## 費用說明

### Google Cloud Run 免費額度（每月）

- 📊 2 百萬次請求
- ⏱️ 360,000 GB-秒
- 🌐 1 GB 網路流量（北美）

對於小型 LINE Bot 來說，**免費額度通常就足夠了**！

### 收費標準（超過免費額度後）

- 請求次數：$0.40 USD / 百萬次請求
- CPU 使用：$0.00002400 USD / vCPU-秒
- 記憶體：$0.00000250 USD / GB-秒
- 網路流量：$0.12 USD / GB

### 節省費用小技巧

1. 不設定最小實例數（讓它自動縮放到 0）
2. 優化程式碼，減少 CPU 使用時間
3. 設定計費警報

```bash
# 設定預算警報
gcloud billing budgets create \
  --billing-account=YOUR_BILLING_ACCOUNT_ID \
  --display-name="LINE Bot Budget" \
  --budget-amount=10USD
```

---

## 常見問題

### Q1：Webhook 驗證失敗怎麼辦？

**檢查清單：**
- ✅ URL 是否正確（必須是 `/callback`）
- ✅ 服務是否正常運行
- ✅ 環境變數是否正確設定
- ✅ Channel Secret 是否正確

**查看日誌：**

```bash
gcloud run logs tail linebot --region asia-east1
```

### Q2：Bot 沒有回應？

**檢查步驟：**
1. Webhook 是否啟用（Use webhook 開關）
2. 自動回覆訊息是否已關閉
3. Channel Access Token 是否正確
4. 查看 Cloud Run 日誌確認是否收到請求

```bash
gcloud run logs read linebot --region asia-east1 --limit 50
```

### Q3：如何更新程式碼？

```bash
# 修改程式碼後
git add .
git commit -m "Update bot logic"
git push

# 重新部署
gcloud run deploy linebot --source . --region asia-east1
```

### Q4：如何更新環境變數？

```bash
gcloud run services update linebot \
  --region asia-east1 \
  --set-env-vars LINE_CHANNEL_SECRET=新的值,LINE_CHANNEL_ACCESS_TOKEN=新的值
```

### Q5：部署時出現權限錯誤？

確保你的 Google Cloud 專案已啟用計費，並且有足夠的權限：

```bash
# 查看當前專案
gcloud config get-value project

# 確認已啟用必要的 API
gcloud services list --enabled
```

### Q6：如何查看服務的 URL？

```bash
gcloud run services describe linebot \
  --region asia-east1 \
  --format 'value(status.url)'
```

### Q7：可以使用自訂網域嗎？

可以！在 Cloud Run 主控台設定自訂網域映射：

1. 前往 Cloud Run 主控台
2. 選擇你的服務
3. 點擊「MANAGE CUSTOM DOMAINS」
4. 新增你的網域並設定 DNS

---

## 進階功能

### 使用 Cloud SQL 資料庫

```bash
# 建立 Cloud SQL 實例
gcloud sql instances create linebot-db \
  --database-version=POSTGRES_14 \
  --tier=db-f1-micro \
  --region=asia-east1

# 連接到 Cloud Run
gcloud run services update linebot \
  --add-cloudsql-instances=YOUR_PROJECT_ID:asia-east1:linebot-db
```

### 使用 Cloud Firestore

```bash
# 啟用 Firestore
gcloud firestore databases create --region=asia-east1

# 在 requirements.txt 加入
google-cloud-firestore==2.13.0
```

### 設定 CI/CD 自動部署

在 GitHub 建立 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to Cloud Run

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: google-github-actions/auth@v2
        with:
          credentials_json: ${{ secrets.GCP_SA_KEY }}
      
      - name: Deploy to Cloud Run
        run: |
          gcloud run deploy linebot \
            --source . \
            --region asia-east1 \
            --project ${{ secrets.GCP_PROJECT_ID }}
```

---

## 除錯技巧

### 1. 即時查看日誌

```bash
gcloud run logs tail linebot --region asia-east1
```

### 2. 測試 Webhook 連線

```bash
curl -X POST https://你的Cloud_Run_URL/callback \
  -H "Content-Type: application/json" \
  -d '{"events":[]}'
```

### 3. 查看服務詳細資訊

```bash
gcloud run services describe linebot --region asia-east1
```

### 4. 查看最近的請求

在 Google Cloud Console > Cloud Run > 你的服務 > LOGS

---

## 參考資源

### 官方文件

- [LINE Messaging API 文件](https://developers.line.biz/en/docs/messaging-api/)
- [Google Cloud Run 文件](https://cloud.google.com/run/docs)
- [Flask 文件](https://flask.palletsprojects.com/)

### 相關專案

- [LINE Bot SDK Python](https://github.com/line/line-bot-sdk-python)
- [Cloud Run 範例](https://github.com/GoogleCloudPlatform/cloud-run-samples)

### 社群資源

- [LINE Developers Community](https://www.line-community.me/)
- [Google Cloud Community](https://www.googlecloudcommunity.com/)

---

## 授權條款

本專案採用 MIT 授權條款 - 詳見 [LICENSE](LICENSE) 檔案

---

## 貢獻

歡迎提交 Issue 和 Pull Request！

---

## 作者

Hope Chen - [GitHub](https://github.com/HopeCCChen)

---

如果這個專案對你有幫助，請給一個星星 ⭐️

Made with ❤️ for LINE Bot developers