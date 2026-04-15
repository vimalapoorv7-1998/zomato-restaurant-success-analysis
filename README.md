# 🍽️ Zomato Restaurant Success Analysis
### *What Makes a Restaurant Succeed in Bangalore, Mumbai & Delhi?*

---

## 📌 Project Overview

Food delivery is not just an industry in India — it is daily infrastructure. Zomato operates across 500+ cities and processes millions of orders every day. Yet most restaurant owners, brand managers, and food-tech analysts still rely on intuition rather than data when making decisions about pricing, menu strategy, and market entry.

This project answers a specific, high-value business question:

> **"What combination of pricing, cuisine type, menu strategy, and customer engagement separates successful restaurants from unsuccessful ones — and does that formula change across Bangalore, Mumbai, and Delhi?"**

The answer is not the same for all three cities. That finding is the core of this project.

---

## 🎯 Business Problem

A restaurant brand expanding across Indian metros cannot use a single playbook. What works in Delhi (premium pricing, menu restraint) actively fails in Mumbai (price-insensitive, engagement-driven). This project builds the data foundation for city-specific restaurant strategy by:

- Defining a rigorous, dual-condition success metric grounded in both quality and demand
- Testing six specific hypotheses about what drives success
- Delivering city-level recommendations backed by statistical evidence
- Building an interactive Power BI dashboard for ongoing business exploration


---

## 🔧 Tech Stack & Tool Division

This project intentionally divides work between two tools based on what each does best — not convenience.

| Layer | Tool |
|-------|------|
| Data Cleaning | Python (pandas) |
| Statistical Testing | Python (scipy) |
| EDA & Business Charts | Python (matplotlib, seaborn) |
| Interactive Dashboard | Power BI |
| DAX Measures | Power BI |

---

## 📊 Dataset

- **Source:** [https://www.kaggle.com/datasets/narsingraogoud/zomato-restaurants-dataset-for-metropolitan-areas]
- **Raw size:** 123,657 rows × 12 columns
- **Scope after cleaning:** 22,547 item-level rows across 190 restaurants in 3 cities
- **Granularity:** One row per menu item (aggregated to restaurant level for analysis)

**Columns:**
| Column | Description |
|--------|-------------|
| `Restaurant Name` | Name of the restaurant |
| `City` | City of operation |
| `Cuisine` | Primary cuisine category |
| `Dining Rating` | Dine-in rating (2.5–4.8, 26% missing for delivery-only) |
| `Delivery Rating` | Delivery rating (2.5–4.6) — primary success metric |
| `Dining Votes / Delivery Votes` | Vote counts per channel |
| `Item Name` | Menu item name |
| `Prices` | Item price in ₹ |
| `Best Seller` | Promotional tag (BESTSELLER, MUST TRY, CHEF'S SPECIAL, etc.) |

---

## 🧹 Data Cleaning — Key Decisions

Every cleaning step was discovered through audit, not assumed. Nine issues were found and fixed:

| Issue Found | Fix Applied | Reason |
|------------|-------------|--------|
| `'Cuisine '` column had trailing space | Stripped column names | Silent KeyError on every groupby |
| All City values had leading space (`' Mumbai'`) | Stripped string values | df[df['City']=='Mumbai'] returned 0 rows |
| Banaswadi, Ulsoor, Magrath Road, Malleshwaram listed as cities | Remapped to 'Bangalore' | Each contained exactly 1 restaurant — confirmed neighbourhoods |
| 'New Delhi' inconsistent naming | Standardised to 'Delhi' | City groupby split into two groups |
| 22,127 fully identical rows | `drop_duplicates()` | Confirmed scraper artifact — every column matched |
| ~5,500 same item, different Best Seller tag | Priority-based dedup (BESTSELLER > MUST TRY > CHEF'S SPECIAL) | Zomato's scraper captured same item from multiple menu sections |
| Dining Rating — 26.1% missing | Flagged with sentinel (-1), boolean indicator added | Delivery-only restaurants genuinely have no dine-in rating — imputing would fabricate data |
| Delivery Rating — 1.0% missing | Filled with City × Cuisine median | Context-aware fill; global mean ignores city/cuisine differences |
| Price outliers (min ₹0.95, max ₹12,024) | Per-city 99th percentile cap + remove < ₹5 | City-level cap respects Mumbai's legitimately higher price ceiling |

---

## 🎯 Success Metric Definition

This is the most important analytical decision in the project.

**Why not rating alone?** A ghost kitchen with 3 orders and a 4.5 rating is not a business success — it is a small sample artifact.

**Why not votes alone?** A restaurant processing thousands of orders at a 3.5 rating is not sustainable.

**Chosen: Dual Condition**

```
is_successful = 1  IF:
    delivery_rating >= 4.0          ← quality threshold (Zomato's own "good" benchmark)
    AND
    engagement >= city 60th pct     ← top 40% of demand, measured WITHIN each city
```

City-relative engagement threshold ensures Delhi restaurants are not unfairly penalised against Mumbai's larger user base.

**Result:** 41 of 190 restaurants (21.6%) classified as successful.

---

## 📈 Analysis Structure

### Chapter 3 — Exploratory Data Analysis (4 charts)

| Chart | Question Asked |
|-------|---------------|
| EDA-1: City Overview | What is the baseline scale, quality, and pricing? |
| EDA-2: Rating Distributions + ANOVA | Are city rating profiles statistically different? |
| EDA-3: Price Distributions | Is Mumbai genuinely premium or just more variable? |
| EDA-4: Top Cuisines by City | Do cities have different cuisine ecosystems? |

### Chapter 4 — Business Analysis (6 hypotheses tested)

| Analysis | Hypothesis | Statistical Test |
|----------|-----------|-----------------|
| A: Price vs Success | Successful restaurants charge more | Mann-Whitney U (non-parametric, prices are right-skewed) |
| B: Cuisine vs Success | Winning cuisine differs by city | Success rate by cuisine group |
| C: Price vs Rating | Higher price → higher rating | Pearson r + OLS regression |
| D: Menu Strategy | Menu size and promo ratio differ by success | Scatter with group centroids |
| E: Bestseller Label | BESTSELLER tag drives demand and pricing | Item-level grouped comparison |
| F: City DNA Radar | Synthesis of all 5 dimensions | Normalised radar chart |

---

## 🔑 Key Findings

### Bangalore — The Balanced Market
- 21% success rate (18/86 restaurants)
- Successful restaurants charge **₹51 more** than unsuccessful ones
- **Rajasthani cuisine** leads success rate — a niche that outperforms dominant categories
- Promotion strategy: slight advantage from promoting (~21% of menu) — guided discovery works

### Mumbai — The Volume Game
- 24% success rate (19/80 restaurants) — most competitive city
- Price premium of successful restaurants: only **₹1** — pricing does not separate winners
- **Mexican cuisine** leads — differentiation beats market dominance
- Winners **promote less** (9.6% vs 12.5%) — restraint signals confidence

### Delhi — The Premium Market
- 17% success rate (4/24 restaurants) — most selective
- Successful restaurants charge **₹107 more** — the largest premium gap in the dataset
- **Desserts** lead success rate — experience-driven over functional dining
- Winners **promote far less** (13.0% vs 13.6%) — over-promotion is a negative signal here

### Cross-City Pattern
> In all three cities, the top-performing cuisine is NOT the most common cuisine. **Differentiation consistently beats dominance.** Restaurants occupying less-crowded niches outperform those competing in saturated categories.

---

## 📊 Power BI Dashboard

The dashboard has 5 pages, each answering a distinct business question:

| Page | Business Question |
|------|------------------|
| 1. Executive Overview | What is the bottom line across all cities? |
| 2. Price Analysis | Does pricing strategy drive success — differently per city? |
| 3. Cuisine Intelligence | Which food categories win where? |
| 4. Menu Strategy | Does menu size and promotion mix matter? |
| 5. City Fingerprints | What is the full success DNA of each city? |

**DAX Measures built:** Success Rate, Price Premium of Success, Rating vs City Average (using ALLEXCEPT), Promoted Item Share, Dynamic Page Title, Budget/Mid-Range/Premium Share, Avg Items Successful vs Unsuccessful.

See `reports/Zomato_Restaurant_Analysis.pdf` for the exported dashboard.


---

## 👤 Author

**[Apoorv Vimal]**
  [https://www.linkedin.com/in/apoorv-vimal-analytics/]
  [vimalapoorv08@gmail.com]

---
