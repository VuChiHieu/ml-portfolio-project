# AI-Powered Stock Return Forecasting & Portfolio Optimization

Ung dung Machine Learning trong du bao loi suat co phieu va xay dung danh muc dau tu toi uu — Nghien cuu tren nhom co phieu von hoa lon thuoc S&P 500.


## Demo truc tuyen

**App: [https://ml-portfolio-project-kkw3crj8yztvlqdvrcg9sj.streamlit.app/](https://ml-portfolio-project-kkw3crj8yztvlqdvrcg9sj.streamlit.app/)**

## Muc tieu

Xay dung he thong su dung du lieu lich su cua 25 co phieu large-cap S&P 500 de:
- Du bao loi suat 5 ngay giao dich tiep theo (`future_return_5d`)
- Xep hang va chon Top-K co phieu tiem nang
- Xay dung danh muc dau tu va backtest chien luoc

## Cau truc project
ml-portfolio-project/
├── app.py # Streamlit dashboard (4 trang)
├── requirements.txt
├── data/processed/ # Dataset da xu ly (feature + target, da chia train/val/test)
├── models/ # Model da train (XGBoost, Random Forest, LSTM) + scaler
├── notebooks/ # Notebook pipeline day du (EDA -> Backtest)
├── results/ # Ket qua backtest, bieu do, bang ablation study
└── README.md

## Phuong phap

1. **Du lieu:** OHLCV daily 2000-2026, 25 ma large-cap S&P 500, tu Yahoo Finance (`yfinance`)
2. **Feature (17):** Price/Return (5), Technical Indicators dang ty le (10), Volume (2)
3. **Time-based split:** Train (2000-2017) / Validation (2018-2021) / Test (2022-2026) — khong random split de tranh leakage
4. **Model:** Linear Regression (baseline), Random Forest, XGBoost, LSTM
5. **Portfolio construction:** Rank theo predicted return -> chon Top-K -> phan bo trong so -> rebalance moi 5 ngay
6. **Danh gia:** IC (Information Coefficient), Sharpe Ratio, Volatility, Max Drawdown, Calmar Ratio

## Ket qua chinh (Test set, 2022-2026)

| Chien luoc | Sharpe Ratio | Total Return | Max Drawdown |
|---|---|---|---|
| Buy & Hold | **1.068** | 87.88% | -18.92% |
| Equal Weight | 1.026 | 82.80% | -19.92% |
| XGBoost (Top5, Equal Weight) | 0.777 | 90.09% | -19.87% |
| LSTM | 0.759 | 90.73% | -20.31% |
| Linear Regression | 0.702 | 80.26% | -21.21% |
| Random Forest | 0.599 | 61.27% | -21.32% |
| Proposed Strategy (Top5, Volatility Weight) | 0.553 | 51.33% | -26.03% |

**Ket luan quan trong:** Muc tieu Sharpe >= 1.8 KHONG dat duoc bang phuong phap trung thuc. Chien luoc tot nhat la Buy & Hold thu dong. Cac model ML co tin hieu du bao that (IC duong nhat quan, 0.014-0.028) nhung chua du manh de vuot qua chi phi rui ro khi tap trung danh muc Top-K.

**Phat hien quan trong nhat:** Proposed Strategy (Top-5 + Volatility Weight) dat Sharpe 1.130 tren Validation nhung sut con 0.553 tren Test — bang chung thuc nghiem ro rang ve hien tuong **backtest/validation overfitting**, mot van de pho bien trong quant finance.

## Cai dat va chay

```bash
pip install -r requirements.txt
streamlit run app.py
```

Luu y: `app.py` doc du lieu tu thu muc `data/` va `models/` trong repo — can giu nguyen cau truc thu muc.

## Ablation Study

| Experiment | Features | IC | Sharpe |
|---|---|---|---|
| A: Price/Return only | 5 | 0.0134 | 0.584 |
| B: A + Technical Indicators | 15 | 0.0145 | 0.714 |
| C: B + Volume (full) | 17 | 0.0135 | 0.777 |

Technical Indicators dong gop manh nhat vao hieu suat; Volume features dong gop nhe hon nhung van tich cuc.

## Gioi han cua nghien cuu

- Chi dung du lieu OHLCV cong khai, khong co alternative data (tin tuc, sentiment, du lieu vi mo)
- Bo qua transaction cost va slippage trong backtest
- Universe chi 25 ma, nho hon chuan nghien cuu thuc te
- Chi bao (ATR_pct) va hieu suat portfolio deu the hien "regime shift" ro ret giua giai doan 2018-2021 (COVID) va 2022-2026 (lai suat tang)

## Cong nghe su dung

Python, Pandas, NumPy, Scikit-learn, XGBoost, PyTorch (LSTM), Plotly, Streamlit

## Disclaimer

Day la do an hoc thuat, khong phai he thong giao dich tien that. Khong cam ket loi nhuan. Moi ket qua deu tinh tu du lieu lich su, khong dam bao hieu suat tuong lai.
