# Multi-Horizon Solar Power Forecasting for Grid Integration

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Machine learning-based solar power forecasting system for grid integration, featuring multi-horizon predictions, uncertainty quantification, and model interpretability analysis.

## Problem Statement

Solar power is inherently **intermittent** - output varies with weather and time of day. Grid operators require accurate forecasts to:

- **Balance supply and demand** in real-time (AGC)
- **Schedule reserves** and backup generation
- **Optimize economic dispatch** of power plants
- **Plan unit commitment** for day-ahead operations

This project implements **multi-horizon forecasting** (15-min, 1-hour, 4-hour ahead) with **prediction intervals** and **SHAP-based interpretability** to address these operational needs.

## Key Results

### Model Performance (Test Set)

| Horizon | Best Model | R² | RMSE (kW) | nRMSE |
|---------|------------|-----|-----------|-------|
| 15 min | Ridge | **0.934** | 21,078 | 32.5% |
| 1 hour | LightGBM | **0.884** | 27,872 | 43.5% |
| 4 hours | CatBoost | **0.875** | 28,570 | 46.9% |

### Cross-Validation Stability (1-hour horizon)

| Model | CV R² (Mean ± Std) |
|-------|-------------------|
| CatBoost | **0.879 ± 0.011** |
| XGBoost | 0.871 ± 0.019 |
| LightGBM | 0.870 ± 0.015 |
| Ridge | 0.857 ± 0.027 |

### Uncertainty Quantification

- **80% Prediction Interval Coverage**: 78.5% (target: 80%)
- Adaptive interval width based on forecast uncertainty

## Features

### Technical Highlights

- **Multi-Horizon Forecasting**: 15-min, 1-hour, and 4-hour ahead predictions
- **32 Engineered Features**: Time-based, solar position, lag, rolling statistics, ramp rates
- **4 Model Comparison**: Ridge, XGBoost, LightGBM, CatBoost
- **Time Series CV**: 5-fold walk-forward validation
- **Uncertainty Quantification**: Quantile regression for prediction intervals
- **SHAP Analysis**: Model interpretability and feature importance

### Feature Engineering

| Category | Features | Description |
|----------|----------|-------------|
| Time | hour_sin/cos, time_slot | Cyclical encoding for daily patterns |
| Solar Position | hour_angle, solar_elevation | Sun position approximation |
| Temperature | temp_diff, temp_efficiency | Thermal derating (~0.4%/°C) |
| Lag Features | power_lag_1/4/96 | Historical values (15min, 1hr, 24hr) |
| Rolling Stats | mean, std, max | 1-hour and 2-hour windows |
| Ramp Rate | power_ramp, irr_ramp | Rate of change features |

## Grid Integration Applications

| Forecast Horizon | Application | Operational Use |
|------------------|-------------|-----------------|
| 15 minutes | AGC | Real-time frequency regulation |
| 1 hour | Economic Dispatch | Generator scheduling, reserve allocation |
| 4 hours | Unit Commitment | Start-up/shut-down decisions |

## Project Structure

```
solar-power-forecasting/
├── data/                                    # Dataset (gitignored)
│   ├── Plant_1_Generation_Data.csv
│   └── Plant_1_Weather_Sensor_Data.csv
├── notebooks/
│   └── 04_multi_horizon_forecasting.ipynb   # Main analysis
├── requirements.txt
└── README.md
```

## Quick Start

```bash
# Clone repository
git clone https://github.com/ZeuPark/SolarPowerForecasting.git
cd SolarPowerForecasting

# Install dependencies
pip install -r requirements.txt

# Run the notebook
jupyter notebook notebooks/04_multi_horizon_forecasting.ipynb
```

## Dataset

- **Source**: [Kaggle - Solar Power Generation Data](https://www.kaggle.com/datasets/anikannal/solar-power-generation-data)
- **Duration**: 34 days (May 15 - June 17, 2020)
- **Resolution**: 15-minute intervals (3,157 samples)
- **Plant**: 22 inverters aggregated to plant-level

| Feature | Description | Unit |
|---------|-------------|------|
| DC_POWER | Target variable (total plant output) | kW |
| IRRADIATION | Solar irradiance | kW/m² |
| AMBIENT_TEMPERATURE | Air temperature | °C |
| MODULE_TEMPERATURE | Panel temperature | °C |

## Technical Stack

| Category | Tools |
|----------|-------|
| Data Processing | pandas, numpy |
| Machine Learning | scikit-learn, XGBoost, LightGBM, CatBoost |
| Interpretability | SHAP |
| Visualization | matplotlib, seaborn |

## Methodology

### 1. Data Preprocessing
- Aggregate 22 inverters to plant-level
- Remove data leakage (AC_POWER, DAILY_YIELD, TOTAL_YIELD)
- Handle missing values from lag/rolling features

### 2. Feature Engineering
- 32 features capturing temporal patterns, solar physics, and historical trends
- Temperature efficiency factor based on Si panel thermal coefficients

### 3. Model Training
- Chronological train/test split (80/20)
- Separate models for each forecast horizon
- Hyperparameter tuning via cross-validation

### 4. Uncertainty Quantification
- Quantile regression (10th, 50th, 90th percentiles)
- 80% prediction intervals for reserve planning

### 5. Model Interpretability
- SHAP TreeExplainer for feature importance
- Dependence plots for feature interactions
- Waterfall plots for individual predictions

## Key Insights

### SHAP Analysis Findings
1. **Lag features dominate**: Recent power values most influential
2. **Irradiation matters**: Primary physical driver correctly identified
3. **Physical consistency**: Model behavior aligns with solar physics
4. **Time patterns**: Hour and time_slot capture daily cycles

### Error Patterns
- Higher errors during peak generation hours (10:00-14:00)
- Morning/evening transitions show more variability
- Prediction intervals widen during uncertain periods

## Future Work

- Weather forecast integration (NWP data)
- Deep learning models (LSTM, Transformer)
- Real-time deployment with SCADA integration
- Multi-plant ensemble forecasting

## Author

Electrical Engineering Student  
Focus: Power Systems, Renewable Energy Integration, Machine Learning

## License

MIT License
