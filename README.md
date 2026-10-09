# Predictive Aviation Analytics & Flight Delay Mitigation Engine

**Project 131 | Team 19 — Team Vortex | B.Tech (CS AIML), GLA University, Mathura**  
**Project phase:** Planning and proposal — as of 9 October 2026

This repository is for an academic project that is currently in its **early planning phase**. The team has a proposed problem statement, objectives, methodology, architecture, and implementation plan. **The final application, trained models, API, and dashboard have not yet been completed.** This README describes the intended work, not finished functionality.

## Project Overview

Flight delays can be caused or worsened by changing weather, airport congestion, and network-wide operational effects. Static schedules alone cannot capture all changing conditions.

The planned project will investigate whether historical flight-performance data, weather observations, and airport traffic/congestion features can help predict:

1. The probability that a flight will be delayed by **more than 15 minutes**.
2. An estimated delay duration in minutes.

The intended output is a decision-support prototype for aviation operations analysis. It will not make autonomous flight-dispatch or safety-critical decisions.

## Problem Statement

Static flight schedules frequently fail to account for dynamic meteorological fluctuations and airport congestion bottlenecks. These disruptions can lead to operational inefficiencies for airline carriers and uncertainty for passengers.

The project proposes a localized analytics system that combines historical flight records with weather and traffic/congestion information to estimate delay risk before it escalates.

## Planned Objectives

- Study and prepare a suitable historical flight dataset.
- Investigate the availability and compatibility of weather and airport traffic data.
- Clean and align data using airport identifiers and timestamps.
- Engineer temporal, carrier, route, weather, and congestion-related features.
- Develop a classification model for delays exceeding 15 minutes.
- Develop a regression model to estimate delay duration.
- Evaluate candidate models using ROC-AUC, MAE, and RMSE.
- Plan a FastAPI service for prediction requests.
- Plan a Streamlit dashboard for risk and context visualization.
- Investigate ONNX Runtime as a possible model-inference optimization.

These are planned objectives; no model performance or completed feature is claimed at this stage.

## Proposed Methodology

### Phase 1 — Data Research and Preparation
- Review the historical flight-delay dataset.
- Identify suitable weather and traffic/congestion data sources.
- Check data quality, licensing, missing values, duplicates, and timestamp alignment.
- Decide the exact delay target to predict (departure delay, arrival delay, or another defined measure).

### Phase 2 — Exploratory Analysis and Feature Engineering
- Explore delay patterns by time, carrier, airport, and route.
- Derive scheduled departure hour, day of week, and other temporal features.
- Investigate weather variables such as visibility, wind speed, and precipitation.
- Explore rolling airport traffic/congestion indicators if appropriate data is available.
- Avoid using information that would not be available at prediction time.

### Phase 3 — Model Development (Planned)
- Establish a simple baseline.
- Compare candidate XGBoost and/or LightGBM classification models.
- Develop a regression model for delay duration.
- Use appropriate train/validation/test splits, with time-aware validation where feasible.
- Evaluate using ROC-AUC for classification and MAE/RMSE for regression.

### Phase 4 — API and Dashboard (Planned)
- Design a FastAPI REST endpoint for structured predictions.
- Design a Streamlit dashboard showing predicted risk, estimated delay duration, and relevant context.
- Test API inputs, output schemas, error handling, and dashboard flows.

### Phase 5 — Optimization and Demonstration (Future)
- Benchmark local inference latency.
- Explore ONNX conversion if compatible with the selected model.
- Document limitations, evaluation results, and possible future improvements.

## Proposed Features

The following are planned features, not claims of completed functionality:

- Delay-risk probability for delays exceeding 15 minutes.
- Estimated delay duration in minutes.
- Weather-context analysis, subject to data availability.
- Airport traffic/congestion indicators, subject to data availability.
- Temporal, carrier, airport, and route feature engineering.
- Streamlit risk dashboard and visual summaries.
- FastAPI prediction endpoint.
- Structured operational summary.
- Possible ONNX Runtime optimization after a model is developed.

## Proposed Architecture

```text
Historical Flight Records ─┐
Weather Data (to be sourced) ┼──> Data Validation and Cleaning
Airport Traffic Data (TBD) ─┘                |
                                             v
                                  Timestamp/Airport Alignment
                                             |
                                             v
                                      Feature Engineering
                                             |
                                             v
                             Classification + Regression Models
                                             |
                                             v
                                  Model Evaluation and Saving
                                             |
                                             v
                              FastAPI Service (planned)
                                             |
                                             v
                            Streamlit Dashboard (planned)
```

## Proposed Technology Stack

| Area | Planned technology |
|---|---|
| Programming language | Python |
| Data processing | pandas, NumPy |
| Machine learning | scikit-learn, XGBoost and/or LightGBM |
| Visualization | Matplotlib, Seaborn, Streamlit |
| API | FastAPI, Uvicorn |
| Model optimization | ONNX Runtime, subject to model compatibility |
| Testing | pytest and/or Postman |
| Data sources | Historical flight data, compatible weather and airport traffic sources |

The final stack may change after data exploration and implementation decisions.

## Planned Data Sources

### Primary dataset under consideration
- **2015 Flight Delays and Cancellations — Kaggle / U.S. DOT:**  
  https://www.kaggle.com/usdot/flight-delays/data

### Aviation statistics
- **U.S. Bureau of Transportation Statistics — TranStats:**  
  https://transtats.bts.gov/

### Data still to be finalized
The proposed project needs compatible weather observations (for example, visibility, wind speed, and precipitation) and airport traffic/congestion indicators. Specific datasets or APIs for these additional inputs have **not yet been finalized**.

**Important geographic note:** The cited 2015 Kaggle dataset is U.S.-based and historical. The team must confirm the intended geographic scope and data sources before implementation; this README does not claim that India-specific or live flight data has been secured.

Do not commit API keys, credentials, or large raw datasets to GitHub. Check data usage terms before downloading or redistributing any source.

## Planned Machine Learning Approach

### Target variables
- **Classification target:** whether the chosen flight delay measure exceeds 15 minutes.
- **Regression target:** delay duration in minutes.

The team still needs to define the precise delay field and prediction time consistently. Feature selection must avoid target leakage from actual outcomes or future observations.

### Candidate features
- Scheduled departure hour and day of week.
- Carrier identifier.
- Origin/destination airport and route.
- Weather conditions aligned to airport and time.
- Airport traffic/congestion metrics, if a suitable source is found.

### Candidate models
- A simple baseline model.
- XGBoost and/or LightGBM for classification.
- A regression model for delay-duration estimation.

### Planned evaluation
- **ROC-AUC** for classification.
- **MAE** and **RMSE** for regression.
- Precision, recall, confusion matrix, and error analysis as supporting measures.
- Local inference latency measurement if a working API/model is produced.

No trained model, benchmark, or prediction accuracy is claimed yet. Results will be added after experiments are conducted.

## Planned API Design

The following endpoints are proposed and may change during implementation.

| Endpoint | Method | Intended purpose |
|---|---|---|
| `/predict` | `POST` | Return delay-risk probability and estimated delay duration |
| `/health` | `GET` | Report service health and whether a model is loaded |
| `/metadata` | `GET` | Return model and feature metadata |

Illustrative response shape only:

```json
{
  "risk_probability": 0.72,
  "risk_label": "high",
  "predicted_delay_minutes": 24.5,
  "model_version": "v1"
}
```

The values above are placeholders to show a possible response format. They are **not predictions from a trained model**.

## Planned Dashboard

The proposed Streamlit dashboard may include:
- Delay-risk probability and risk category.
- Estimated delay duration.
- Weather and congestion context, if integrated data supports it.
- Charts showing delay patterns and trends.
- A structured summary for operational analysis.

The dashboard has not yet been completed.

## Early-Phase Work Plan

The current plan is to proceed through these stages:

1. Confirm scope, data availability, and the exact prediction target.
2. Profile and clean the historical flight dataset.
3. Research and assess compatible weather and traffic/congestion sources.
4. Perform exploratory data analysis and develop candidate features.
5. Establish baseline classification and regression models.
6. Compare models and document evaluation results.
7. Develop the API and dashboard after the data/model pipeline is stable.
8. Test, document limitations, and prepare the project demonstration.

Timeline and task ownership should be updated by the team as work progresses.

## Suggested Repository Structure

This is a proposed structure, not a description of files already implemented.

```text
miniproject_aviationanalysis/
├── data/
│   ├── raw/                  # Downloaded data; normally git-ignored
│   └── processed/            # Cleaned/merged data; normally git-ignored
├── notebooks/                # Planned EDA and experiments
├── src/
│   ├── data/                 # Planned loading and cleaning
│   ├── features/             # Planned feature engineering
│   ├── models/               # Planned training and evaluation
│   └── inference/            # Planned shared prediction pipeline
├── app/                      # Planned FastAPI service
├── dashboard.py              # Planned Streamlit entry point
├── models/                   # Future saved model artifacts
├── tests/                    # Planned tests
├── requirements.txt          # Add when dependencies are decided
└── README.md
```

## Target Users and Potential Use Cases

### Intended users
- Airline dispatch and operations-control teams.
- Airport operations and capacity-planning analysts.
- Aviation analytics teams.
- Researchers and students working on aviation analytics.

### Potential use cases
- Prioritize flights with higher predicted delay risk.
- Explore how weather and congestion relate to delay patterns.
- Estimate possible delay duration for operational planning.
- Provide structured predictions to another application or dashboard.

These are intended use cases, not validated outcomes.

## Limitations and Open Questions

- The primary dataset under consideration is historical and U.S.-based.
- Weather and traffic data sources have not yet been finalized.
- The precise delay target and prediction-time assumptions need to be confirmed.
- Model performance is unknown until training and evaluation are completed.
- The proposed sub-50 ms inference target is aspirational and must be benchmarked.
- Live data, ONNX optimization, API deployment, and dashboard features are not yet confirmed as implemented.
- This is intended as an academic decision-support prototype, not a safety-critical aviation system.

## Future Scope

Potential future work, after the initial pipeline is validated:
- Add newer multi-year flight records.
- Integrate appropriate live weather or aviation feeds where access and licensing permit.
- Add explainability methods such as SHAP.
- Investigate cascading/network delays with temporal or graph-based models.
- Produce uncertainty estimates and scenario analysis.
- Add alerts and operational recommendations with human review.
- Containerize services and consider cloud deployment.

## Team

**Team 19 — Team Vortex**  
**Institute:** B.Tech (CS AIML), GLA University, Mathura

| Member | Planned role |
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

**Team Vortex | Project 131 | Planning-phase README — 9 October 2026**
