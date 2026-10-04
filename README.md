# ev-battery-health

Estimates EV battery state of health (SOH) and remaining useful life (RUL) from public datasets. Compares degradation models, quantifies uncertainty and provides reproducible benchmarks for battery analytics research.

> **Status:** early development. The data pipeline, models and benchmarks are being built in the open. See the [Roadmap](#roadmap).

---

## Why this exists

The battery is the most expensive part of an electric vehicle, and its health decides resale value, warranty claims, charging strategy and second-life use. Yet most battery-health projects share the same problems:

- Each one uses a different dataset, split and metric, so results **cannot be compared**.
- Most report a single number (for example, "SOH = 87%") with **no uncertainty**, which is risky for real decisions.
- Many leak information from the same cell into both train and test sets, which makes models look better than they are.

`ev-battery-health` aims to provide a clean, reproducible starting point:

- **Unified data loaders** for public battery aging datasets.
- **Leakage-safe splits** (train and test never share the same cell).
- **Multiple model families** compared under the same protocol.
- **Uncertainty estimates** with every prediction, not just a point value.
- **One command** to reproduce every reported result.

---

## What it predicts

| Quantity | Meaning |
|---|---|
| **SOH** (State of Health) | Current usable capacity divided by the rated (initial) capacity, as a percentage. |
| **RUL** (Remaining Useful Life) | Number of charge-discharge cycles left before SOH drops below an end-of-life threshold (commonly 80%). |
| **Prediction interval** | A range in which the true value is expected to fall with a stated confidence (for example, 90%). |

---

## Data

The project is built around openly available battery aging datasets, such as:

- NASA Prognostics Center of Excellence battery dataset
- CALCE battery data (University of Maryland)
- Oxford Battery Degradation Dataset
- MIT-Stanford fast-charging cycle life dataset

Datasets are **not** redistributed in this repository. Scripts download them from the original sources, and each dataset keeps its own license and citation requirements.

---

## Planned approach

### Models compared

- **Baselines:** linear and exponential capacity-fade fits
- **Classical ML:** gradient boosting, random forest on engineered features
- **Deep learning:** LSTM / GRU sequence models on cycle features
- **Probabilistic:** Gaussian process regression and quantile / conformal methods for uncertainty

### Metrics

- **Error:** MAE and RMSE for SOH, MAE in cycles for RUL
- **Calibration:** coverage of the prediction intervals (does the 90% interval really contain the truth 90% of the time?)
- **Interval width:** narrower is better, as long as coverage holds
- **Early-life accuracy:** how well the model predicts using only the first N cycles

---

## How it works

```
raw cycling data (voltage, current, temperature, capacity)
                    |
                    v
          cleaning + feature extraction
                    |
                    v
      leakage-safe split (by cell, not by row)
                    |
                    v
   model (baseline / ML / deep / probabilistic)
                    |
                    v
   SOH + RUL prediction with uncertainty interval
                    |
                    v
          metrics + plots + benchmark table
```

---

## Planned repository structure

```
ev-battery-health/
├── data/
│   ├── raw/              # downloaded datasets (git-ignored)
│   └── processed/        # cleaned features
├── ev_battery_health/
│   ├── datasets/         # loaders for each public dataset
│   ├── features.py       # feature extraction
│   ├── models/           # baselines, ML, deep and probabilistic models
│   ├── uncertainty.py    # intervals and calibration
│   └── evaluate.py       # metrics
├── benchmarks/           # scripts and saved results
├── notebooks/            # exploration and plots
├── tests/
├── docs/
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

---

## Quickstart (planned)

```bash
git clone https://github.com/amoltathesoftwareengineer/ev-battery-health.git
cd ev-battery-health
pip install -r requirements.txt

# download and prepare a dataset
python -m ev_battery_health.datasets.download --name nasa

# train a model and predict SOH / RUL
python -m ev_battery_health.train --model gbm --dataset nasa

# reproduce the benchmark table
python -m benchmarks.run_all
```

---

## Results

Results will be added once the first benchmark is complete.

| Model | Dataset | SOH MAE | RUL MAE (cycles) | 90% interval coverage |
|---|---|---|---|---|
| _coming soon_ | - | - | - | - |

---

## Roadmap

- [ ] Write dataset download and loader scripts
- [ ] Build cleaning and feature-extraction pipeline
- [ ] Implement leakage-safe train / test splitting
- [ ] Add baseline models (linear, exponential fit)
- [ ] Add ML and deep learning models
- [ ] Add uncertainty estimation and calibration checks
- [ ] Publish the first reproducible benchmark
- [ ] Add a simple dashboard for exploring predictions
- [ ] Add more datasets and cell chemistries

---

## Limitations

- Public datasets are mostly lab cycling data on single cells. Real vehicle packs age differently (temperature swings, partial cycles, mixed driving).
- Results on one dataset may not carry over to another chemistry or usage pattern.
- This project is for research and education. It is not a certified tool for warranty or safety decisions.

---

## Contributing

Contributions are welcome, especially:

- New dataset loaders
- New models and baselines
- Better uncertainty and calibration methods
- Bug reports and reproducibility fixes

Please open an issue to discuss before sending a large pull request.

---

## License

MIT License. See [LICENSE](LICENSE).

---

## Author

**Amol Tathe**
[LinkedIn](https://linkedin.com/in/amoltathesoftwareengineer) · [GitHub](https://github.com/amoltathesoftwareengineer)
