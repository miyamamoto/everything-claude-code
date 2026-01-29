---
name: datarobot-patterns
description: DataRobot SDK patterns, best practices, and AutoML workflows for efficient model development and deployment.
---

# DataRobot Patterns and Best Practices

Comprehensive patterns for DataRobot Python SDK, covering AutoML, deployment, predictions, and MLOps.

## Quick Reference

### Installation

```bash
# Install DataRobot SDK
pip install datarobot

# With optional dependencies
pip install datarobot[notebooks]  # Jupyter support
pip install datarobot[complete]   # All optional features
```

### Environment Setup

```bash
# Set environment variables (RECOMMENDED)
export DATAROBOT_API_TOKEN="your-api-token-here"
export DATAROBOT_ENDPOINT="https://app.datarobot.com/api/v2"

# Or create .env file
cat > .env <<EOF
DATAROBOT_API_TOKEN=your-api-token-here
DATAROBOT_ENDPOINT=https://app.datarobot.com/api/v2
EOF
```

## Authentication Patterns

### Secure Authentication

```python
import datarobot as dr
import os
from dotenv import load_dotenv

# ✅ CORRECT: Use environment variables
load_dotenv()

client = dr.Client(
    token=os.getenv('DATAROBOT_API_TOKEN'),
    endpoint=os.getenv('DATAROBOT_ENDPOINT')
)

# Verify connection
user = dr.User.get()
print(f"✓ Connected as: {user.username}")

# ❌ WRONG: Hardcoded credentials
# client = dr.Client(token='sk-proj-xxxxx', endpoint='...')  # NEVER DO THIS
```

### Multi-Environment Configuration

```python
import datarobot as dr
from typing import Literal

Environment = Literal['dev', 'staging', 'prod']

def get_datarobot_client(env: Environment) -> dr.Client:
    """Get DataRobot client for specific environment"""
    endpoints = {
        'dev': os.getenv('DR_ENDPOINT_DEV'),
        'staging': os.getenv('DR_ENDPOINT_STAGING'),
        'prod': os.getenv('DR_ENDPOINT_PROD')
    }

    tokens = {
        'dev': os.getenv('DR_TOKEN_DEV'),
        'staging': os.getenv('DR_TOKEN_STAGING'),
        'prod': os.getenv('DR_TOKEN_PROD')
    }

    return dr.Client(
        token=tokens[env],
        endpoint=endpoints[env]
    )
```

## Project Creation Patterns

### Standard Classification Project

```python
import pandas as pd
import datarobot as dr

def create_classification_project(
    data: pd.DataFrame,
    target: str,
    project_name: str,
    positive_class: str = None,
    metric: str = 'AUC'
) -> dr.Project:
    """
    Create binary/multiclass classification project

    Args:
        data: Training dataset
        target: Target column name
        project_name: Project name
        positive_class: Positive class label (for binary)
        metric: Optimization metric (AUC, LogLoss, F1, etc.)
    """
    project = dr.Project.create(
        sourcedata=data,
        project_name=project_name
    )

    project.analyze_and_model(
        target=target,
        mode=dr.AUTOPILOT_MODE.QUICK,  # or FULL_AUTO
        metric=metric,
        positive_class=positive_class,
        worker_count=-1,  # Use all available workers
        # Advanced partitioning
        partitioning_method=dr.CV(
            holdout_pct=20,
            validation_pct=16,
            reps=5
        )
    )

    return project
```

### Regression Project

```python
def create_regression_project(
    data: pd.DataFrame,
    target: str,
    project_name: str,
    metric: str = 'RMSE'
) -> dr.Project:
    """
    Create regression project

    Common metrics: RMSE, MAE, R Squared, MAPE
    """
    project = dr.Project.create(
        sourcedata=data,
        project_name=project_name
    )

    project.analyze_and_model(
        target=target,
        mode=dr.AUTOPILOT_MODE.FULL_AUTO,
        metric=metric,
        worker_count=-1
    )

    return project
```

### Time Series Project

```python
def create_time_series_project(
    data: pd.DataFrame,
    target: str,
    datetime_column: str,
    forecast_window: int,
    project_name: str,
    known_in_advance: list[str] = None,
    series_id: str = None
) -> dr.Project:
    """
    Create time series forecasting project

    Args:
        data: Time series dataset (must be sorted by datetime)
        target: Target variable to forecast
        datetime_column: Datetime column name
        forecast_window: Number of periods to forecast ahead
        project_name: Project name
        known_in_advance: Features known at prediction time
        series_id: Column for multiseries (None for single series)
    """
    # Ensure data is sorted
    data = data.sort_values(datetime_column)

    project = dr.Project.create(
        sourcedata=data,
        project_name=project_name
    )

    # Configure time series partitioning
    time_spec = dr.DatetimePartitioningSpecification(
        datetime_partition_column=datetime_column,
        use_time_series=True,

        # Forecast window
        forecast_window_start=1,
        forecast_window_end=forecast_window,

        # Feature derivation window (lookback)
        feature_derivation_window_start=-forecast_window * 20,
        feature_derivation_window_end=-1,

        # Known-in-advance features
        known_in_advance_features=known_in_advance or [],

        # Multiseries settings
        multiseries_id_columns=[series_id] if series_id else None,

        # Disable target leakage
        allow_partial_history_time_series_predictions=True
    )

    project.analyze_and_model(
        target=target,
        mode=dr.AUTOPILOT_MODE.FULL_AUTO,
        partitioning_method=time_spec,
        worker_count=-1,
        max_wait=7200  # Time series can take longer
    )

    return project
```

## Model Selection Patterns

### Get Best Model by Metric

```python
def get_best_model(
    project: dr.Project,
    metric: str = None,
    sample_pct_min: float = 64.0
) -> dr.Model:
    """
    Get best performing model from leaderboard

    Args:
        project: DataRobot project
        metric: Metric to optimize (None = project default)
        sample_pct_min: Minimum sample percentage
    """
    project.wait_for_autopilot()

    models = project.get_models()
    metric_name = metric or project.metric

    # Filter by sample percentage
    models = [m for m in models if m.sample_pct >= sample_pct_min]

    # Sort by validation metric
    best_model = min(
        models,
        key=lambda m: m.metrics[metric_name].validation or float('inf')
    )

    return best_model
```

### Get Recommended Models

```python
def get_recommended_models(project: dr.Project) -> list[dr.Model]:
    """Get DataRobot recommended models (starred models)"""
    project.wait_for_autopilot()

    models = project.get_models()
    recommended = [m for m in models if m.is_starred]

    return recommended
```

### Compare Multiple Models

```python
def compare_models_table(
    models: list[dr.Model],
    metrics: list[str] = None
) -> pd.DataFrame:
    """
    Create comparison table for models

    Args:
        models: List of models to compare
        metrics: Metrics to include (None = all)
    """
    rows = []

    for model in models:
        row = {
            'model_id': model.id,
            'model_type': model.model_type,
            'sample_pct': model.sample_pct,
            'is_starred': model.is_starred
        }

        # Add metric scores
        for metric_name, metric_obj in model.metrics.items():
            if metrics is None or metric_name in metrics:
                row[f'{metric_name}_validation'] = metric_obj.validation
                row[f'{metric_name}_holdout'] = metric_obj.holdout

        rows.append(row)

    df = pd.DataFrame(rows)
    return df.sort_values(f'{models[0].project.metric}_validation')
```

## Feature Impact and Insights

### Get Feature Impact

```python
def get_feature_importance(
    model: dr.Model,
    top_n: int = 20
) -> pd.DataFrame:
    """
    Get feature impact for model

    Returns:
        DataFrame with features and impact scores
    """
    # Request feature impact (async job)
    fi_job = model.request_feature_impact()
    feature_impacts = fi_job.get_result_when_complete()

    # Convert to DataFrame
    df = pd.DataFrame([
        {
            'feature': fi.feature_name,
            'impact': fi.impact_normalized,
            'impact_unnormalized': fi.impact_unnormalized
        }
        for fi in feature_impacts
    ])

    return df.sort_values('impact', ascending=False).head(top_n)
```

### Get SHAP Values

```python
def compute_shap_values(
    model: dr.Model,
    data: pd.DataFrame,
    max_rows: int = 1000
) -> pd.DataFrame:
    """
    Compute SHAP values for predictions

    Args:
        model: Trained model
        data: Data to explain
        max_rows: Maximum rows to compute (SHAP is expensive)
    """
    # Limit dataset size
    data_sample = data.head(max_rows)

    # Initialize SHAP
    shap_job = model.request_training_predictions(
        data_subset='validation',
        explanation_algorithm='shap'
    )

    training_predictions = shap_job.get_result_when_complete()

    return training_predictions
```

## Deployment Patterns

### Deploy Model with Monitoring

```python
def deploy_with_monitoring(
    model: dr.Model,
    deployment_name: str,
    enable_drift_tracking: bool = True,
    enable_predictions_monitoring: bool = True
) -> dr.Deployment:
    """
    Deploy model with full monitoring enabled
    """
    # Get default prediction server
    pred_servers = dr.PredictionServer.list()
    default_server = pred_servers[0]

    # Create deployment
    deployment = dr.Deployment.create_from_learning_model(
        model_id=model.id,
        label=deployment_name,
        default_prediction_server_id=default_server.id,
        importance='HIGH'  # or 'LOW', 'MODERATE', 'CRITICAL'
    )

    # Enable monitoring
    if enable_drift_tracking:
        deployment.update_drift_tracking_settings(
            target_drift_enabled=True,
            feature_drift_enabled=True
        )

    if enable_predictions_monitoring:
        deployment.update_predictions_data_collection_settings(
            enabled=True
        )

    return deployment
```

### Champion/Challenger Pattern

```python
def setup_champion_challenger(
    champion_model: dr.Model,
    challenger_model: dr.Model,
    deployment_name: str,
    challenger_traffic_pct: float = 10.0
) -> dr.Deployment:
    """
    Deploy champion model with challenger for A/B testing

    Args:
        champion_model: Current best model (champion)
        challenger_model: New model to test (challenger)
        deployment_name: Deployment name
        challenger_traffic_pct: % of traffic to challenger (0-100)
    """
    # Deploy champion
    deployment = deploy_with_monitoring(
        model=champion_model,
        deployment_name=deployment_name
    )

    # Add challenger
    deployment.validate_replacement_model(challenger_model.id)

    # Configure challenger with traffic split
    # (Note: This requires DataRobot MLOps features)

    print(f"Champion: {champion_model.model_type}")
    print(f"Challenger: {challenger_model.model_type} ({challenger_traffic_pct}% traffic)")

    return deployment
```

## Prediction Patterns

### Batch Predictions with Error Handling

```python
from datarobot.errors import ClientError, AsyncFailureError
import time

def robust_batch_predict(
    deployment: dr.Deployment,
    data: pd.DataFrame,
    max_retries: int = 3
) -> pd.DataFrame:
    """
    Make batch predictions with retry logic

    Args:
        deployment: DataRobot deployment
        data: Input data
        max_retries: Maximum retry attempts
    """
    for attempt in range(max_retries):
        try:
            job = dr.BatchPredictionJob.score(
                deployment=deployment.id,
                intake_settings={
                    'type': 'dataset',
                    'dataset': data
                },
                output_settings={
                    'type': 'json'
                },
                max_explanations=0,  # Set > 0 for prediction explanations
                # Optional threshold for classification
                # threshold=0.5
            )

            # Wait for completion (with timeout)
            job.wait_for_completion(max_wait=3600)

            # Get results
            predictions = job.get_result_when_complete()
            return pd.DataFrame(predictions)

        except AsyncFailureError as e:
            print(f"Batch prediction failed (attempt {attempt + 1}): {e}")
            if attempt < max_retries - 1:
                time.sleep(30)
            else:
                raise

        except ClientError as e:
            print(f"Client error: {e}")
            raise

    raise RuntimeError("Batch prediction failed after all retries")
```

### Real-Time Predictions

```python
def predict_realtime(
    deployment: dr.Deployment,
    records: list[dict] | pd.DataFrame,
    with_explanations: bool = False
) -> list[dict]:
    """
    Make real-time predictions via API

    Args:
        deployment: DataRobot deployment
        records: Records to score
        with_explanations: Include prediction explanations
    """
    # Convert DataFrame to records
    if isinstance(records, pd.DataFrame):
        records = records.to_dict('records')

    # Make predictions
    predictions = deployment.predict(
        records,
        max_explanations=3 if with_explanations else 0
    )

    return predictions
```

## MLOps Patterns

### Submit Actuals for Monitoring

```python
def submit_actual_outcomes(
    deployment: dr.Deployment,
    actuals_data: pd.DataFrame,
    association_id_column: str,
    actual_value_column: str
) -> None:
    """
    Submit actual outcomes for model monitoring

    Args:
        deployment: Deployment to monitor
        actuals_data: DataFrame with actual outcomes
        association_id_column: Column linking to predictions
        actual_value_column: Column with actual target values
    """
    # Format actuals
    actuals = []
    for _, row in actuals_data.iterrows():
        actuals.append({
            'association_id': str(row[association_id_column]),
            'actual_value': row[actual_value_column],
            'was_acted_on': True  # Optional: if prediction was acted on
        })

    # Submit to DataRobot
    deployment.submit_actuals(actuals)

    print(f"Submitted {len(actuals)} actual outcomes")
```

### Check Model Performance

```python
def check_deployment_accuracy(
    deployment: dr.Deployment,
    start_time: str = None,
    end_time: str = None
) -> dict:
    """
    Check deployment accuracy metrics

    Args:
        start_time: Start datetime (ISO format)
        end_time: End datetime (ISO format)
    """
    # Get service stats
    stats = deployment.get_service_stats(
        start_time=start_time,
        end_time=end_time
    )

    # Get accuracy metrics (if actuals submitted)
    try:
        accuracy = deployment.get_accuracy()

        return {
            'total_predictions': stats.metrics.get('totalPredictions', 0),
            'accuracy_metrics': accuracy.metrics,
            'actuals_received': accuracy.period.actual_count
        }
    except ClientError:
        return {
            'total_predictions': stats.metrics.get('totalPredictions', 0),
            'accuracy_metrics': None,
            'message': 'No actuals submitted yet'
        }
```

### Automated Retraining

```python
def trigger_retraining_if_needed(
    deployment: dr.Deployment,
    drift_threshold: float = 0.3,
    accuracy_threshold: float = 0.8
) -> dr.Model | None:
    """
    Check if model needs retraining based on drift/accuracy

    Returns:
        New model if retrained, None otherwise
    """
    # Check drift
    drift = check_data_drift(deployment)

    # Check accuracy
    accuracy_info = check_deployment_accuracy(deployment)

    needs_retraining = False

    if drift['drift_detected']:
        max_drift = max(
            [f['drift_score'] for f in drift['drifted_features']],
            default=0
        )
        if max_drift > drift_threshold:
            print(f"⚠️ High drift detected: {max_drift:.3f}")
            needs_retraining = True

    # Additional accuracy checks here...

    if needs_retraining:
        print("🔄 Triggering model retraining...")
        # Implement retraining logic
        # new_model = retrain_model(...)
        # return new_model

    return None
```

## Common Gotchas and Solutions

### ❌ Problem: Random Train-Test Split (Data Leakage)

```python
# ❌ WRONG: Random split for time-series data
project.analyze_and_model(
    target='price',
    partitioning_method=dr.RandomCV()  # LEAKAGE!
)

# ✅ CORRECT: Use datetime partitioning
project.analyze_and_model(
    target='price',
    partitioning_method=dr.DatetimePartitioning(
        datetime_partition_column='date'
    )
)
```

### ❌ Problem: Hardcoded API Keys

```python
# ❌ WRONG
client = dr.Client(token='my-secret-token')

# ✅ CORRECT
client = dr.Client(token=os.getenv('DATAROBOT_API_TOKEN'))
```

### ❌ Problem: Not Waiting for Jobs to Complete

```python
# ❌ WRONG: Job might not be done
job = model.request_feature_impact()
fi = job.get_result()  # Might fail if not ready

# ✅ CORRECT: Wait for completion
job = model.request_feature_impact()
fi = job.get_result_when_complete(max_wait=600)
```

### ❌ Problem: Ignoring Sample Percentage

```python
# ❌ WRONG: Comparing models with different sample %
best = min(models, key=lambda m: m.metrics['RMSE'].validation)

# ✅ CORRECT: Only compare models at same sample %
models_64pct = [m for m in models if m.sample_pct == 64.0]
best = min(models_64pct, key=lambda m: m.metrics['RMSE'].validation)
```

## Performance Optimization

### Parallel Model Training

```python
def train_multiple_blueprints_parallel(
    project: dr.Project,
    blueprint_ids: list[str],
    sample_pct: float = 64.0
) -> list[dr.Model]:
    """Train multiple blueprints in parallel"""
    jobs = []

    # Start all training jobs
    for bp_id in blueprint_ids:
        job = project.train(bp_id, sample_pct=sample_pct)
        jobs.append(job)

    # Wait for all to complete
    models = []
    for job in jobs:
        model = job.get_result_when_complete(max_wait=3600)
        models.append(model)

    return models
```

### Efficient Predictions

```python
def efficient_batch_predictions(
    deployment: dr.Deployment,
    large_dataset: pd.DataFrame,
    chunk_size: int = 10000
) -> pd.DataFrame:
    """
    Process large datasets in chunks for efficiency
    """
    all_predictions = []

    for i in range(0, len(large_dataset), chunk_size):
        chunk = large_dataset.iloc[i:i + chunk_size]
        predictions = make_batch_predictions(deployment, chunk)
        all_predictions.append(predictions)
        print(f"Processed {i + len(chunk)}/{len(large_dataset)} rows")

    return pd.concat(all_predictions, ignore_index=True)
```

## Documentation and Resources

### Latest SDK Documentation

```python
# Use Context7 MCP to fetch latest docs
# Agent will automatically use this when available

# Check current version
import datarobot as dr
print(f"DataRobot SDK: {dr.__version__}")

# Recommended minimum: 3.3.0
# Latest stable: Check https://datarobot-public-api-client.readthedocs-hosted.com/
```

### Useful Links

- Official Docs: https://datarobot-public-api-client.readthedocs-hosted.com/
- API Reference: https://docs.datarobot.com/en/docs/api/
- Community: https://community.datarobot.com/
- Release Notes: https://docs.datarobot.com/en/docs/release/index.html

---

**Best Practice Summary**:

1. ✅ Always use environment variables for credentials
2. ✅ Use datetime partitioning for time-aware data
3. ✅ Wait for async jobs to complete
4. ✅ Compare models at same sample percentage
5. ✅ Enable monitoring on deployments
6. ✅ Submit actuals for accuracy tracking
7. ✅ Handle errors with retries
8. ✅ Use Context7 MCP for latest documentation
