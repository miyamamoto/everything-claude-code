---
name: datarobot
description: DataRobot development specialist. Implements AutoML workflows, deployments, and predictions using DataRobot SDK.
---

# /datarobot - DataRobot Development Command

Launch DataRobot specialist agent for AutoML implementation, model deployment, and production ML operations.

## Usage

```bash
/datarobot [task description]

# Examples:
/datarobot Create a classification project for customer churn
/datarobot Deploy the best model with monitoring enabled
/datarobot Implement time series forecasting for sales data
/datarobot Set up batch predictions with error handling
```

## What This Command Does

Invokes the **datarobot-specialist** agent to:

1. **Project Creation** - Set up AutoML projects with proper configuration
2. **Model Training** - Train and evaluate models using DataRobot
3. **Model Selection** - Analyze leaderboard and select best models
4. **Deployment** - Deploy models to prediction servers
5. **Predictions** - Implement batch and real-time predictions
6. **MLOps** - Set up monitoring, drift detection, and retraining

## Agent Capabilities

### AutoML Workflows

- Classification (binary/multiclass)
- Regression
- Time series forecasting
- Anomaly detection
- Custom metrics and partitioning

### Model Analysis

- Feature impact analysis
- SHAP value computation
- Model comparison
- Performance metrics
- Recommendation engine

### Deployment & Predictions

- Model deployment with monitoring
- Champion/challenger setup
- Batch predictions
- Real-time API predictions
- Prediction explanations

### MLOps

- Data drift monitoring
- Accuracy tracking
- Actuals submission
- Automated retraining
- A/B testing

## When to Use

Use this command when:

- Creating new DataRobot projects
- Deploying models to production
- Implementing prediction pipelines
- Setting up model monitoring
- Troubleshooting DataRobot SDK issues
- Following MLOps best practices

## Example Workflows

### Create Classification Project

```bash
/datarobot Create a binary classification project for customer churn prediction
```

Agent will:
1. Set up project with proper partitioning
2. Configure AutoML settings
3. Train models
4. Analyze feature importance
5. Deploy best model
6. Implement prediction pipeline

### Deploy Model with Monitoring

```bash
/datarobot Deploy the top-performing model with full MLOps monitoring enabled
```

Agent will:
1. Select best model from leaderboard
2. Create deployment
3. Enable drift tracking
4. Configure prediction logging
5. Set up accuracy monitoring
6. Provide deployment API examples

### Time Series Forecasting

```bash
/datarobot Create time series project to forecast sales data, with 7-day forecast window
```

Agent will:
1. Configure time series settings
2. Define known-in-advance features
3. Set forecast window
4. Train time series models
5. Generate forecasts
6. Validate temporal integrity

## Best Practices Applied

The agent automatically:

✅ Uses environment variables for credentials
✅ Implements temporal validation for time-aware data
✅ Waits for async jobs to complete
✅ Compares models at same sample percentage
✅ Enables monitoring on deployments
✅ Handles errors with retry logic
✅ References latest documentation via Context7 MCP

## Common Tasks

### Project Setup

```bash
/datarobot Set up AutoML project for [use case]
```

### Model Deployment

```bash
/datarobot Deploy model [model_id] with monitoring
```

### Predictions

```bash
/datarobot Implement batch predictions for [dataset]
```

### MLOps

```bash
/datarobot Set up drift monitoring and automated retraining
```

## Output

Agent provides:

- Complete DataRobot SDK code
- Configuration explanations
- Best practice recommendations
- Error handling patterns
- Integration examples
- Monitoring setup

## Related Commands

- `/tdd` - Write tests for DataRobot code
- `/code-review` - Review DataRobot implementation
- `/security-review` - Check credential handling
- `/python-test` - Create comprehensive test suite

## Documentation

Agent uses **Context7 MCP** to fetch latest DataRobot documentation:
- Python SDK docs
- API reference
- Best practices
- Release notes

Always has access to up-to-date information.

---

**Quick Start**: `/datarobot Create a classification project for [your use case]`
