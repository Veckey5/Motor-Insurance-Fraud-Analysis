<h1 align="center">🚘 Motor Insurance Fraud Analysis</h1>

<p align="center">
  <i>Finding fraud in hundreds of thousands of car insurance policies, and showing what it costs.</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/pandas-150458?style=for-the-badge&logo=pandas&logoColor=white"/>
  <img src="https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white"/>
  <img src="https://img.shields.io/badge/Matplotlib-11557c?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white"/>
</p>

## The Problem

This project asks two questions:

1. How much does fraud hurt the company's finances?
2. How well is the company catching it, and how could it do better?

## The Headline Results

| | |
|---|---|
| **4.8%** | of policies were fraudulent |
| **~39%** | of total claims cost came from those policies |
| **602%** | loss ratio on fraud policies, against 50% on genuine ones (anything over 100% is a loss) |
| **12x** | how much more often fraudulent policies make a claim |
| **73%** | of investigations ended in confirmed fraud |

Summarily, fraud is under 5% of customers, but it drives close to 40% of claims costs. Those customers are not noticeably different in how much each claim costs. They simply claim far more often.

## My Analytical Approach

- **Cleaned messy data:** fixed mixed date formats, missing values and coded fields.
- **Measured the business impact:** loss ratio, claim frequency, claim cost and investigation success rate.
- **Tracked fraud over time:** monthly trends and seasonality, which showed fraud declining across the period.
- **Found who is most at risk:** payment type, vehicle age, driver age, vehicle value and region stood out.
- **Built two machine learning models** (Decision Tree and XGBoost) to predict fraud, using SHAP to explain which factors drive the predictions.
- **Proposed a new tool, CoverCheck,** to help customers spot "ghost broking" scams (fake brokers selling invalid policies).

## Recommendations

1. Use the risk factors to prioritize which policies get investigated.
2. Fix inconsistencies in how fraud and investigation are recorded.
3. Track fraud KPIs monthly to catch spikes early.
4. Improve the model with better tuning, validation and extra features.
5. Pilot CoverCheck on a small scale.

Full evaluation and reasoning are in **[REPORT.md](REPORT.md)**.

## Tools Used

- **Data analysis and cleaning:** Python, pandas, NumPy
- **Visualisation:** Matplotlib, Seaborn
- **Statistics and time series:** statsmodels, seasonal decomposition
- **Machine learning:** scikit-learn, XGBoost, SHAP, handling imbalanced data

## Repository

| File | What it is |
|---|---|
| `Fraud_Task.ipynb` | The full analysis, step by step, with comments |
| `REPORT.md` | Findings, evaluation and recommendations |
| `Data_Task.xlsx` | The dataset |

## Run It Yourself

```bash
pip install pandas numpy matplotlib seaborn openpyxl statsmodels scikit-learn imbalanced-learn xgboost shap jupyter
jupyter notebook Fraud_Task.ipynb
```
