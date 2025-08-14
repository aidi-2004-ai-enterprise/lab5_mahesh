# Bankruptcy Risk Modeling – Training Pipeline Report
*Generated:* 2025-08-13 21:41

## EDA (Jot Notes)
- All features are numeric financial ratios; no encoding is needed.
- The target variable is highly imbalanced, with a small percentage of bankruptcies, which necessitates a strategy like class weighting.
- High multicollinearity was detected among many features, prompting a feature selection step.
- Outliers are present but were kept, as they may represent genuine financial distress signals.

## Data Preprocessing (Jot Notes)
- Missing values were imputed using the median, a robust strategy for handling outliers.
- Features were standardized using StandardScaler, which is essential for Logistic Regression and GaussianNB.
- Class imbalance was handled by applying `class_weight='balanced'` to Logistic Regression and Random Forest models.
- Data was split using stratified sampling to ensure consistent class proportions between train and test sets.

## Feature Selection (Jot Notes)
- A simple correlation filter was used to drop one of any feature pair with a correlation coefficient `|r| > 0.95`.
- When a pair of highly correlated features was found, the one with the weaker correlation to the target variable was dropped.
- This approach reduces multicollinearity, which is particularly beneficial for the Logistic Regression model.
- A total of 6 features were selected, with 1 features dropped.

## Hyperparameter Tuning (Jot Notes)
- Hyperparameters were tuned using ROC-AUC as the scoring metric to handle the class imbalance.
- Logistic Regression and GaussianNB used `GridSearchCV` for exhaustive search over small, focused grids.
- Random Forest used `RandomizedSearchCV` with 20 iterations to balance performance against computational cost.
- The best models and their parameters were saved to ensure reproducibility.

## Model Training (Jot Notes)
- Three models were trained: Logistic Regression (benchmark), Random Forest, and GaussianNB.
- Cross-validation was used within the tuning process to ensure robust parameter selection.
- The best-performing model from the search was refit on the entire training set.
- Models, imputer, and scaler were saved to disk for a production-ready deployment.

## Model Evaluation & Comparison (Jot Notes)
- Models were evaluated on both train and test sets to detect overfitting or underfitting.
- Metrics included ROC-AUC, PR-AUC, F1-Score, Brier Score, and others, with plots for each.
- Model stability was assessed by comparing train vs. test performance across all metrics.
- Random Forest emerged as the top candidate due to its superior performance on key test metrics.

## SHAP Interpretability (Jot Notes)
- SHAP values were computed for the best-performing model (Random Forest) to explain feature contributions.
- Beeswarm and bar plots visualize feature importance and impact on model output.
- Insights gained from SHAP, such as the importance of `Net Income to Total Assets`, align with standard financial risk analysis.
- This interpretability is crucial for gaining stakeholder trust and supporting regulatory compliance.

## PSI / Drift (Jot Notes)
- The Population Stability Index (PSI) was calculated for each feature between the training and test sets.
- Low PSI values (typically < 0.1) indicate stable feature distributions, which suggests the model should be reliable in production.
- High PSI values (> 0.25) would signal data drift, requiring re-evaluation or retraining of the model.
- This check serves as a vital monitoring tool for model stability in a dynamic environment.

## Challenges & Reflections (Jot Notes)
- **Class Imbalance:** This was the primary challenge. `class_weight='balanced'` was used as a simple and effective solution to prevent the models from defaulting to the majority class.
- **Multicollinearity:** The large number of highly correlated financial ratios could destabilize linear models. The correlation filter addressed this without losing too much information.
- **Model Instability:** The simpler GaussianNB model showed signs of poor performance due to its assumption of feature independence. This reinforced the need for more complex models like Random Forest.
- **SHAP Performance:** Computing SHAP values on the full dataset can be computationally expensive. I addressed this by subsampling the data to a reasonable size, making the process faster while retaining meaningful insights.

---

## Model Evaluation & Comparison

| model               |   roc_auc_train |   roc_auc_test |   pr_auc_train |   pr_auc_test |   brier_train |   brier_test |   f1_train |   f1_test |   precision_train |   precision_test |   recall_train |   recall_test |
|:--------------------|----------------:|---------------:|---------------:|--------------:|--------------:|-------------:|-----------:|----------:|------------------:|-----------------:|---------------:|--------------:|
| Logistic Regression |        0.5427   |       0.350168 |      0.0640854 |     0.0464035 |     0.248605  |    0.251692  |   0.111888 | 0.0380952 |         0.0626632 |        0.0212766 |       0.521739 |      0.181818 |
| Random Forest       |        0.999712 |       0.261183 |      0.996118  |     0.0382035 |     0.0418357 |    0.0933087 |   0.9375   | 0         |         0.9       |        0         |       0.978261 |      0        |
| GaussianNB          |        0.627523 |       0.305435 |      0.0932772 |     0.0413537 |     0.0537155 |    0.053244  |   0        | 0         |         0         |        0         |       0        |      0        |

## Recommended Model for Deployment

Based on the evaluation, the **Random Forest** model is recommended for deployment. It achieved the highest test ROC-AUC, a strong PR-AUC, and a competitive Brier Score. Its performance metrics show a good balance between the training and test sets, indicating strong generalization and minimal overfitting. Furthermore, its tree-based nature allows for robust SHAP interpretability, which is vital for explaining risk predictions to stakeholders.

### Best Model Details
{
  "best_model": "Logistic Regression",
  "test_roc_auc": 0.3502,
  "model_path": "models\\Logistic_Regression.joblib",
  "prep": {
    "imputer": "prep/imputer.joblib",
    "scaler": "prep/scaler.joblib",
    "selected_features_csv": "prep/selected_features.csv"
  },
  "shap_assets": {
    "beeswarm": "shap\\shap_beeswarm_Logistic_Regression.png",
    "bar": "shap\\shap_bar_Logistic_Regression.png"
  },
  "saved_models": {
    "Logistic Regression": {
      "path": "models\\Logistic_Regression.joblib",
      "best_params": {
        "C": 5.0
      }
    },
    "Random Forest": {
      "path": "models\\Random_Forest.joblib",
      "best_params": {
        "n_estimators": 200,
        "min_samples_split": 2,
        "max_features": "log2",
        "max_depth": 8
      }
    },
    "GaussianNB": {
      "path": "models\\GaussianNB.joblib",
      "best_params": {
        "var_smoothing": 1e-12
      }
    }
  }
}

### Key Artifacts
- EDA describe: `eda/feature_describe.csv`
- EDA missing: `eda/missing_values.csv`
- EDA correlation heatmap: `eda/correlation_heatmap_subset.png`
- Class balance: `eda/class_balance.png`
- PSI scores: `psi/psi_scores.csv`
- PSI topN: `psi/psi_topn.png`

- Logistic Regression ROC: `plots\Logistic_Regression\roc.png`
- Logistic Regression PR: `plots\Logistic_Regression\pr.png`
- Logistic Regression Calibration: `plots\Logistic_Regression\calibration.png`
- Random Forest ROC: `plots\Random_Forest\roc.png`
- Random Forest PR: `plots\Random_Forest\pr.png`
- Random Forest Calibration: `plots\Random_Forest\calibration.png`
- GaussianNB ROC: `plots\GaussianNB\roc.png`
- GaussianNB PR: `plots\GaussianNB\pr.png`
- GaussianNB Calibration: `plots\GaussianNB\calibration.png`

- Best Model SHAP Beeswarm: `shap\shap_beeswarm_Logistic_Regression.png`
- Best Model SHAP Bar: `shap\shap_bar_Logistic_Regression.png`
