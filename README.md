

## Anomaly Detection

## Objective
This project demonstrates approaches to anomaly detection and reasoning on infrastructure-level metrics, as part of the SentinelOps internship assignment. The focus is on AI/ML fundamentals, reasoning, and clear explanations, not DevOps or cloud expertise.

## Problem Statement
Given metrics for a set of resources, the goal is to:
- Identify whether a resource is anomalous or inefficient
- Explain the reasoning
- Suggest a reasonable action
- Assign a confidence score (0–1)
- (Optional) Highlight any potential security concerns

## Input Data (Sample)
```
[
  {
    "resource_id": "i-1",
    "cpu_avg": 2,
    "cpu_p95": 5,
    "memory_avg": 70,
    "network_pct": 10,
    "internet_facing": true,
    "identity_attached": true
  },
  {
    "resource_id": "i-2",
    "cpu_avg": 85,
    "cpu_p95": 98,
    "memory_avg": 40,
    "network_pct": 60,
    "internet_facing": false,
    "identity_attached": false
  }
]
```


## Expected Output Format
```
{
  "resource_id": "i-1",
  "is_anomalous": true,
  "anomaly_type": "over_provisioned",
  "reason": "Very low CPU usage with high memory usage suggests inefficient resource utilization",
  "suggested_action": "Consider downsizing instance",
  "confidence": 0.78,
  "security_note": "Internet-facing resource with identity attached may introduce risk"
}
```
## Approach Implemented: Machine Learning Model

## Model Details

This solution uses a Random Forest Classifier from scikit-learn for anomaly detection and classification. The model is trained on synthetic infrastructure metrics generated to simulate real-world resource usage patterns.

**Features Used:**
- cpu_avg: Average CPU usage
- cpu_p95: 95th percentile CPU usage
- memory_avg: Average memory usage
- network_pct: Network utilization percentage
- internet_facing: Whether the resource is internet-facing (0/1)
- identity_attached: Whether an identity is attached (0/1)

**Model Parameters:**
- n_estimators: 100 (number of trees in the forest)
- Default settings for other RandomForestClassifier parameters

**How It Works:**
1. Data is generated or loaded, and features are extracted.
2. Labels are assigned using simple rules for anomaly types (over_provisioned, cpu_spike, network_anomaly, normal).
3. Features are scaled using StandardScaler.
4. The Random Forest model is trained to predict whether a resource is anomalous and to classify the anomaly type.
5. For new inputs, the model predicts anomaly status, type, confidence, and suggests actions and security notes based on the input features.



### Why Machine Learning?
- **Best for Complex Patterns:** ML models can capture subtle and complex relationships in data that rule-based or heuristic approaches often miss.
- **Scalability:** Once trained, the model can efficiently process large volumes of resource data without manual rule updates.
- **Adaptability:** The model can be retrained with new data, allowing it to adapt to evolving infrastructure behaviors and new types of anomalies.
- **Generalization:** ML can generalize from examples, making it robust to noise and imperfect signals in real-world data.
### Tradeoffs of the ML Approach
- **Strengths:**
  - Captures complex, non-linear relationships in data
  - Scalable to large datasets and adaptable to new data
  - Reduces manual rule maintenance
  - Provides consistent, automated outputs
- **Limitations:**
  - Requires sufficient and representative training data
  - May be less interpretable than simple rules (though feature importance helps)
  - Needs periodic retraining as data patterns change
  - Can be sensitive to data quality and feature selection

### Benefits of the ML Approach
- **Accuracy:** Random Forests are powerful classifiers that combine multiple decision trees, improving prediction accuracy and reducing overfitting.
- **Automation:** Reduces the need for manual rule creation and maintenance.
- **Consistent Output:** Produces structured, machine-readable outputs suitable for downstream automation or reporting.
- **Explainability:** Feature importance can be extracted to understand which metrics drive decisions, supporting transparency.

### Why Not Rule-Based or LLM Approaches?
 - **Rule-based** methods are simple but struggle with complex or ambiguous cases and require frequent manual updates.
 - **LLM-based** approaches (e.g., GPT) provide rich explanations but are dependent on external APIs, can be costly, and may introduce variability in outputs. ML models, in contrast, are deterministic and cost-effective once deployed.


## Improvements with More Time

- **Use Real Infrastructure Data:**
  Replace synthetic data with real-world metrics so the model learns from actual usage patterns, making predictions more relevant and robust.

- **Feature Engineering:**
  Add new features (e.g., time-based trends, resource types, user activity) to help the model detect subtler anomalies and improve accuracy.

- **Model Optimization:**
  Tune hyperparameters and experiment with different algorithms (like XGBoost, LightGBM) to maximize performance and reduce false positives/negatives.

-- **Results Visualization Dashboard:**
  Build a simple dashboard to visualize predictions, feature importances, and flagged anomalies, helping users quickly interpret and act on results.


## How to Run
1. Install dependencies (see below).
2. Open `model.ipynb` in Jupyter or VS Code.
3. Run all cells. The notebook will generate data, train models, and print sample outputs for each approach.

### Dependencies
- Python 3.8+
- pandas
- numpy
- scikit-learn
- openai (for LLM-based approach)

Install with:
```bash
pip install pandas numpy scikit-learn openai
```

## Sample Outputs
The notebook prints outputs for at least 5 test cases, e.g.:
```
{
  "resource_id": "t1",
  "is_anomalous": true,
  "anomaly_type": "over_provisioned",
  "reason": "Low CPU but high memory usage",
  "suggested_action": "Downsize instance",
  "confidence": 0.84,
  "security_note": "Internet-facing resource with identity attached may introduce risk"
}
{
  "resource_id": "t2",
  "is_anomalous": true,
  "anomaly_type": "cpu_spike",
  "reason": "CPU usage consistently very high",
  "suggested_action": "Scale up or load balance",
  "confidence": 0.97,
  "security_note": null
}
{
  "resource_id": "t3",
  "is_anomalous": false,
  "anomaly_type": "normal",
  "reason": "Usage within normal range",
  "suggested_action": "No action needed",
  "confidence": 1.0,
  "security_note": null
}
{
  "resource_id": "t4",
  "is_anomalous": true,
  "anomaly_type": "network_anomaly",
  "reason": "High network utilization",
  "suggested_action": "Check traffic patterns or scaling",
  "confidence": 0.98,
  "security_note": "Internet-facing resource with identity attached may introduce risk"
}
{
  "resource_id": "t5",
  "is_anomalous": false,
  "anomaly_type": "normal",
  "reason": "Usage within normal range",
  "suggested_action": "No action needed",
  "confidence": 0.99,
  "security_note": null
}
```


