<div align="center">

#  Customer Categorizer

**End-to-end Machine Learning pipeline to categorize customers using demographic and transaction data.**

Dynamic cluster prediction · FastAPI endpoints · MLOps tracking · Docker deployment · CI via GitHub Actions

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-API-009688?logo=fastapi&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E?logo=scikitlearn&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)

</div>

---

##  Overview

Customer Categorizer groups customers into meaningful segments using their **demographic** and **transaction** data. Instead of hard-coding the number of segments, the pipeline supports **dynamic cluster prediction**, and the trained model is exposed through **FastAPI** endpoints so any application can request a customer's category in real time.

The project follows an MLOps-style structure: modular pipeline components, config-driven runs, experiment tracking, a Docker image for reproducible deployment, and a GitHub Actions workflow for CI.

**Typical use cases (BFSI / Fintech / Retail):**
- Customer segmentation for targeted marketing
- Identifying high-value vs. low-engagement customers
- Personalised offers and product recommendations
- Risk and retention analysis by segment

---

##  Key Features

- **End-to-end ML pipeline**: ingestion → validation → transformation → training → evaluation → pushing
- **Separate training and prediction pipelines**
- **Dynamic cluster prediction**: number of customer segments is not fixed blindly
- **FastAPI endpoints**: serve predictions through a REST API
- **Web interface**: simple HTML templates with CSS for manual predictions
- **MLOps tracking**: experiments, parameters and metrics are logged
- **Model evaluation gate**: a new model is pushed only after it is evaluated
- **Dockerised**: one-command build and run
- **CI workflow**: automated checks through GitHub Actions

---

##  Pipeline Architecture

The project is split into two main pipelines: a **Training Pipeline** that builds and publishes the model, and a **Prediction Pipeline** that uses the published model to categorize new customers.

###  Training Pipeline

<p align="center">
  <img src="flowchart/training%20pipeline.png" alt="Training Pipeline Flowchart" width="900">
</p>

###  Prediction Pipeline

<p align="center">
  <img src="flowchart/prediction%20pipeline.png" alt="Prediction Pipeline Flowchart" width="900">
</p>

---

##  Pipeline Components (Stage by Stage)

### 1️ Data Ingestion

Loads the raw demographic and transaction data from the source, and stores it as ingested artifacts (train / test data) for the next stages.

<p align="center">
  <img src="flowchart/Data%20Ingestion.png" alt="Data Ingestion Flowchart" width="800">
</p>

### 2️ Data Validation

Checks that the ingested data is usable: expected columns and schema, data types, missing values, and overall data quality, before it moves ahead.

<p align="center">
  <img src="flowchart/data%20validation.png" alt="Data Validation Flowchart" width="800">
</p>

### 3️ Data Transformation

Cleans and prepares the validated data: handles missing values, encodes categorical features, scales numerical features, and saves the preprocessing object so the same transformation is reused at prediction time.

<p align="center">
  <img src="flowchart/data%20transformation.png" alt="Data Transformation Flowchart" width="800">
</p>

### 4️ Model Trainer

Trains the clustering model on the transformed data, using dynamic cluster selection to decide the number of customer segments, and saves the trained model.

<p align="center">
  <img src="flowchart/model%20trainer.png" alt="Model Trainer Flowchart" width="800">
</p>

### 5️ Model Evaluation

Evaluates the newly trained model and compares it with the currently deployed model, so that only a model that performs well enough moves forward.

<p align="center">
  <img src="flowchart/Model%20Evaluation.png" alt="Model Evaluation Flowchart" width="800">
</p>

### 6️ Model Pusher

Pushes the accepted model to its deployment location so the prediction pipeline and API always use the latest approved version.

<p align="center">
  <img src="flowchart/Model%20Pusher.png" alt="Model Pusher Flowchart" width="800">
</p>

---

##  Project Structure

```
Customer-Categorizer/
├── .github/workflows/   # CI pipeline (GitHub Actions)
├── assignment/          # Problem statement / assignment files
├── config/              # Configuration files
├── docs/                # Additional documentation
├── flowchart/           # Pipeline flowchart images
├── notebooks/           # EDA and experimentation notebooks
├── scripts/             # Helper / pipeline run scripts
├── src/                 # Core source code (pipeline components)
├── static/css/          # Styles for the web UI
├── templates/           # HTML templates for the web UI
├── .dockerignore
├── .gitignore
├── Dockerfile           # Container definition
├── app.py               # FastAPI application entry point
├── requirements.txt     # Python dependencies
├── setup.py             # Package setup
└── README.md
```

---

##  Tech Stack

| Area | Tools |
|------|-------|
| Language | Python |
| Data & ML | Pandas, NumPy, scikit-learn |
| API | FastAPI, Uvicorn |
| MLOps / Tracking | Experiment tracking (see `config/` and `src/` for the tool used) |
| Containerisation | Docker |
| CI/CD | GitHub Actions |
| Frontend | HTML, CSS (Jinja templates) |

---

##  Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/ahmedrajakhan/Customer-Categorizer.git
cd Customer-Categorizer
```

### 2. Create a virtual environment

```bash
python -m venv venv

# Linux / macOS
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the application

```bash
uvicorn app:app --reload
```

The app will be available at: **http://127.0.0.1:8000**

Interactive API docs (Swagger UI): **http://127.0.0.1:8000/docs**

---

##  Run with Docker

```bash
# Build the image
docker build -t customer-categorizer .

# Run the container
docker run -p 8000:8000 customer-categorizer
```

Then open **http://localhost:8000**.

---

##  API Usage

Open `/docs` after starting the server to see every available endpoint and try them live.

**Example request** (adjust the fields to match the model's input features):

```bash
curl -X POST "http://127.0.0.1:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{
        "age": 35,
        "income": 60000,
        "total_transactions": 42,
        "avg_transaction_amount": 1250.5
      }'
```

**Example response:**

```json
{
  "cluster": 2,
  "category": "High Value Customer"
}
```

>  Endpoint names and input fields above are examples. Update them to match `app.py` and the trained model's features.

---

##  CI/CD

A GitHub Actions workflow in `.github/workflows/` runs automatically on push / pull request to keep the project healthy and the Docker build reproducible.

---

##  Roadmap

- [ ] Add model monitoring and drift detection
- [ ] Add unit tests for pipeline components
- [ ] Push Docker image to a container registry
- [ ] Add a dashboard (Power BI / Streamlit) for segment insights
- [ ] Deploy on a cloud platform (AWS)

---

##  Contributing

Contributions, issues and feature requests are welcome. Feel free to open an issue or submit a pull request.

1. Fork the repo
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

##  Author

**Ahmed Raza Khan**
- GitHub: [github.com/ahmedrajakhan](https://github.com/ahmedrajakhan)
- Project: [github.com/ahmedrajakhan/Customer-Categorizer](https://github.com/ahmedrajakhan/Customer-Categorizer)

---

<div align="center">

⭐ If you found this project useful, consider giving it a star!

</div>
