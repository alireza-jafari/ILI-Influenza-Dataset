# Influenza-Like-Illness (ILI) Forecasting on U.S. HHS Regions  
_A clean, reproducible starter kit_


## 📑 Overview
This repository packages a **region-level, weekly ILI time-series dataset (2005 – 2025)** from the U.S. CDC FluView API together with data-prep utilities, cross-validation helpers, and example visualisations.  
It is designed to let researchers benchmark classical models (ARIMA, Prophet, etc.) and modern sequence models (Transformers, Time-LLMs) under two complementary evaluation protocols:

1. **Spatial hold-out** – train on 8 regions, test on 2 unseen regions (5-fold CV)  
2. **Temporal hold-out** – train on the first *N* weeks of every region, predict the most recent weeks


---

## 🔍 Data
| Field            | Description                                            |
|------------------|--------------------------------------------------------|
| `YEAR`, `WEEK`   | ISO calendar year / week number                        |
| `REGION`         | HHS region label (`Region 1` … `Region 10`)            |
| `% WEIGHTED ILI` | Main target – % of outpatient visits with ILI symptoms |
| `AGE …`, `ILITOTAL` | Optional covariates (age-stratified counts)        |

The raw export is stored in **`data/raw/ILINet.csv`** (≈ 10 kB). It is *already* cleaned for NaNs and sorted by region & week inside the generator scripts, so no heavy preprocessing is required beyond those utilities.


![Picture2](https://github.com/user-attachments/assets/810d6dc5-0ac5-4147-9755-66bb6ace6d54)

![Picture1](https://github.com/user-attachments/assets/c375bdfb-ffab-412d-90bd-048850f9c81b)

Source: U.S. Centers for Disease Control and Prevention (CDC), FluView Interactive.
---


### What each script does
| Script | Purpose | Key I/O |
|--------|---------|---------|
| **`Data_Generator_ILI_HHS_LocationSplit.py`** | 5-fold **spatial CV**: choose one of the predefined train/test region splits and emit Pandas DataFrames for train & test. | Reads `ILINet.csv`; writes/returns `train_df`, `test_df` etc. (see example below) :contentReference[oaicite:0]{index=0} |
| **`Data_Generator_ILI_HHS_TimeSplit.py`** | **Temporal split**: last *k %* of each series becomes test, rest is train. | Same as above; user controls `--test_size`. :contentReference[oaicite:1]{index=1} |
| **`Utils.py`** | Tiny helpers: `splitter()` (row-level split per series) & `drop_last_n_samples()`. | Imported by both generators. :contentReference[oaicite:2]{index=2} |
| **`Plots.ipynb`** | Starter notebook: loads the processed DataFrames and recreates the line plot shown here; feel free to extend with your own model experiments. |

---

> **Data provenance.**  
> Weekly, region-level influenza-like illness percentages were
> downloaded from CDC FluView Interactive on <download-date>.  
> *Source: Centers for Disease Control and Prevention (CDC).*  
> Reference to CDC does **not** imply endorsement by CDC, HHS,
> or the United States Government. The original, freely available
> data can be accessed at https://gis.cdc.gov/grasp/fluview/fluportaldashboard.html.


## 🚀 Quick-start

### 1. Clone and install
```bash
git clone https://github.com/<your-user>/ILI-HHS-Influenza.git
cd ILI-HHS-Influenza
python -m venv .venv      # or `conda env create -f environment.yml`
source .venv/bin/activate
pip install -r requirements.txt
