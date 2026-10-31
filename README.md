# Tata Motors Stock Price Prediction

A self-contained handover project based on the supplied `stockmarketF.ipynb`.
It trains an  neural network on historical closing prices, evaluates it on a
chronologically held-out test period, creates visual charts, and exports an
Excel workbook with embedded charts.

## 1. What is included

- `main.py` — complete training, evaluation, charting and Excel-export pipeline.
- `requirements.txt` — Python dependencies.
- `data/` — place the historical stock CSV here.
- `outputs/` — generated charts, Excel report, predictions, metrics and saved model.

## 2. Dataset required

The supplied dataset is included at `data/TATAMOTORS.NS.csv`.
It contains 8,427 source rows and these columns: `Unnamed: 0`, `open`, `high`,
`low`, `close`, `adjclose`, `volume`, `ticker`. The runner automatically maps
`Unnamed: 0` to Date and `close` to Close, removes rows with invalid dates/prices,
sorts chronologically and removes duplicate dates. After cleaning, the dataset
contains 8,420 observations covering 1991-01-02 through 2024-04-29.

The program also accepts conventional `Date`/`Close` column names if another
CSV is supplied. The default included file is ready to use; no separate download
is required.

## 3. Run on Windows

1. Install Python 3.10 or 3.11 (64-bit).
2. Extract the project ZIP.
3. Open the extracted folder.
4. Double-click `run_windows.bat`.
5. On the first run, it creates a virtual environment and installs packages.
6. The included dataset is already in `data/`; no extra file is needed.
7. The charts are saved to `outputs/` and displayed in chart windows.
8. Open `outputs/stock_prediction_report.xlsx` in Excel.

## 4. Run on macOS / Linux

Open Terminal in the extracted project folder and run:

    python3 -m venv .venv
    source .venv/bin/activate
    python -m pip install --upgrade pip
    pip install -r requirements.txt
    python main.py

To save charts without opening windows:

    python main.py --no-show

If the CSV is elsewhere:

    python main.py --csv "/full/path/to/TATAMOTORS.NS.csv"

## 5. Outputs

| File | Purpose |
|---|---|
| `stock_prediction_report.xlsx` | Summary, historical data, test predictions, training history and embedded charts |
| `actual_vs_predicted.png` | Actual vs predicted prices on unseen test data |
| `training_loss.png` | Training and validation loss across epochs |
| `test_predictions.csv` | Row-level actual and predicted test prices |
| `model_metrics.json` | MAE, RMSE, MAPE and next-day estimate |
| `lstm_stock_model.keras` | Trained Keras model |
| `price_scaler.pkl` | Fitted MinMaxScaler |

## 6. How the model works

1. Reads the CSV and sorts records by date.
2. Uses the `Close` column as the prediction target.
3. Splits chronologically: the earliest 80% is used for training and the latest
   20% is reserved for testing by default.
4. Fits MinMaxScaler on training data only.
5. Creates sequences of 60 previous trading-day closing prices.
6. Trains a three-layer LSTM network with 50 units per layer, 20% dropout,
   Adam optimizer and mean squared error loss.
7. Evaluates on the unseen test period.
8. Reports MAE, RMSE and MAPE, and estimates the next trading day's close from
   the latest 60 observations.

## 7. Important interpretation notes

- The original notebook trained on the full dataset and then tested on the
  final 200 rows. That means its test period was already seen during training,
  causing data leakage. This handover version fixes that issue.
- The original notebook's printed estimate (about ₹91.38) belongs to its
  original run and should not be treated as a verified current forecast.
- A next-trading-day estimate is a model output, not a guaranteed future price.
- Stock prices are affected by news, market conditions, corporate actions,
  liquidity and many variables not represented by a Close-only model.
- This project is educational and is not financial or investment advice.

## 8. Troubleshooting

**Dataset not found:** put the CSV in `data/TATAMOTORS.NS.csv` or pass `--csv`.

**Missing Date or Close column:** check the CSV header spelling.

**TensorFlow installation issue:** use 64-bit Python 3.10/3.11 and create a
fresh virtual environment. TensorFlow platform support varies by Python version
and operating system.

**Excel charts not visible:** open the generated `.xlsx` file in Microsoft
Excel or LibreOffice Calc; the Charts worksheet contains embedded charts.

**Training takes time:** LSTM training speed depends on CPU/GPU, dataset size
and TensorFlow build. Reduce epochs for a quick test, e.g. `python main.py --epochs 5`.

## 9. Project structure

    stock_market_prediction_project/
    ├── data/
    │   └── README_DATASET.txt
    ├── outputs/
    │   └── README_OUTPUTS.txt
    ├── main.py
    ├── requirements.txt
    ├── run_windows.bat
    └── README.md
