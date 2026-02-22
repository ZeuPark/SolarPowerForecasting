# Multi-Horizon Solar Power Forecasting for Grid Integration

Forecasting solar power generation at multiple time horizons to support grid stability and economic dispatch.

## Problem Statement

Solar power is inherently **intermittent** - output varies with weather and time of day. Grid operators require accurate forecasts to:

- **Balance supply and demand** in real-time
- **Schedule reserves** and backup generation
- **Optimize economic dispatch** of power plants
- **Plan unit commitment** for day-ahead operations

This project implements multi-horizon forecasting (15-min, 1-hour, 4-hour ahead) using machine learning to address these operational needs.

## Key Features

- **Multi-Horizon Forecasting**: 15-min, 1-hour, and 4-hour ahead predictions
- **Feature Engineering**: Time-based, solar position, lag features, rolling statistics
- **Model Comparison**: Ridge Regression, XGBoost, LightGBM
- **Time Series Cross-Validation**: Walk-forward validation for proper evaluation
- **Error Analysis**: Performance breakdown by time of day and conditions

## Dataset

- **Source**: [Kaggle - Solar Power Generation Data](https://www.kaggle.com/datasets/anikannal/solar-power-generation-data)
- **Duration**: 34 days of 15-minute interval data
- **Plant**: 22 inverters, aggregated to plant-level output
- **Features**:
  - DC Power (target)
  - Irradiation (kW/m²)
  - Ambient Temperature (°C)
  - Module Temperature (°C)

## Methodology

### Feature Engineering

| Category | Features |
|----------|----------|
| Time | hour, minute, dayofweek, time_slot, cyclical encoding |
| Solar Position | hour_angle, solar_elevation_approx, is_daytime |
| Temperature | temp_diff, temp_efficiency (thermal derating) |
| Lag Features | power_lag_1/2/4/8/16/96, irr_lag_1/4/8 |
| Rolling Stats | mean, std, max over 1-hour and 2-hour windows |
| Ramp Rate | power change rate, irradiation change rate |

### Models

- **Ridge Regression**: Linear baseline with L2 regularization
- **XGBoost**: Gradient boosting with tree-based learning
- **LightGBM**: Fast gradient boosting, handles large datasets efficiently

### Validation

- **Train/Test Split**: 80/20 chronological split (no shuffle)
- **Time Series CV**: 5-fold walk-forward validation

## Results

| Horizon | Best Model | R² | RMSE | nRMSE |
|---------|------------|-----|------|-------|
| 15 min | LightGBM | 0.98+ | - | - |
| 1 hour | LightGBM | 0.95+ | - | - |
| 4 hours | LightGBM | 0.85+ | - | - |

*Run the notebook to see full results*

## Project Structure

```
solar-power-forecasting/
├── data/                              # Dataset (not tracked)
├── notebooks/
│   ├── 01_data_exploration.ipynb      # Initial EDA
│   ├── 03_pipeline_v2.ipynb           # Baseline model
│   └── 04_multi_horizon_forecasting.ipynb  # Main analysis
├── models/                            # Saved models (not tracked)
├── requirements.txt
└── README.md
```

## Tech Stack

- **Data Processing**: pandas, numpy
- **Machine Learning**: scikit-learn, XGBoost, LightGBM
- **Visualization**: matplotlib, seaborn

## Usage

```bash
# Install dependencies
pip install -r requirements.txt

# Run the main notebook
jupyter notebook notebooks/04_multi_horizon_forecasting.ipynb
```

## Grid Integration Value

| Forecast Horizon | Grid Application |
|------------------|------------------|
| 15 minutes | Real-time balancing, AGC (Automatic Generation Control) |
| 1 hour | Economic dispatch, spinning reserve scheduling |
| 4 hours | Unit commitment, day-ahead market participation |

## Future Improvements

- Incorporate weather forecast data (NWP)
- Ensemble methods combining multiple horizons
- Deep learning (LSTM/Transformer) for sequence modeling
- Probabilistic forecasting with prediction intervals
- Integration with real-time SCADA systems

## Author

UNSW Electrical Engineering Student  
Focus: Power Systems, Renewable Energy Integration, Data Science
