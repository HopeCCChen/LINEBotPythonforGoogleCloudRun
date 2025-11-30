# LINE Bot + Google Cloud Run 部署教學

<div align="center">

![Python](https://img.shields.io/badge/Python-3.11-blue.svg)
![Flask](https://img.shields.io/badge/Flask-3.0.0-green.svg)
![LINE Bot SDK](https://img.shields.io/badge/LINE--Bot--SDK-3.8.0-00B900.svg)
![Cloud Run](https://img.shields.io/badge/Google%20Cloud-Run-4285F4.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

一個簡單易懂的教學專案，教你如何使用 Python Flask 建立 LINE Bot 並部署到 Google Cloud Run

[開始使用](#快速開始) • [部署教學](#部署到-google-cloud-run) • [常見問題](#常見問題)

</div>

---

## 📋 目錄

- [專案簡介](#專案簡介)
- [功能特色](#功能特色)
- [系統需求](#系統需求)
- [快速開始](#快速開始)
- [專案結構](#專案結構)
- [詳細教學](#詳細教學)
  - [1. 建立 LINE Bot](#1-建立-line-bot)
  - [2. 本地開發](#2-本地開發)
  - [3. 部署到 Google Cloud Run](#3-部署到-google-cloud-run)
  - [4. 設定 Webhook](#4-設定-webhook)
- [環境變數設定](#環境變數設定)
- [常見問題](#常見問題)
- [授權條款](#授權條款)

---

## 專案簡介

這是一個完整的 LINE Bot 教學專案，展示如何：
- ✅ 使用 Python Flask 建立 LINE Bot
- ✅ 透過 Docker 容器化應用程式
- ✅ 部署到 Google Cloud Run（無伺服器架構）
- ✅ 自動擴展和高可用性
- ✅ HTTPS 安全連線（LINE 必須）

## 功能特色

- 🤖 **簡單回聲機器人** - 回覆使用者傳送的訊息
- ☁️ **無伺服器部署** - 使用 Google Cloud Run，按需付費
- 🔒 **安全性** - 使用環境變數管理敏感資訊
- 📦 **容器化** - Docker 標準化部署流程
- 🚀 **自動擴展** - 根據流量自動調整資源
- 💰 **免費額度** - Cloud Run 提供慷慨的免費配額

## 系統需求

- Python 3.11 或以上
- Docker（選用，本地測試用）
- Google Cloud 帳號
- LINE Developers 帳號

## 快速開始

### 1. Clone 專案

```bash
git clone https://github.com/HopeCCChen/LINEBotPythonforGoogleCloudRun.git
cd LINEBotPythonforGoogleCloudRun
```

### 2. 安裝依賴

```bash
pip install -r requirements.txt
```

### 3. 設定環境變數

建立 `.env` 檔案（注意：不要提交到 Git）：

```bash
LINE_CHANNEL_SECRET=your_channel_secret_here
LINE_CHANNEL_ACCESS_TOKEN=your_access_token_here
```

### 4. 本地執行

```bash
python app.py
```

伺服器會在 `http://localhost:8080` 啟動

## 專案結構

```
linebot-cloudrun/
├── app.py                  # Flask 主程式
├── requirements.txt        # Python 套件依賴
├── Dockerfile             # Docker 容器設定
├── .dockerignore          # Docker 忽略檔案
├── .gitignore            # Git 忽略檔案
├── .env.example          # 環境變數範例
└── README.md             # 專案說明文件
```

## 詳細教學

### 1. 建立 LINE Bot

#### 1.1 註冊 LINE Developers

1. 前往 [LINE Developers Console](https://developers.line.biz/)
2. 使用你的 LINE 帳號登入
3. 如果沒有 Provider，點擊「Create」建立一個

#### 1.2 建立 Messaging API Channel

1. 在 Provider 頁面點擊「Create a Messaging API channel」
2. 填寫基本資訊：
   - **Channel name**: 你的 Bot 名稱
   - **Channel description**: Bot 描述
   - **Category**: 選擇適合的分類
   - **Subcategory**: 選擇子分類
3. 同意條款後點擊「Create」

#### 1.3 取得憑證

在 Channel 設定頁面：

1. **Channel Secret**: 
   - 在「Basic settings」分頁找到
   - 點擊「Show」查看
   
2. **Channel Access Token**:
   - 在「Messaging API」分頁
   - 點擊「Issue」產生 token
   - 複製並妥善保存

⚠️ **重要**: 這些金鑰很敏感，絕對不要上傳到 GitHub！

### 2. 本地開發

#### 2.1 安裝依賴套件

```bash
# 建立虛擬環境（建議）
python -m venv venv

# 啟動虛擬環境
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# 安裝套件
pip install -r requirements.txt
```

#### 2.2 設定環境變數

複製 `.env.example` 為 `.env`：

```bash
cp .env.example .env
```

編輯 `.env` 填入你的金鑰：

```
LINE_CHANNEL_SECRET=你的Channel_Secret
LINE_CHANNEL_ACCESS_TOKEN=你的Access_Token
```

#### 2.3 執行應用程式

```bash
python app.py
```

你應該會看到：

```
* Running on http://0.0.0.0:8080
```

#### 2.4 使用 ngrok 測試（選用）

因為 LINE Webhook 需要 HTTPS，本地測試可以使用 ngrok：

```bash
# 安裝 ngrok
# 從 https://ngrok.com/ 下載

# 啟動 ngrok
ngrok http 8080
```

複製 ngrok 提供的 HTTPS URL，暫時設定到 LINE Webhook。

### 3. 部署到 Google Cloud Run

#### 3.1 安裝 Google Cloud SDK

從 [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) 下載並安裝。

#### 3.2 登入並設定專案

```bash
# 登入 Google Cloud
gcloud auth login

# 建立新專案（或使用現有專案）
gcloud projects create YOUR_PROJECT_ID --name="LINE Bot Project"

# 設定當前專案
gcloud config set project YOUR_PROJECT_ID

# 啟用計費（必須，但有免費額度）
# 前往 https://console.cloud.google.com/billing
```

#### 3.3 啟用必要的 API

```bash
# 啟用 Cloud Run 和 Container Registry
gcloud services enable cloudbuild.googleapis.com
gcloud services enable run.googleapis.com
```

#### 3.4 部署應用程式

```bash
gcloud run deploy linebot \
  --source . \
  --platform managed \
  --region asia-east1 \
  --allow-unauthenticated \
  --set-env-vars LINE_CHANNEL_SECRET=你的Channel_Secret,LINE_CHANNEL_ACCESS_TOKEN=你的Access_Token
```

**參數說明**:
- `--source .`: 使用當前目錄的程式碼
- `--region asia-east1`: 部署到台灣區域（延遲最低）
- `--allow-unauthenticated`: 允許公開存取（LINE Webhook 需要）
- `--set-env-vars`: 設定環境變數

#### 3.5 部署完成

部署成功後，你會看到：

```
Service [linebot] revision [linebot-00001-xxx] has been deployed and is serving 100 percent of traffic.
Service URL: https://linebot-xxxxx-de.a.run.app
```

**複製這個 URL**，下一步會用到！

### 4. 設定 Webhook

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

🎉 **恭喜！你的 LINE Bot 已經成功部署！**

## 環境變數設定

本專案使用以下環境變數：

| 變數名稱 | 說明 | 必填 |
|---------|------|------|
| `LINE_CHANNEL_SECRET` | LINE Channel 金鑰 | ✅ |
| `LINE_CHANNEL_ACCESS_TOKEN` | LINE Channel 存取權杖 | ✅ |
| `PORT` | 伺服器埠號（Cloud Run 自動設定） | ❌ |

### 本地開發設定

建立 `.env` 檔案：

```bash
LINE_CHANNEL_SECRET=your_secret_here
LINE_CHANNEL_ACCESS_TOKEN=your_token_here
```

然後使用 `python-dotenv` 載入（已包含在 requirements.txt）。

### Cloud Run 設定

使用 `--set-env-vars` 參數：

```bash
gcloud run deploy linebot \
  --set-env-vars LINE_CHANNEL_SECRET=xxx,LINE_CHANNEL_ACCESS_TOKEN=yyy
```

或使用 Secret Manager（生產環境建議）：

```bash
# 建立 Secret
echo -n "your_secret" | gcloud secrets create line-channel-secret --data-file=-

# 部署時使用
gcloud run deploy linebot \
  --set-secrets LINE_CHANNEL_SECRET=line-channel-secret:latest
```

## 擴展功能

### 加入更多回覆類型

編輯 `app.py` 的 `handle_message` 函數：

```python
@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    user_message = event.message.text
    
    # 根據關鍵字回覆
    if "你好" in user_message:
        reply = "你好！很高興認識你！"
    elif "天氣" in user_message:
        reply = "今天天氣很好喔！"
    else:
        reply = f"你說：{user_message}"
    
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=reply)
    )
```

### 加入圖片回覆

```python
from linebot.models import ImageSendMessage

# 回傳圖片
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

## 監控與維護

### 查看日誌

```bash
# 查看最新日誌
gcloud run logs read linebot --region asia-east1

# 即時追蹤日誌
gcloud run logs tail linebot --region asia-east1
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

### 刪除服務

```bash
gcloud run services delete linebot --region asia-east1
```

## 費用說明

### Google Cloud Run 免費額度（每月）

- 📊 **2 百萬次請求**
- ⏱️ **360,000 GB-秒**
- 🌐 **1 GB 網路流量**

對於小型 LINE Bot 來說，免費額度通常就足夠了！

### 收費標準（超過免費額度後）

- 請求次數：$0.40 USD / 百萬次請求
- CPU 使用：$0.00002400 USD / vCPU-秒
- 記憶體：$0.00000250 USD / GB-秒

💡 **建議**: 設定計費警報，避免意外費用。

## 常見問題

### Q1: Webhook 驗證失敗怎麼辦？

**A**: 檢查以下項目：
- ✅ URL 是否正確（必須是 `/callback`）
- ✅ 服務是否正常運行
- ✅ 環境變數是否正確設定
- ✅ Channel Secret 是否正確

查看 Cloud Run 日誌：
```bash
gcloud run logs tail linebot --region asia-east1
```

### Q2: Bot 沒有回應？

**A**: 檢查：
1. Webhook 是否啟用（Use webhook 開關）
2. 自動回覆訊息是否已關閉
3. Channel Access Token 是否正確
4. 查看 Cloud Run 日誌確認是否收到請求

### Q3: 如何更新環境變數？

**A**: 
```bash
gcloud run services update linebot \
  --region asia-east1 \
  --set-env-vars LINE_CHANNEL_SECRET=新的值
```

### Q4: 可以使用其他雲端服務嗎？

**A**: 可以！這個專案也可以部署到：
- AWS Lambda + API Gateway
- Azure Functions
- Heroku
- Railway
- Render

只需要調整部署設定即可。

### Q5: 如何保護環境變數安全？

**A**: 
1. ❌ 絕對不要提交 `.env` 到 Git
2. ✅ 使用 `.gitignore` 忽略敏感檔案
3. ✅ 生產環境使用 Secret Manager
4. ✅ 定期更換 Token

### Q6: 可以處理圖片或貼圖嗎？

**A**: 可以！修改 handler：

```python
from linebot.models import ImageMessage, StickerMessage

@handler.add(MessageEvent, message=ImageMessage)
def handle_image(event):
    # 處理圖片
    pass

@handler.add(MessageEvent, message=StickerMessage)
def handle_sticker(event):
    # 處理貼圖
    pass
```

## 進階主題

### 使用資料庫

加入 Cloud SQL 或 Firestore 儲存使用者資料：

```bash
# 啟用 Firestore
gcloud firestore databases create --region=asia-east1

# 在 requirements.txt 加入
google-cloud-firestore==2.13.0
```

### 加入 CI/CD

使用 GitHub Actions 自動部署：

建立 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to Cloud Run

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - uses: google-github-actions/setup-gcloud@v0
        with:
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          project_id: ${{ secrets.GCP_PROJECT_ID }}
      
      - name: Deploy
        run: |
          gcloud run deploy linebot \
            --source . \
            --region asia-east1
```

### 效能優化

1. **使用 gunicorn 多 worker**（已包含在 Dockerfile）
2. **啟用 Cloud CDN** 快取靜態資源
3. **設定最小實例數** 減少冷啟動

```bash
gcloud run services update linebot \
  --min-instances=1 \
  --region asia-east1
```

## 貢獻指南

歡迎提交 Issue 和 Pull Request！

1. Fork 本專案
2. 建立功能分支：`git checkout -b feature/amazing-feature`
3. 提交變更：`git commit -m 'Add amazing feature'`
4. 推送到分支：`git push origin feature/amazing-feature`
5. 開啟 Pull Request

## 授權條款

本專案採用 MIT 授權條款 - 詳見 [LICENSE](LICENSE) 檔案

## 參考資源

### 官方文件
- 📚 [LINE Messaging API 文件](https://developers.line.biz/en/docs/messaging-api/)
- 📚 [Google Cloud Run 文件](https://cloud.google.com/run/docs)
- 📚 [Flask 文件](https://flask.palletsprojects.com/)

### 相關教學
- [LINE Bot SDK Python](https://github.com/line/line-bot-sdk-python)
- [Cloud Run Quickstart](https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-python-service)

### 社群資源
- [LINE Developers Community](https://www.line-community.me/)
- [Google Cloud Community](https://www.googlecloudcommunity.com/)

## 聯絡方式

如有問題或建議，歡迎：
- 📧 Email: your.email@example.com
- 💬 開 Issue
- 🐦 Twitter: [@yourhandle](https://twitter.com/yourhandle)

---

<div align="center">

Made with ❤️ by [Your Name]

如果這個專案對你有幫助，請給一個 ⭐️

[回到頂部](#line-bot--google-cloud-run-部署教學)

</div>