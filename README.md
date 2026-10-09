# Predictive Aviation Analytics & Flight Delay Mitigation Engine

**Project 131 | Team 19 — Team Vortex | B.Tech (CS AIML), GLA University, Mathura**

A machine-learning decision-support prototype designed to predict flight delay risk and estimated delay duration using historical flight performance, weather conditions, and airport traffic/congestion features.

> **Status:** Academic project/prototype. Live data integrations, ONNX optimization, and production deployment are intended extensions unless implemented and tested in this repository.

## Table of Contents
- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [Proposed Solution](#proposed-solution)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Data Sources](#data-sources)
- [Machine Learning Methodology](#machine-learning-methodology)
- [API Design](#api-design)
- [Getting Started](#getting-started)
- [Suggested Project Structure](#suggested-project-structure)
- [Evaluation](#evaluation)
- [Target Users and Use Cases](#target-users-and-use-cases)
- [Limitations](#limitations)
- [Future Scope](#future-scope)
- [Team](#team)
- [References](#references)

## Overview

Flight delays can be caused or worsened by changing weather, airport congestion, and network-wide operational effects. Static schedules alone cannot capture all changing conditions.

This project proposes an aviation analytics engine that combines historical flight records with contextual weather and traffic data to provide:
1. A probability that a flight will be delayed by **more than 15 minutes**.
2. An estimated delay duration in minutes.
3. Visual risk and context summaries in a Streamlit dashboard.
4. Structured predictions through a FastAPI REST API.

The system is intended to support analysis and planning. It is **not** intended to make autonomous flight-dispatch or safety-critical decisions.

## Problem Statement

Static flight schedules frequently fail to account for dynamic meteorological fluctuations and airport congestion bottlenecks. These disruptions can lead to operational inefficiencies for airline carriers and uncertainty for passengers.

The project aims to combine historical flight-performance data with weather telemetry and airport traffic/congestion indicators to identify delay risks before they escalate.

## Objectives

- Predict the probability of a delay exceeding 15 minutes.
- Estimate delay duration using a regression model.
- Engineer temporal, carrier, route, airport, weather, and congestion features.
- Compare candidate models using classification and regression metrics.
- Expose predictions through a FastAPI REST service.
- Present results through an interactive Streamlit dashboard.
- Explore ONNX Runtime for efficient local CPU inference, targeting sub-50 ms model inference where achievable.

## Proposed Solution

1. **Collect data:** obtain historical flight records and compatible weather and traffic/congestion data.
2. **Validate and clean:** inspect missing values, duplicates, timestamps, airport identifiers, and outliers.
3. **Align data:** join weather and traffic observations to flights using airport and time information.
4. **Engineer features:** derive time patterns, carrier, route, weather, and rolling traffic features.
5. **Train models:** train a classifier for the `>15-minute delay` target and a regressor for delay duration.
6. **Evaluate:** compare a baseline and boosted models using held-out data.
7. **Serve predictions:** load the selected model behind a FastAPI endpoint.
8. **Visualize:** use Streamlit to display risk probabilities, estimated duration, and context.

## Key Features

- **Delay-risk classification:** probability of delay exceeding 15 minutes.
- **Delay-duration regression:** estimated delay duration in minutes.
- **Weather context:** intended use of visibility, wind speed, and precipitation where data is available.
- **Congestion context:** airport traffic density and rolling congestion metrics where available.
- **Feature engineering:** temporal, carrier, airport, and route features.
- **Interactive dashboard:** risk gauges, trends, and weather/congestion visualizations.
- **REST API:** structured JSON responses through FastAPI.
- **Model optimization:** ONNX Runtime is a planned optimization target.
- **Operational summary:** structured output to support logistics planning.

Feature availability depends on the datasets actually acquired and integrated. This README describes the intended design and does not imply every component is already implemented.

## Architecture

```text
Historical Flight Records ─┐
Weather Telemetry ─────────┼──> Data Validation & Cleaning
Airport Traffic Data ──────┘              |
                                          v
                              Time/Airport Data Alignment
                                          |
                                          v
                               Feature Engineering
                                          |
                                          v
                          Classifier + Delay Regressor
                                          |
                              Evaluation & Serialization
                                          |
                                          v
                              FastAPI / Uvicorn API
                                          |
                                          v
                                Streamlit Dashboard
                     Risk Probability • Delay Estimate
                     Weather Context • Congestion Views
```

## Technology Stack

| Area | Technologies |
|---|---|
| Language | Python |
| Data processing | pandas, NumPy |
| Machine learning | scikit-learn, XGBoost and/or LightGBM |
| Visualization | Matplotlib, Seaborn, Streamlit |
| API | FastAPI, Uvicorn |
| Inference optimization | ONNX Runtime (planned/subject to compatibility) |
| Data sources | Kaggle flight-delay dataset, U.S. DOT BTS, compatible weather/traffic sources |
| Testing | pytest and/or Postman |

## Data Sources

### Primary historical dataset
- **2015 Flight Delays and Cancellations — Kaggle / U.S. DOT:** https://www.kaggle.com/usdot/flight-delays/data

### Aviation statistics
- **U.S. Bureau of Transportation Statistics — TranStats:** https://transtats.bts.gov/

The proposed pipeline also requires compatible hourly weather observations (such as visibility, wind speed, and precipitation) and airport traffic/congestion indicators. These additional sources must be selected, checked for licensing, and aligned to flight timestamps and airport identifiers.

**Data note:** The primary dataset cited above is historical and U.S.-based. It does not by itself provide current real-time conditions or confirm India-specific coverage. Document any change of dataset or geographic scope in the implementation and final report.

Do not commit large raw datasets, API keys, or private credentials to GitHub. Prefer download instructions or small synthetic samples where appropriate.

## Machine Learning Methodology

### Target variables
- **Classification:** whether the selected delay measure exceeds 15 minutes.
- **Regression:** delay duration in minutes.

The implementation should define whether the target is departure delay, arrival delay, or another delay field and use that definition consistently.

### Feature engineering
Potential features include:
- Scheduled departure hour and day of week.
- Carrier identifier.
- Origin/destination airport and route identifiers.
- Weather conditions aligned to the relevant airport and prediction time.
- Rolling airport traffic/congestion metrics, where available.

Only information available at prediction time should be used. Avoid target leakage from actual outcomes or future observations.

### Candidate models
- Establish a simple baseline.
- Compare XGBoost and/or LightGBM classifiers for delay-risk probability.
- Train a regression model to estimate delay duration.
- Use time-aware validation where appropriate.
- Save preprocessing with the model so inference uses the same transformations as training.

### Evaluation
Report measured results rather than assumed performance:
- **ROC-AUC** for classification.
- **MAE** and **RMSE** for delay-duration regression.
- Precision, recall, confusion matrix, and error analysis as supporting diagnostics.
- Local inference latency benchmark for the selected model.

No model scores are included here because they must be calculated from the final trained model and test split.

## API Design

The proposed FastAPI service includes:

| Endpoint | Method | Purpose |
|---|---|---|
| `/predict` | `POST` | Return delay-risk probability and predicted delay duration |
| `/health` | `GET` | Report service health and whether the model is loaded |
| `/metadata` | `GET` | Return model version, feature version, and training metadata |

Illustrative response shape (exact fields depend on the implementation):

```json
{
  "risk_probability": 0.72,
  "risk_label": "high",
  "predicted_delay_minutes": 24.5,
  "model_version": "v1"
}
```

The values above are examples, not real model predictions.

## Getting Started

The commands below are generic examples. Update module paths and filenames to match the actual repository.

### Prerequisites
- Python 3.10 or a compatible version supported by the chosen dependencies.
- Git.
- Access to the required datasets.
- Optional: ONNX Runtime if ONNX inference is implemented.

### 1. Clone the repository

```bash
git clone https://github.com/Shouryaaa-SB/miniproject_aviationanalysis.git
cd miniproject_aviationanalysis
```

### 2. Create a virtual environment

**Windows PowerShell**
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

If the repository includes `requirements.txt`:

```bash
pip install -r requirements.txt
```

Otherwise, a possible starting set is:

```bash
pip install pandas numpy scikit-learn xgboost lightgbm matplotlib seaborn fastapi uvicorn streamlit
```

Install ONNX Runtime only if needed:

```bash
pip install onnxruntime
```

### 4. Prepare the data

Download the required data from the sources above and place it in the location expected by the implemented pipeline. Do not assume a folder name until the repository defines it. Keep large/raw data files out of Git unless licensing and repository policy permit them.

### 5. Run the API and dashboard

Example API command (requires an `app/main.py` module or equivalent):

```bash
uvicorn app.main:app --reload
```

API documentation, if enabled, is usually available at:

```text
http://127.0.0.1:8000/docs
```

Example dashboard command (requires `dashboard.py` or equivalent):

```bash
streamlit run dashboard.py
```

These commands must be adjusted to match the actual files in the repository.

## Suggested Project Structure

This is a suggested layout, not a claim about the current repository files.

```text
miniproject_aviationanalysis/
├── data/
│   ├── raw/                  # Downloaded source data (usually git-ignored)
│   └── processed/            # Cleaned/merged data (usually git-ignored)
├── notebooks/                # EDA and experiments
├── src/
│   ├── data/                 # Loading, cleaning, joining
│   ├── features/             # Feature engineering
│   ├── models/               # Training and evaluation
│   └── inference/             # Shared prediction pipeline
├── app/
│   └── main.py               # FastAPI entry point (suggested)
├── dashboard.py              # Streamlit entry point (suggested)
├── models/                   # Saved model artifacts
├── tests/
├── requirements.txt
├── .gitignore
└── README.md
```

## Evaluation

| Task | Main metric | Supporting analysis |
|---|---|---|
| Delay-risk classification | ROC-AUC | Precision, recall, confusion matrix |
| Delay-duration regression | MAE, RMSE | Residual/error analysis |
| Inference performance | Local latency | Hardware and test conditions |
| Data pipeline | Validation checks | Missingness, duplicates, join coverage, leakage checks |

## Target Users and Use Cases

### Primary users
- Airline dispatch and operations-control teams.
- Airport operations and capacity-planning analysts.
- Aviation analytics teams.

### Secondary users
- Travel-management companies.
- Aviation software providers.
- Researchers and students working on aviation analytics.

### Example use cases
- Prioritize flights with higher predicted delay risk.
- Explore how weather and congestion relate to delay patterns.
- Estimate likely delay duration for operational planning.
- Provide structured predictions to another dashboard or service.

## Limitations

- The cited primary dataset is historical and U.S.-based; it does not provide current real-time flight conditions by itself.
- Weather and airport traffic sources require compatible timestamps, airport identifiers, and appropriate usage rights.
- Prediction quality depends on data quality, feature availability, validation strategy, and model performance.
- The sub-50 ms inference target must be benchmarked; it is not guaranteed.
- This is an academic decision-support prototype and must not be used as the sole basis for safety-critical aviation decisions.

## Future Scope

- Add newer multi-year flight records.
- Integrate licensed or openly available live weather and aviation feeds.
- Add explainability methods such as SHAP.
- Investigate cascading/network delays with temporal or graph-based models.
- Produce uncertainty intervals and scenario analysis.
- Add alerting and operational recommendations with human review.
- Containerize services and deploy them to a suitable cloud environment.

## Team

**Team 19 — Team Vortex**  
**Institute:** B.Tech (CS AIML), GLA University, Mathura

| Member | Role |
|---|---|
| Shourya Bansal | Project Lead |
| Shivam Khandelwal | ML/Data Lead |
| Piyush Yadav | Backend/Deployment |
| Shraddha Sharma | Dashboard/QA |

## References

1. **Kaggle / U.S. DOT — 2015 Flight Delays and Cancellations:** https://www.kaggle.com/usdot/flight-delays/data
2. **U.S. Bureau of Transportation Statistics — TranStats:** https://transtats.bts.gov/
3. **FAA — Weather Delay FAQ:** https://www.faa.gov/nextgen/programs/weather/faq
4. **FAA — Traffic Management Overview:** https://www.faa.gov/nextgen/programs/weather/tfm_support/overview
5. **FlightAware:** https://www.flightaware.com/about/
6. **Cirium Aviation APIs:** https://www.cirium.com/data/aviation-api/
7. **Cirium Aviation Data:** https://www.cirium.com/data/aviation-data/
8. **OAG:** https://www.oag.com/
9. **XGBoost documentation:** https://xgboost.readthedocs.io/
10. **LightGBM documentation:** https://lightgbm.readthedocs.io/
11. **scikit-learn documentation:** https://scikit-learn.org/stable/
12. **FastAPI documentation:** https://fastapi.tiangolo.com/
13. **Streamlit documentation:** https://docs.streamlit.io/
14. **ONNX Runtime documentation:** https://onnxruntime.ai/docs/
15. **Systematic review of machine-learning flight-delay prediction:** https://doi.org/10.1016/j.asoc.2026.115761
16. **Aircraft ground-handling and TOBT prediction research:** https://doi.org/10.1016/j.trpro.2023.12.009

---

**Team Vortex | Project 131 | Predictive Aviation Analytics & Flight Delay Mitigation Engine**
