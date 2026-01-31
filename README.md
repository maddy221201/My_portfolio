```markdown
# Credit Scorecard Project – Credit Card Default Risk

Hey, this is my take on building a proper credit scorecard using real-world-ish credit card data. I wanted something interpretable, something you could actually explain to a risk committee or regulator, not just another black-box model.

The goal here is simple: turn application info + payment history into a points-based credit score that tells you how risky someone is likely to be. Think old-school scorecard style — the kind banks still use because it's easy to understand and defend.

## What this actually does

- Cleans up the messy data from application_record.csv and credit_record.csv
- Defines a target: "bad" if someone ever went 90+ days late (or worse) in the observation window
- Fits a Random Forest model 
- The model checks the marker of a defaulter which through a more balanced dataset can be used to detect applicants' stability

## Why bother with a scorecard instead of XGBoost or whatever?

Because in real credit risk:
- You need to explain why someone got denied
- Regulators want monotonic trends and no surprises
- You want reason codes ("low income", "recent late payments", etc.)
- Stability over time matters more than squeezing out 0.01 extra AUC

Machine learning models are great for ranking, but they suck for production explainability.

## Folder layout

```
credit-scorecard-credit-card/
├── scorecard_credit.ipynb      ← the whole thing is here
├── application_record.csv      ← you can skip committing these big files
├── credit_record.csv
├── README.md
└── requirements.txt
```

## How I built it (rough steps)

1. Loaded the data and figured out the target from credit_record (worst status over time)
2. Fixed ages, employment years, imputed missing occupations roughly
3. Dropped junk variables and redundant columns
4. Trained Random Forest on the new features
5. Scaled points: picked PDO=20, base score 600 at 50:1 odds or something like that
6. Assesed the metric summary with a primary focus on Recall and ROC-AUC.

Performance on this dataset is decent — ROC-AUC ~0.95, Recall~ 0.55. Not world-beating, but realistic for credit data.

## Tech stack

Just the basics:
- pandas / numpy for data wrangling
- matplotlib/seaborn for quick plots
- scikit-learn Random Forest
- I tried scorecardpy but ended up doing most of it manually to understand it better

requirements.txt has everything.

## Running it

```bash
git clone https://github.com/Amateurish65455/credit-scorecard-credit-card.git
cd credit-scorecard-credit-card
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
jupyter notebook
```

Open scorecard_credit.ipynb and run top to bottom.

## Caveats / what’s missing

- No reject inference yet (huge in real life)
- Target window is simplistic — in production you'd have proper observation/performance periods
- No PSI monitoring code (but the notebook shows you how to prep for it)
- Points table and reason codes are sketched but not fully automated
- Could use better binning (optbinning is great)

## Next things I want to do

- Add reject inference (maybe simple fuzzy matching)
- Generate a clean Excel scorecard + reason codes
- Build a small monitoring dashboard (score drift, PSI plots)
- Try champion-challenger with XGBoost
- Calibrate properly to actual default rates

## Data source

This is based on the Home Credit / Kaggle credit card default datasets. Application + monthly credit behavior files.

## License

Apache3 – do whatever you want with it, just say hi if you use it.

Made by Amateurish Production  
[@Amateurish65455](https://x.com/Amateurish65455)  
January 2026

If you're reading this and actually building scorecards, hit me up — I'd love to hear what works for you in practice.
```