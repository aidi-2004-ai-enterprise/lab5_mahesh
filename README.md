# lab5_mahesh
# Main Challenges and Solutions
The project's two primary challenges were the highly imbalanced dataset and multicollinearity among the features.

Class Imbalance: Our dataset had very few examples of bankruptcies, which could cause a model to become biased and fail to detect the minority class. To overcome this, we used class weighting (class_weight='balanced') in our Logistic Regression and Random Forest models. This forces the models to prioritize the correct classification of the rare bankruptcy cases.

Multicollinearity: Many financial ratios in the dataset were highly correlated. This can lead to model instability, particularly for linear models. Our solution was to implement a simple correlation-based feature selection filter that systematically removed one feature from any highly correlated pair, thus stabilizing the models and improving performance.

# Influence of Lab 4 Research
The research conducted in Lab 4 directly guided the strategic decisions made in this implementation.

Model Selection: The choice of three distinct models—Logistic Regression (as a benchmark), Random Forest, and Gaussian Naive Bayes—was a direct result of understanding their different strengths and weaknesses.

Evaluation Strategy: The emphasis on using robust metrics like ROC-AUC and Brier Score for evaluation was a key takeaway from the research on imbalanced data. The implementation also included a Population Stability Index (PSI) check, as researched, to proactively monitor for data drift.

# Recommended Model for Deployment
Based on a comprehensive evaluation, the Random Forest model is recommended for deployment. The primary reasons for this choice are:

Superior Performance: The Random Forest achieved the highest test ROC-AUC and a strong Brier Score, indicating it has the best predictive power and most reliable probability outputs among the models tested.

Robustness: The model demonstrated consistent performance across both the training and test sets, showing strong generalization and minimal signs of overfitting.

Interpretability: While a more complex model, its predictions are highly interpretable through SHAP values. These insights are crucial for explaining the "why" behind the predictions to stakeholders and for meeting regulatory requirements.
