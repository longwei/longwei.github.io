---
layout: post
title: ML in production
---

I just finished the deeplearning.ai's ML in production.
unfortunely, they remove course 2-4 as the specialization, maybe it is too focus on the tensorflow?
anyway, chip's design machine learning system provide more general idea without handon.

Here is my note on the course

Step of ML project in reverted order
* deployment
* Modeling
* Data
* scope

== deployment ==
why the model test set ok, but not in prod?
key challenges: concept drift and data drift
ask the question:
* realtime or batch, or latency or throughput
* CPU/GPU/Memory

What to monitor?
brain storm things that could go wrong
it is ok to use many metrics and gradually remove them.

common metrics:
* memory, cpu, latency, throughput, load
* avg input langth, volume, missing values, 
* return null, user redo search, click through rate

== Modeling  ==
key challenges on model selection
do well in training set measure by avg training error is not enough.
it also need to do well in dev/test set and also in business metrics
on disporportionately important or key slice of the data set. or rare classes

Get a baseline for the irreducible error/bayes error
Human level performace or older system
watch out structure and unstructured.

sanity-check for code and algorithem, see if you can overfit a small training dataset.

perform error analysis
iterative process of example <=>propose tags
* specify class labels, scratch, dent
* image properties, any blurry, dark background..
* other meta-data
* how much room of improvement is there in that tag? prioritizing what to work on




data iteration
research focus on model and hyperparameter, while production focus on data
data augmentation: create realistice mock data that algorithem does poorly on, but human do well (三年模考)
add/improve data -> training -> error analysis

can adding data hurt?
unstructred data, if the model is large and mapping from x->y is clear then adding data won't hurt.

adding features, what are the added signals that can help make a decision?

how to handle skewed dataset?
accurage not that useful, 不枉不纵
precison is on predicted：法律，宁可错放，也不要错判
recall is on the actual: 医生，不要错放，耽误治疗

performance auditing


== Data ==
is data label consistenly? data definition is hard!
define data and baseline,
label and organize data

majority type of common data problems:
unstructured
* may or may not have huge collection of unlabeled example x
* human can label more data
* data augmentation more likely to be helpful

structured
* more difficult to obtain more data
* human labeling may not be possible

when the data is small, focus on clean label, manual checking and sync labeler
when data is huge, consistent data process

get in the POC ASAP.



== scope ==
decide the key ML metrics and how it convert to $
estimation on the resource and timeline
diligence on feasibility and value
do we have features that are predictive?


==final overview==
news news article
techiques:
* estabilish a baseline, 
* balance train/dev/test split.
* error analysis
* track experiments
* deploy using tensorflow serving


POC ML Infra idea from chatgpt:
# **PoC Plan for ML Infra Project: AI Feed System**

## **Objective**
Develop a **minimal viable implementation** of an end-to-end ML pipeline for **user interaction prediction** in a social feed. The PoC will validate the feasibility of:
- Data pipeline and preprocessing
- Model training and tracking
- API-based inference
- Workflow orchestration

---

## **Phase 1: Environment Setup (Week 1)**
### **Tasks**:
✅ Set up a local development environment  
✅ Define dataset structure (synthetic or real-world)  
✅ Initialize a Git repository and project structure  

### **Tools**:  
- **Python 3.9+**  
- **Virtual environment (venv, Conda, or Poetry)**  
- **GitHub for version control**  

### **Deliverables**:  
- Basic project folder structure  
- Virtual environment with dependencies installed  

---

## **Phase 2: Data Pipeline & Validation (Week 2)**
### **Tasks**:
✅ Generate a small-scale **synthetic dataset** with:
  - `user_features` (e.g., browsing history, engagement metrics)  
  - `content_features` (e.g., post category, hashtags)  
✅ Implement basic **data validation scripts** (missing values, duplicates, type checks)  

### **Tools**:  
- **Pandas** (data handling)  
- **Great Expectations (optional)** (data validation)  

### **Deliverables**:  
- A `.csv` or `.parquet` dataset  
- A validation script that logs issues  

---

## **Phase 3: Model Training & Experiment Tracking (Week 3)**
### **Tasks**:
✅ Train an **initial model** (XGBoost or Scikit-learn)  
✅ Set up **MLflow for experiment tracking**  
✅ Evaluate with metrics (**Accuracy, Precision, Recall, F1-score**)  

### **Tools**:  
- **Scikit-learn / XGBoost** (binary classification)  
- **MLflow** (experiment tracking & model registry)  

### **Deliverables**:  
- A trained model artifact  
- MLflow logs tracking training experiments  

---

## **Phase 4: Model Serving via REST API (Week 4)**
### **Tasks**:
✅ Deploy trained model as an API using **FastAPI**  
✅ Create `/predict` endpoint to accept JSON input  
✅ Test with sample requests  

### **Tools**:  
- **FastAPI** (lightweight REST API)  
- **Uvicorn** (API server)  
- **Postman / Curl** (testing API calls)  

### **Deliverables**:  
- A running FastAPI service  
- Sample request-response logs  

---

## **Phase 5: Automating Workflow with Airflow (Week 5)**
### **Tasks**:
✅ Define Airflow DAG for:
  - **Data preprocessing → Model training → Deployment**  
✅ Test task dependencies & scheduling  

### **Tools**:  
- **Apache Airflow** (workflow automation)  

### **Deliverables**:  
- Airflow DAG running locally  
- Logs of scheduled workflow execution  

---

## **Phase 6: Evaluation & Next Steps (Week 6)**
### **Tasks**:
✅ Analyze model performance & bottlenecks  
✅ Document PoC findings  
✅ Identify scalability & production challenges  

### **Deliverables**:  
- A report summarizing feasibility, performance, and improvement areas  
- Plan for scaling (e.g., cloud deployment, monitoring, large-scale data handling)  

---

## **Stretch Goals (Optional)**
🔹 **Dockerize the application** for portability  
🔹 **Deploy to AWS/GCP** (S3, Lambda, SageMaker, etc.)  
🔹 **Implement monitoring** (Prometheus, EvidentlyAI)  

---