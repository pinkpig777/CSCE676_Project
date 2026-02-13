# CSCE676_Project
This is the project portfolio for TAMU's CSCE676 data mining course.
# Amazon Electronics Data Mining Project  
**CSCE 676 – Data Mining | Texas A&M University**

## Project Overview

This project analyzes the Amazon Electronics dataset to study how co-occurrence patterns and collaborative filtering methods perform in large-scale, highly sparse retail data.

The goal is to bridge:

- **Interpretable pattern mining** (Frequent Itemsets & Association Rules)
- **Predictive recommendation modeling** (Collaborative Filtering)

The dataset contains:

- 7.8 million ratings  
- 4.2 million users  
- 476,000 products  
- Sparsity: 0.999996  

This project investigates whether rule-based patterns can meaningfully support or enhance recommendation performance in real-world e-commerce data.

---

## Research Questions

1. How sensitive are association rules to support thresholds in long-tail retail data?
2. Do high-confidence rules primarily reflect popularity bias?
3. How does rule-based recommendation compare to collaborative filtering?
4. Can hybrid models improve predictive performance?

---

## Dataset

**Source:**  
Amaozn Product Reviews  
https://www.kaggle.com/datasets/saurav9786/amazon-product-reviews/data

**Category Used:** Electronics

**Data Structure:**
- userId
- productId
- rating (1–5)
- timestamp

---

## Project Structure
```
amazon-electronics-data-mining/
│
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_frequent_itemsets.ipynb
│   ├── 03_recommendation_model.ipynb
│   └── 04_hybrid_evaluation.ipynb
│
├── src/
│   ├── preprocessing.py
│   ├── rules.py
│   ├── recommender.py
│   └── evaluation.py
│
├── requirements.txt
└── README.md
```
---

## Phase 1 – Exploratory Data Analysis

Key findings:

- Extreme sparsity (0.999996)
- Long-tail distribution of users and products
- Strong rating inflation (dominance of 4–5 stars)
- Popularity bias in top products
- Bursty temporal review patterns

Implication:  
Standard dense modeling approaches are inappropriate. Support thresholds must be tuned carefully.

---

## Phase 2 – Frequent Itemsets & Association Rules

- Transaction baskets constructed per user
- FP-Growth used instead of naive Apriori
- Evaluated via:
  - Support
  - Confidence
  - Lift
  - Rule diversity

Goal:
Understand interpretability and popularity bias effects.

---

## Phase 3 – Recommendation Modeling

- Ratings converted to binary interaction (≥ 4 = positive)
- Collaborative filtering baseline
- Evaluation metrics:
  - Precision@K
  - Recall@K
  - Coverage
  - Long-tail exposure

---

## Phase 4 – Hybrid Integration

- Association rule confidence used as additional signal
- Compare hybrid vs pure collaborative filtering

---

## Technologies Used

- Python
- Pandas / NumPy
- Matplotlib / Seaborn
- mlxtend (FP-Growth)
- Surprise / implicit CF
- Sparse matrix modeling

---

## Key Technical Themes

- Sparse matrix analysis
- Long-tail distribution modeling
- Popularity bias evaluation
- Interpretability vs predictive trade-offs
- Hybrid recommender systems

---

## Portfolio Value

This project demonstrates:

- Large-scale data handling
- Association rule mining
- Sparse recommendation systems
- Model evaluation design
- Research-style hypothesis formulation

It is designed to reflect real-world recommendation system challenges encountered in e-commerce platforms.

---

## Future Extensions

- Graph embeddings (node2vec on user–product network)
- Temporal sequence modeling
- Debiasing strategies for popularity bias
- Transformer-based review embedding integration

---

## Author

Charlie Chiu  
Master of Computer Science  
Texas A&M University  
