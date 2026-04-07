<div align="center">

<br/>



### Identify phishing URLs before they identify you.

<br/>

[![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-3.1.2-black?style=flat-square&logo=flask)](https://flask.palletsprojects.com)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.0.2-F7931E?style=flat-square&logo=scikit-learn&logoColor=white)](https://scikit-learn.org)
[![XGBoost](https://img.shields.io/badge/XGBoost-enabled-0070C0?style=flat-square)](https://xgboost.readthedocs.io)
[![CatBoost](https://img.shields.io/badge/CatBoost-enabled-yellow?style=flat-square)](https://catboost.ai)
[![Dataset](https://img.shields.io/badge/Dataset-Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/eswarchandt/phishing-website-detector)
[![License](https://img.shields.io/github/license/gketan456/Identification-of-Phising-websites-and-URLs?style=flat-square)](LICENSE)

<br/>

| 🧠 10 Models Benchmarked | 📊 11,054 Real Samples | 🔍 30 Engineered Features | 🏆 97.4% Accuracy |
|:---:|:---:|:---:|:---:|

</div>

---

## 🚨 The Problem

Every day, millions of people click phishing links — fake URLs crafted to look like real websites — and unknowingly hand over passwords, credit card numbers, and personal data. In 2023 alone, phishing accounted for over **36% of all data breaches**.

Traditional defenses rely on:
- **Blocklists** — only catch *known* threats, useless against new attacks
- **Manual rules** — brittle, easy to bypass, expensive to maintain

This project takes a different approach: **machine learning that learns the structural DNA of a phishing URL** — patterns that hold true even for URLs that have never been seen before.

---

## 💡 The Solution

A complete ML pipeline that:

1. **Extracts 30 features** from any URL — covering its structure, domain metadata, HTML content, and external signals
2. **Trains and benchmarks 10 different classification algorithms** side-by-side on 11,054 labeled real-world URLs
3. **Deploys the best model** (Gradient Boosting, 97.4% accuracy) as a live Flask web app where anyone can paste a URL and get an instant verdict

```
Paste any URL  →  30 features extracted in real time  →  Prediction in milliseconds

http://paypa1-secure-login.ru/verify   →   ❌ Phishing detected
https://www.github.com                 →   ✅ Safe to visit
```

---

## 🛠️ Tech Stack & Tools Used

| Category | Tools |
|---|---|
| Language | Python 3.8+ |
| Web Framework | Flask 3.1.2 |
| ML & Data | scikit-learn, XGBoost, CatBoost, pandas, numpy |
| Feature Extraction | BeautifulSoup4, requests, python-whois, googlesearch-python |
| Visualization | matplotlib, seaborn |
| Model Persistence | pickle |
| Frontend | HTML, CSS, Jinja2 templates |
| Dataset | [Kaggle — Phishing Website Detector](https://www.kaggle.com/eswarchandt/phishing-website-detector) |
| Notebook | Jupyter Notebook |

---

## 🔬 What I Did — Step by Step

### 1. Data Loading & Understanding
- Loaded 11,054 URL samples from Kaggle into a pandas DataFrame
- Dataset has **30 feature columns** + 1 label column (`1` = phishing, `-1` = legitimate)
- All features are integer-encoded (`1`, `0`, `-1`), so no label encoding was needed
- **Zero missing values** — clean dataset ready for modeling

### 2. Exploratory Data Analysis (EDA)
- Used `data.describe()`, `data.info()`, `data.nunique()` to profile every feature
- Generated a **30×30 correlation heatmap** using seaborn to understand feature relationships
- Plotted a **pie chart** of class distribution to check for class imbalance
- Key observations:
  - No outliers present
  - No null values in any column
  - Features like `PrefixSuffix-`, `LongURL`, `PageRank` showed strong correlation with phishing class

### 3. Data Splitting
- Split into **80% training / 20% test** using `train_test_split` with `random_state=42`
- Training set: **8,843 samples** · Test set: **2,211 samples**

### 4. Feature Engineering
30 features extracted per URL across 3 layers:

<details>
<summary><b>URL structure features (1–12)</b></summary>

| # | Feature | What it detects |
|---|---------|-----------------|
| 1 | `UsingIP` | Raw IP in URL instead of a domain name |
| 2 | `LongURL` | URL length > 75 characters |
| 3 | `ShortURL` | URL shortener services (bit.ly, tinyurl, goo.gl…) |
| 4 | `Symbol@` | `@` symbol forces browser to ignore the left side |
| 5 | `Redirecting//` | Double slash `//` beyond position 6 = redirect |
| 6 | `PrefixSuffix-` | Dash in domain name (e.g. `paypal-secure.com`) |
| 7 | `SubDomains` | Excess dots = too many subdomains |
| 8 | `HTTPS` | Absence of HTTPS protocol |
| 9 | `DomainRegLen` | Domain registered for less than 1 year |
| 10 | `Favicon` | Favicon loaded from a foreign domain |
| 11 | `NonStdPort` | Unusual port number in URL |
| 12 | `HTTPSDomainURL` | The word `https` appearing inside the domain string itself |

</details>

<details>
<summary><b>HTML & JavaScript features (13–21)</b></summary>

| # | Feature | What it detects |
|---|---------|-----------------|
| 13 | `RequestURL` | % of objects (images, audio, iframes) loaded from external domains |
| 14 | `AnchorURL` | % of `<a>` tags linking to external/suspicious domains |
| 15 | `LinksInScriptTags` | % of `<link>` and `<script>` tags from external sources |
| 16 | `ServerFormHandler` | Form action pointing to blank or external URL |
| 17 | `InfoEmail` | `mailto:` used to harvest credentials |
| 18 | `AbnormalURL` | URL hostname doesn't match WHOIS record |
| 19 | `WebsiteForwarding` | More than 4 HTTP redirects in the response chain |
| 20 | `StatusBarCust` | `onmouseover` used to hide the real destination URL |
| 21 | `DisableRightClick` | JavaScript blocking right-click to prevent source inspection |

</details>

<details>
<summary><b>Domain & external signal features (22–30)</b></summary>

| # | Feature | What it detects |
|---|---------|-----------------|
| 22 | `UsingPopupWindow` | Popup window requesting credentials |
| 23 | `IframeRedirection` | Hidden iframe for invisible redirects |
| 24 | `AgeofDomain` | Domain younger than 6 months |
| 25 | `DNSRecording` | Missing or very recent DNS record |
| 26 | `WebsiteTraffic` | Low Alexa rank indicates low-trust site |
| 27 | `PageRank` | Low global page rank |
| 28 | `GoogleIndex` | URL not indexed by Google |
| 29 | `LinksPointingToPage` | Very few inbound backlinks |
| 30 | `StatsReport` | IP/URL matches known phishing blacklists |

</details>

### 5. Model Training — 10 Algorithms Benchmarked

Every model was trained on the same 8,843-sample training set and evaluated on the same 2,211-sample test set. Metrics tracked: **Accuracy, F1 Score, Recall, Precision**.

| Rank | Algorithm | Accuracy | F1 Score | Recall | Precision |
|:----:|-----------|:--------:|:--------:|:------:|:---------:|
| 🥇 | **Gradient Boosting Classifier** ← deployed | **97.4%** | **97.7%** | **99.4%** | **98.6%** |
| 🥈 | CatBoost Classifier | 97.2% | 97.5% | 99.4% | 98.9% |
| 🥉 | XGBoost Classifier | 96.9% | 97.3% | 99.3% | 98.4% |
| 4 | Multi-layer Perceptron (Neural Net) | 96.7% | 97.0% | 98.5% | 98.9% |
| 5 | Random Forest | 96.6% | 96.9% | 99.2% | 99.1% |
| 6 | Support Vector Machine | 96.4% | 96.8% | 98.0% | 96.5% |
| 7 | Decision Tree | 95.7% | 96.2% | 99.1% | 99.3% |
| 8 | K-Nearest Neighbors | 95.6% | 96.1% | 99.1% | 98.9% |
| 9 | Logistic Regression | 93.4% | 94.1% | 94.3% | 92.7% |
| 10 | Naive Bayes Classifier | 60.5% | 45.4% | 29.2% | 99.7% |

**Notable implementation details:**
- SVM was tuned using `GridSearchCV` with `kernel: ['rbf', 'linear']` and `gamma: [0.1]`
- Gradient Boosting used `max_depth=4`, `learning_rate=0.7`
- A learning rate sweep (0.1 → 0.9) was run to validate the GBC hyperparameter choice
- All results stored in a comparison DataFrame, sorted by accuracy + F1

### 6. Model Comparison & Selection

Results were compiled into a unified DataFrame and sorted by Accuracy + F1 Score. A line chart was plotted to visually compare all 10 models.

**Why Gradient Boosting was chosen:**
- Highest test accuracy at **97.4%**
- Highest recall at **99.4%** — meaning it misses the fewest phishing URLs
- In security contexts, **recall matters more than precision**: a missed phishing URL causes real harm; a false alarm just means extra caution

### 7. Key Findings

- **Ensemble methods dominate** — GBC, CatBoost, XGBoost, Random Forest, and MLP all exceeded 96% accuracy. The non-linear, high-correlation nature of URL features rewards ensemble approaches.
- **Naive Bayes failed badly** — 60.5% accuracy. The feature independence assumption breaks down completely since URL signals are highly correlated.
- **Top 3 most predictive features** by importance: `HTTPS`, `AnchorURL`, `WebsiteTraffic`
- **Zero overfitting** on top models — GBC train accuracy (98.9%) vs test accuracy (97.4%) shows excellent generalization.

### 8. Model Deployment

- Best model serialized to `pickle/model.pkl` using Python's `pickle` module
- Flask app loads it at startup, accepts URL input via POST, runs `FeatureExtraction`, and returns the prediction
- Deployed locally on `http://localhost:3000`

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────┐
│               User submits URL                    │
│             via browser (port 3000)               │
└────────────────────┬─────────────────────────────┘
                     │  POST /
                     ▼
┌──────────────────────────────────────────────────┐
│              Flask App  (app2.py)                 │
│   Receives form data, calls FeatureExtraction     │
└────────────────────┬─────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────┐
│        FeatureExtraction  (feature2.py)           │
│                                                  │
│  requests.get(url) → BeautifulSoup parse         │
│  whois.whois(domain) → domain age, reg length    │
│  socket.gethostbyname() → IP lookup              │
│  googlesearch.search() → Google index check      │
│                                                  │
│  Returns: numpy array of shape (1, 30)           │
└────────────────────┬─────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────┐
│   GradientBoostingClassifier (pickle/model.pkl)  │
│   max_depth=4  ·  learning_rate=0.7              │
└──────────┬───────────────────┬───────────────────┘
           │                   │
           ▼                   ▼
       1 = Safe           -1 = Phishing
           │                   │
           └─────────┬─────────┘
                     ▼
          Result rendered in index.html
```

---

## 📁 Project Structure

```
Identification-of-Phising-websites-and-URLs/
│
├── 📓 PhishingURLDett.ipynb    ← Full ML pipeline: EDA → 10 models → comparison → export
├── 🐍 app2.py                  ← Flask web app (entry point)
├── 🐍 feature2.py              ← FeatureExtraction class — all 30 features live here
├── 📊 phishing2.csv            ← 11,054 labelled URL samples from Kaggle
│
├── 📂 pickle/
│   └── model.pkl               ← Serialized Gradient Boosting Classifier
│
├── 📂 templates/
│   └── index.html              ← Frontend UI (Jinja2)
│
├── 📂 static/                  ← CSS, JS, image assets
└── 📋 requirements.txt         ← Python dependencies
```

---

## 🚀 Run Locally

```bash
# Clone
git clone https://github.com/gketan456/Identification-of-Phising-websites-and-URLs.git
cd Identification-of-Phising-websites-and-URLs

# Setup environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# Install dependencies
pip install flask beautifulsoup4 requests scikit-learn pandas numpy \
            python-whois googlesearch-python lxml catboost xgboost

# Run
python app2.py
```

Open **http://localhost:3000**, paste any URL, get an instant result.

---

## 🔮 Roadmap

- [ ] Browser extension for real-time detection while browsing
- [ ] REST API with confidence score / probability output
- [ ] VirusTotal API cross-validation layer
- [ ] Docker container + cloud deployment (Render / Railway)
- [ ] Retrain pipeline on live PhishTank data feed

---

## 📄 License

Licensed under the terms of the [LICENSE](LICENSE) file.

---

<div align="center">

Built by [@gketan456](https://github.com/gketan456)

⭐ Star the repo if it was useful

</div>
