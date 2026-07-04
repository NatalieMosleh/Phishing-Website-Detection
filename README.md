# Phishing Website Detection — A Critical Reproduction Study

**Course:** Data Science in Cyber — Final Project
**Domain:** Phishing Detection

## Project description

This project does not merely reproduce a phishing-detection tutorial — it **critically
evaluates** whether the author's claims are supported by the data and experiments, as
required by the project brief.

The evaluated source trains six classifiers on 10,000 URLs (50% phishing / 50%
legitimate) described by 17 binary/ordinal features, and reports ~86% test accuracy
(XGBoost best), concluding it is an effective phishing detector. We reproduce the
pipeline and then stress-test that conclusion. Our main findings:

- **The ~86% accuracy reproduces but is an information ceiling, not model skill.** The
  coarse binary features collapse 10,000 URLs into only **771 distinct feature vectors**;
  ~64% of rows are *ambiguous* (identical features, both labels), making the Bayes-optimal
  accuracy **0.868** — exactly where every model lands.
- **A leaky feature.** `URL_Length` equals 1 for **100% of legitimate rows** — it encodes
  *which corpus a URL came from*, not whether it is phishing (selection bias).
- **The balanced-data illusion.** Re-projected onto a realistic ~1% phishing prevalence,
  the precision a user would experience falls to **~11%**.
- **Not reproducible today.** The feature extractor depends on Alexa rank (discontinued
  2022) and live WHOIS lookups; removing those features halves model quality (MCC
  0.744 → 0.433).

**Verdict:** the narrow "XGBoost is best" claim holds; the broad "effective ~86% detector"
claim is **not supported** once rigorous, realistic evaluation is applied.

## Selected source (article / tutorial)

- **Tutorial / write-up:** *Phishing Website Detection by Machine Learning Techniques* —
  the project README and accompanying notebooks serve as the tutorial.
  https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques
- **Original GitHub repository:** same as above
  (https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques)

## Dataset source

- **File used:** `data/urldata.csv` (10,000 rows × 18 columns).
- **Origin:** the source repository's `DataFiles/5.urldata.csv`, built from **PhishTank**
  (phishing URLs) and a benign-URL corpus (legitimate URLs), with features extracted by
  the author's `URLFeatureExtraction` code.
- **Direct link:**
  https://raw.githubusercontent.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques/master/DataFiles/5.urldata.csv

## Repository contents

| Path | Description |
|---|---|
| `Phishing_Detection_Critical_Evaluation.ipynb` | **Main deliverable** — complete, executed, documented notebook. |
| `Phishing_Detection_Report.pdf` | **Written report** (all required sections + executive summary). |
| `data/urldata.csv` | The dataset. |
| `outputs/` | Saved metrics (`model_metrics.csv`, `key_findings.json`, …) and figures. |
| `requirements.txt` | Pinned dependencies (Python 3.12). |

## Execution instructions

```bash
# 1. Install dependencies (Python 3.12 recommended)
pip install -r requirements.txt

# 2a. Open and run the notebook interactively
jupyter notebook Phishing_Detection_Critical_Evaluation.ipynb
#     (Kernel -> Restart & Run All)

# 2b. ...or execute it headlessly from the command line
python -m nbconvert --to notebook --execute --inplace \
    Phishing_Detection_Critical_Evaluation.ipynb
```

All randomness is seeded (`RANDOM_STATE = 42`), so results are deterministic. The
notebook reads only `data/urldata.csv` and writes its artifacts to `outputs/`; no internet
access is required to run it.

## Reproducibility note

The **modelling** pipeline is fully reproducible from the supplied CSV. The original
**feature-extraction** step is *not* reproducible today (Alexa discontinued; WHOIS is
live and time-dependent) — a limitation analysed in detail in §4 of the report and the
notebook.
