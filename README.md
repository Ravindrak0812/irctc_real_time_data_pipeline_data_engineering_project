# 🚆 IRCTC Real-Time Data Pipeline (GCP)  

A real-time streaming pipeline that captures IRCTC ticket booking data using **Pub/Sub**, processes it with **Dataflow** via a **UDF**, stores raw data in **GCS**, and writes structured results to **BigQuery**  

---

## 📌 Architecture & Flow  

```
[ Pub/Sub Topic ]
        ↓
[ Dataflow Pipeline ] → UDF transforms → Read function from GCS and also store
the rejected data(Google Cloud Storage) → Writes to BigQuery (analytics)
```

### 🔄 Steps:
1. **Simulate or stream IRCTC booking events** → Pub/Sub  
2. **Dataflow ingestion** → Reads from Pub/Sub via subscription  
3. **Apply UDF transform** → Clean/enrich data  
4. **read udf from** → GCS bucket  
5. **Load structured data** → Write to BigQuery tables  

---

## 🛠️ Tech Stack & Components  

- 🚀 **Google Cloud Platform (GCP)**
- 📩 **Pub/Sub** – Streaming ingestion of ticket events  
- ⚙️ **Dataflow** – Managed Apache Beam pipeline with UDF  
- 🗄️ **Google Cloud Storage (GCS)** – Raw data backup and storage  
- 📊 **BigQuery** – Queryable analytics datastore  

---

## 🚀 Getting Started  

### ✅ Prerequisites
- GCP account with billing enabled  
- Permissions: Pub/Sub, Dataflow, GCS, BigQuery  
- Local setup:  
  ```bash
  gcloud auth login  
  gcloud config set project YOUR_PROJECT_ID  
  ```

### 1️⃣ Provision Infrastructure

```bash
# Create Pub/Sub resources
gcloud pubsub topics create irctc-topic
gcloud pubsub subscriptions create irctc-sub --topic=irctc-topic

# GCS for raw backups
gsutil mb -l REGION gs://YOUR_BUCKET_NAME

# BigQuery dataset/table
bq mk YOUR_DATASET
# Optional: define schema for table
```

### 2️⃣ Pipeline Code Setup

```bash
git clone https://github.com/Ravindrak0812/irctc_real_time_data_pipeline_data_engineering_project.git
cd irctc_real_time_data_pipeline_data_engineering_project
```

Key files:  
- `pipeline.py` – Dataflow pipeline logic  
- `udf.py` – Custom UDF transformations  
- `data_generator.py` – Event generator for IRCTC bookings  

### 3️⃣ Run Locally (DirectRunner)

```bash
python data_generator.py --topic irctc-topic
python pipeline.py   --input_topic projects/${PROJECT_ID}/topics/irctc-topic   --output_gcs gs://YOUR_BUCKET_NAME/raw/   --output_bq ${PROJECT_ID}:YOUR_DATASET.your_table   --runner DirectRunner
```

### 4️⃣ Deploy to Dataflow (Production)

```bash
python pipeline.py   --input_topic projects/${PROJECT_ID}/topics/irctc-topic   --output_gcs gs://YOUR_BUCKET_NAME/raw/   --output_bq ${PROJECT_ID}:YOUR_DATASET.your_table   --runner DataflowRunner   --project ${PROJECT_ID}   --region YOUR_REGION   --temp_location gs://YOUR_BUCKET_NAME/temp/   --job_name irctc-dataflow-job
```

### 5️⃣ Verify Data

- GCS raw files → `gsutil ls gs://YOUR_BUCKET_NAME/raw/`  
- BigQuery → Query transformed structured data  

---

## 🌟 Optional Enhancements  

- Schema evolution automation in BigQuery  
- Enrichment in UDF (geo, metadata, validation)  
- Monitoring with Stackdriver & alerts  
- Visualization dashboards in Looker Studio  

---

## 📝 Tips & Best Practices   

- Regularly **clean up GCS and BigQuery** to manage costs  
- Batch small files in GCS to prevent bloat  
- Use **Dataflow templates** for recurring jobs  
- Add retries and error-handling in UDF for resilience  

---


