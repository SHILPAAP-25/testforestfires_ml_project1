
# 🔥 Forest Fire Prediction App (FWI)

![Python](https://img.shields.io/badge/Python-3.x-blue) ![Flask](https://img.shields.io/badge/Flask-Web%20App-black) ![scikit-learn](https://img.shields.io/badge/scikit--learn-Ridge%20Regression-orange) ![AWS](https://img.shields.io/badge/AWS-Elastic%20Beanstalk-yellow) ![CI/CD](https://img.shields.io/badge/CI%2FCD-CodePipeline-green)

An end-to-end machine learning project: exploratory analysis and model training on the Algerian Forest Fires dataset, served through a Flask web app and deployed with a CI/CD pipeline (GitHub → AWS CodePipeline → AWS Elastic Beanstalk).

🔗 **Live demo:** [testforestfires-ml-project1.onrender.com/predict_datapoint](https://testforestfires-ml-project1.onrender.com/predict_datapoint)
*(hosted on a free plan, so the first load may take 30–60 seconds)*

---

## Problem Statement

Forest fires cause major environmental and economic damage, and early risk estimation helps authorities prepare. This project uses weather conditions and fire-weather indices to predict the **Fire Weather Index (FWI)**, a standard measure of fire intensity, and serves the model through a simple web interface so anyone can enter values and get an instant prediction.

## Highlights

- Complete ML workflow: EDA → feature engineering → model comparison → deployment
- Compared **Ridge, Lasso and ElasticNet** regression; Ridge is used in the deployed app
- The same `StandardScaler` used in training is saved and reused at inference, so there is no train/serve mismatch
- Automated CI/CD on AWS: every push to GitHub triggers a new deployment on Elastic Beanstalk
- Also deployed on Render for a free, always-available live demo

## Results

**Data and preprocessing:** Algerian Forest Fires dataset, 243 records (182 train / 61 test, 75/25 split). Dropped day/month/year, removed highly correlated features (BUI and DC, correlation > 0.85) to reduce multicollinearity, and scaled the remaining 9 features with `StandardScaler`. Target: FWI.

| Model | R² | MAE |
|---|---|---|
| Linear Regression | 0.985 | 0.547 |
| **Ridge (deployed)** | **0.984** | **0.564** |
| Lasso (cross-validated) | 0.982 | 0.620 |
| ElasticNet (cross-validated) | 0.981 | 0.658 |
| Lasso (default) | 0.949 | 1.133 |
| ElasticNet (default) | 0.875 | 1.882 |

Regularized models reach R² ≈ 0.98 on the held-out test set. The deployed app uses Ridge regression (R² 0.984, MAE 0.564) with the saved scaler.

## System Architecture

```mermaid
flowchart LR
    A[Developer pushes to GitHub] --> B[AWS CodePipeline]
    B --> C[AWS Elastic Beanstalk]
    C --> D[Flask app]
    D --> E[StandardScaler]
    E --> F[Ridge model]
    F --> G[FWI prediction shown to user]
```

## Input Features

| Feature | Description |
|---|---|
| Temperature | Temperature |
| RH | Relative humidity |
| Ws | Wind speed |
| Rain | Rainfall |
| FFMC | Fine Fuel Moisture Code |
| DMC | Duff Moisture Code |
| ISI | Initial Spread Index |
| Classes | Fire / not-fire class |
| Region | Region of the data |

**Output:** predicted Fire Weather Index (FWI)

## Tech Stack

| Area | Tools |
|---|---|
| Language | Python |
| ML | Scikit-learn (Ridge, Lasso, ElasticNet, StandardScaler), Pandas, NumPy |
| Web | Flask, HTML templates, Gunicorn |
| Cloud / DevOps | AWS Elastic Beanstalk, AWS CodePipeline, Render, Git/GitHub |

## Project Structure

```
.ebextensions/      # Elastic Beanstalk configuration (python.config)
models/             # ridge.pkl, regression.pkl, scaler.pkl
notebooks/
  ├── Algerian_forest_fires_dataset_UPDATE.csv
  ├── proj4_ridge_lasso_elastic_EDA_featureEng.ipynb   # EDA + feature engineering
  └── model_training_algerian_forest.ipynb             # model training and evaluation
templates/          # index.html, home.html
application.py      # Flask app
requirements.txt    # dependencies
```

## Getting Started

**Prerequisites:** Python 3.8+ and pip

```bash
# 1. Clone the repository
git clone https://github.com/SHILPAAP-25/testforestfires_ml_project1.git
cd testforestfires_ml_project1

# 2. (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the app
python application.py
```

Open `http://127.0.0.1:5000/predict_datapoint`, enter the values, and click **Predict**.

## Routes

| Route | Method | Description |
|---|---|---|
| `/` | GET | Landing page |
| `/predict_datapoint` | GET, POST | Prediction form (GET) and FWI result (POST) |

## Deployment

**AWS (CI/CD):**
1. Code is pushed to the GitHub `main` branch.
2. AWS CodePipeline detects the change and pulls the source.
3. The pipeline deploys the new version to AWS Elastic Beanstalk (region `ap-south-1`).
4. Elastic Beanstalk runs `application.py` using `.ebextensions/python.config`.

**Render (live demo):** deployed as a Web Service from this repository with start command `gunicorn application:application`.

## Limitations

- Trained on a small regional dataset (Algerian forests), so predictions may not generalize to other regions or climates.
- Minimal input validation; invalid values are not rejected with friendly error messages.
- No automated tests yet.

## Future Improvements

- Add input validation and clear error handling
- Add unit tests and a GitHub Actions test step before deployment
- Containerize with Docker
- Track experiments with MLflow
- Retrain and evaluate on data from more regions

## Author

**Shilpa A P**
[GitHub](https://github.com/SHILPAAP-25) · [LinkedIn](https://linkedin.com/in/shilpa-a-p-879ba1389)
