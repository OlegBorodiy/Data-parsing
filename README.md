# 🦅 Birdeye Token Tracker

A real-time tracker for newly listed **Solana tokens** using the [Birdeye public WebSocket API](https://docs.birdeye.so/). This service is built to run on **Google Cloud Run** and store swap transactions in **Google Cloud Storage (GCS)**.

---

## 🚀 Features

- ✨ **Live subscription** to new Solana token listings
- 📈 **Automatic tracking** of swap transactions
- 🔍 Extracts and organizes transaction data
- ↓ **Saves structured JSON files** to GCS per token
- ♻️ Designed for scalable, serverless deployment

---

## 📊 How It Works

### 1. WebSocket Integration
Connects to the Birdeye public WebSocket endpoint:
```
wss://public-api.birdeye.so/socket/solana?x-api-key=<YOUR_API_KEY>
```

### 2. Token Discovery
Subscribes to:
- `SUBSCRIBE_TOKEN_NEW_LISTING`
- `SUBSCRIBE_TXS` (using `complex` query for multiple tokens)

### 3. Token Tracking
- Maintains a list of token addresses
- Batches them in groups of 100 for efficient subscription

### 4. Transaction Parsing
- Detects the `to.address` as the target token
- Converts `blockUnixTime` to readable UTC timestamps
- Builds structured filename paths:

```
transactions/<token_address>/<timestamp>.json
```

### 5. Cloud Storage
- Uploads each parsed transaction as a pretty-printed JSON file to Google Cloud Storage

---

## ⛅️ Cloud Deployment

Deploy on **Google Cloud Run** with:

```bash
gcloud run deploy birdeye-tracker \
  --image=us-central1-docker.pkg.dev/YOUR_PROJECT/YOUR_REPO/birdeye-tracker:latest \
  --platform=managed \
  --region=us-central1 \
  --allow-unauthenticated \
  --set-env-vars=GCS_BUCKET_NAME=your-bucket,BIRDEYE_API_KEY=your-api-key \
  --cpu=0.25 \
  --memory=512Mi \
  --timeout=900
```

---

## 📁 Cloud Storage Structure

Example storage path:
```
gs://birdeye-tracker-bucket/transactions/9sPLi516Mu9E1oYDawakcReNjDRTfkt2Y24WGFvWRn4J/2025-03-21_01-30-38.json
```

---

## 📅 Requirements

- Python 3.9+
- `google-cloud-storage`
- `websockets`

### Python dependencies
```bash
pip install -r requirements.txt
```

---

## 🚪 Local Testing

You can run the service locally:
```bash
python3 birdeye_tracker.py
```
Make sure you:
- Set the required env vars
- Are authenticated with GCP locally (via `gcloud auth application-default login`)

---

## 🌐 License
MIT. Use this freely for research, testing, or to build something amazing ✨

