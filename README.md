# \# LINE Bot Python Template for Google Cloud Run

# 

# 這是一個專為 \*\*Google Cloud Run\*\* 設計的 LINE Bot 範本。

# 使用 Python (Flask) 框架與 Docker 容器化技術，協助開發者快速建立 Serverless 的 LINE 聊天機器人。

# 

# \## 🚀 特色 (Features)

# 

# \* \*\*Serverless 架構\*\*：部署在 Google Cloud Run，無須管理伺服器，依流量自動擴展。

# \* \*\*低成本高效益\*\*：利用 Cloud Run 的按需付費模式（包含每月免費額度），適合個人或測試專案。

# \* \*\*容器化標準\*\*：包含標準 `Dockerfile`，確保開發與生產環境一致。

# \* \*\*安全性\*\*：透過環境變數管理 LINE Channel Secret 與 Access Token。

# 

# \## 🛠️ 技術棧 (Tech Stack)

# 

# \* \[Python 3.10+](https://www.python.org/)

# \* \[Flask](https://flask.palletsprojects.com/) (Web Framework)

# \* \[LINE Bot SDK](https://github.com/line/line-bot-sdk-python)

# \* \[Google Cloud Run](https://cloud.google.com/run)

# \* \[Docker](https://www.docker.com/)

# 

# \## 📋 前置準備 (Prerequisites)

# 

# 1\.  \*\*LINE Developers Console\*\*:

# &nbsp;   \* 建立一個 Provider 與 Channel (Messaging API)。

# &nbsp;   \* 取得 \*\*Channel Secret\*\* 與 \*\*Channel Access Token\*\*。

# 2\.  \*\*Google Cloud Platform (GCP)\*\*:

# &nbsp;   \* 建立一個 GCP 專案。

# &nbsp;   \* 啟用 \*\*Cloud Run API\*\* 與 \*\*Artifact Registry API\*\*。

# &nbsp;   \* 安裝並設定 \[gcloud CLI](https://cloud.google.com/sdk/docs/install)。

# 

# \## ⚙️ 環境變數 (Environment Variables)

# 

# 本專案依賴以下環境變數，請勿將金鑰直接寫入程式碼：
| 變數名稱 | 描述 |
| :--- | :--- |
| `LINE_CHANNEL_SECRET` | LINE Channel Secret |
| `LINE_CHANNEL_ACCESS_TOKEN` | LINE Channel Access Token (Long-lived) |
| `PORT` | Cloud Run 自動注入 (預設 8080) |

# \## 💻 本地端開發 (Local Development)

# 

# \### 1. 安裝依賴

# ```bash

# pip install -r requirements.txt

