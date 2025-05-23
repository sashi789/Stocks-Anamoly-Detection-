# 📈 Stock Market Anomaly Detection with Quix Streams, Kafka, and Isolation Forest

This project implements a real-time **stock market anomaly detection system** using **Kafka (via Redpanda)**, **Quix Streams**, and **Python's Isolation Forest model**. The data pipeline ingests historical stock tick data (e.g., for Nvidia) from [DataBento](https://databento.com), processes it for anomalies, and outputs flagged trades for further analysis or visualization.

---

## 🔧 Architecture Overview

```
[DataBento FTP or HTTP]
       |
       v
[Python Kafka Producer (Quix Source App)]
       |
       v
[Kafka Topic: "stocks"]
       |
       v
[Quix Transformation App - Anomaly Detection]
       |
       +--> High Volume Rule
       +--> Isolation Forest Rule
       |
       v
[Kafka Topic: "anomalies"]
       |
       +--> (Optional Sinks: Streamlit / PostgreSQL / Elasticsearch)
```

---

## ✨ Features

* Reads compressed `.zst` CSV stock tick data from NASDAQ dataset.
* Publishes data into Kafka using Quix Streams `Producer`.
* Consumes data via Quix `Transformation` app and applies:

  * ✅ High Volume Rule: Flags unusually large trades.
  * ✅ Isolation Forest Rule: Detects outliers in trade prices.
* Filters out and publishes only anomalous trades to an output topic.
* Designed with modular, scalable microservices architecture.

---

## 📁 Project Structure

```bash
.
├── producer/                # Produces raw stock data to Kafka
│   ├── main.py              # Reads from .zst files and publishes to Kafka
│   └── requirements.txt
├── anomaly_detector/       # Transformation app that detects anomalies
│   ├── main.py              # Implements high volume + isolation forest detection
│   └── requirements.txt
├── nasdaq/                 # Folder for unzipped NASDAQ tick data files (.zst)
├── .env                    # Holds environment variables for Quix apps
├── docker-compose.yml      # Redpanda + Quix environment setup
├── quix.yaml               # Quix deployment configuration
└── README.md               # This file
```

---

## 🚀 Quickstart Guide

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/stock-anomaly-detection.git
cd stock-anomaly-detection
```

### 2. Install Quix CLI and Initialize Project

```bash
npm install -g @quixio/quix-cli  # Or follow platform-specific instructions from https://quix.io/docs
quix init
```

### 3. Download Data from DataBento

* Register at [https://databento.com](https://databento.com)
* Download NASDAQ dataset for a symbol like **NVIDIA**
* Choose `.csv` format with `.zst` compression
* Place files into `nasdaq/` folder

### 4. Run the Pipeline

```bash
quix pipeline up
```

> This will spin up the Kafka broker (via Redpanda), the Producer, and Anomaly Detector apps using Quix CLI.

---

## 📊 Detection Logic

### ✅ High Volume Rule

Flags trades where the volume exceeds a defined threshold (`20,000` by default).

### ✅ Isolation Forest Rule

Detects anomalous prices using a rolling `IsolationForest` model:

* Model is (re)trained every 1000 data points.
* Uses standardized price values to identify statistical outliers.

---

## 📦 Output

* **Kafka Topic: `stocks`** → Raw published trade data
* **Kafka Topic: `anomalies`** → Only records with at least one anomaly (`high_volume`, `isolation_forest`)
* Extendable to:

  * PostgreSQL
  * Elasticsearch
  * Streamlit Dashboards

---

## 🛠 Tech Stack

* 🐍 Python 3.9
* 🧠 Scikit-learn (Isolation Forest)
* 📊 Pandas, NumPy
* ⚡ Quix Streams
* 🐘 Redpanda (Kafka-compatible)
* 📦 Docker + Quix CLI

---

## ✅ TODO / Future Extensions

* [ ] Add support for PostgreSQL and Elasticsearch as sinks
* [ ] Implement real-time dashboards with Streamlit
* [ ] Model explainability and feature importance scores
* [ ] Per-symbol anomaly thresholds and tuning

---

## 👨‍💼 Maintainer

**Sashidhar Chary Viswanathula**
Data Engineering & Analytics Enthusiast
[LinkedIn](https://www.linkedin.com/in/sashidharchary) • [GitHub](https://github.com/sashi789)

---

## 📄 License

This project is licensed under the MIT License. See `LICENSE` for more details.
