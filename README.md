# Large-Scale Pattern Mining on Amazon Electronics Ratings

## Overview

This project analyzes the Amazon Electronics ratings dataset to study how large-scale user-product interaction data can be mined for behavioral patterns under real-world constraints: extreme sparsity, rating inflation, popularity bias, and scale. The dataset contains more than 7.8 million ratings, but most users interact with only a small number of products, making naive pairwise comparison and standard association rule mining difficult.

The project builds a layered data mining pipeline using Spark, Bloom Filters, MinHash LSH, FP-Growth, PrefixSpan, bipartite graph analysis, graph embeddings, anomaly detection, and interpretability tools. The goal is not only to find frequent products, but also to understand how different mining views, including co-occurrence, temporal ordering, and graph centrality, reveal different aspects of the same product ecosystem.

👉 **Start here:** [`main_notebook.ipynb`](main_notebook.ipynb)

## Project Video

🎥 **Project video:** [Add your Google Drive / YouTube / Canvas-accessible video link here]

Make sure the video link is accessible to the instruction team before submitting. Test it in an incognito window, because private links are where good submissions go to die quietly.

## Research Questions

This project investigates three research questions:

### RQ1. How do support thresholds affect frequent itemsets and association rules?

This question studies how changing the minimum support threshold changes the number and quality of discovered frequent itemsets and association rules.

- **Task type:** Frequent itemset mining and association rule mining
- **Algorithms:** FP-Growth
- **Evaluation:** Number of frequent itemsets, number of association rules, support, confidence, lift, and interpretability

### RQ2. Are discovered product patterns dominated by popularity bias?

This question examines whether the strongest patterns are mostly driven by a small set of highly popular products rather than diverse product relationships.

- **Task type:** Association rule analysis and popularity-bias analysis
- **Algorithms:** FP-Growth, graph centrality analysis
- **Evaluation:** Product frequency, PageRank, itemset overlap, rule count, and product diversity

### RQ3. Do temporal sequences reveal patterns missed by unordered itemsets?

This question compares unordered frequent itemsets with temporally ordered product sequences to test whether user interaction order adds useful structure.

- **Task type:** Sequential pattern mining
- **Algorithms:** PrefixSpan
- **Evaluation:** Sequential support, sequence length distribution, gateway-product candidates, and overlap with frequent itemsets

## Dataset

### Dataset Used

This project uses the **Amazon Electronics Ratings Dataset**, where each row represents one user-product interaction:

```text
userId, productId, rating, timestamp
```

The dataset used in the notebook is expected to be placed at:

```text
/content/drive/MyDrive/TAMU/CSCE676/data/ratings_Electronics.csv
```

The original compressed file may be downloaded from the Amazon product review datasets commonly used in recommender-system and data-mining research. Because the raw dataset is large, it is **not committed directly to this repository**.

### Data Fields

| Column | Description |
|---|---|
| `userId` | Anonymized user identifier |
| `productId` | Amazon product identifier |
| `rating` | User rating from 1 to 5 |
| `timestamp` | Unix timestamp of the interaction |

### Preprocessing

The notebook applies the following preprocessing decisions:

1. Ratings are converted into binary positive interactions:

   ```python
   rating >= 4 => positive = 1
   rating < 4  => positive = 0
   ```

2. Invalid or incomplete rows are removed.
3. User baskets are built from positively rated products.
4. Sequential histories are ordered by timestamp.
5. Large-scale operations are handled with Spark to avoid loading the entire dataset into local pandas memory.
6. For expensive graph algorithms, a sampled bipartite graph is used for tractability.

## Methods

The project combines course techniques and external extensions:

| Component | Purpose |
|---|---|
| Spark | Load and process 7.8M+ interactions at scale |
| Bloom Filter | Probabilistic defense layer for duplicate/spam-style filtering |
| MinHash LSH | Reduce pairwise user similarity search space |
| FP-Growth | Mine unordered frequent product itemsets |
| PrefixSpan | Mine temporally ordered product sequences |
| Bipartite Graph Analysis | Model users and products as a user-product graph |
| PageRank / HITS | Identify structurally central products and users |
| node2vec / Spectral Fallback | Learn dense graph-based node representations |
| Isolation Forest | Detect structurally unusual users |
| Shallow Decision Tree | Explain anomaly-detection pseudo-labels |

## Results Summary

This project analyzed **7,824,482 Amazon Electronics ratings** and converted them into **5,833,322 positive interactions** using `rating >= 4`. About **74.6%** of ratings were positive, confirming strong rating inflation.

The basket-size distribution showed extreme sparsity. There were about **3.26M users with positive interactions**, but the median positive basket size was only **1 product**, and the mean was about **1.79 products**. This makes naive co-occurrence mining difficult because most users do not provide enough product history to form rich baskets.

The strongest scalability result came from **MinHash LSH**. On a sample of **50,000 users**, brute-force comparison would require about **1.25B user pairs**. LSH reduced this to about **351,804 candidate pairs**, a reduction of roughly **99.97%**.

FP-Growth showed that discovered itemsets are highly sensitive to support thresholds:

| minSupport | Frequent Itemsets | Association Rules |
|---:|---:|---:|
| 0.001 | 213 | 2 |
| 0.002 | 47 | 0 |
| 0.005 | 9 | 0 |
| 0.010 | 0 | 0 |

This confirms that meaningful co-review patterns exist, but only at low support thresholds. Higher thresholds quickly remove nearly all patterns because the dataset is sparse.

PrefixSpan found **212 frequent sequential patterns** on a 50,000-user sample. However, the strongest patterns were dominated by popular products such as `B007WTAJTO`, `B0074BW614`, `B003ES5ZUU`, and `B0019EHU8G`. This suggests that temporal mining adds information, but the strongest sequence signal is still shaped heavily by popularity bias and short user histories.

The bipartite graph analysis used a sampled user-product graph with **142,525 nodes** and **100,000 edges**. Top PageRank products included `B0074BW614`, `B00DR0PDNE`, `B007WTAJTO`, and `B0019EHU8G`. These products also appeared in frequent itemset and sequential mining results, suggesting that a small group of head products dominates frequency, temporal patterns, and structural centrality.

The graph embedding extension attempted node2vec with 64-dimensional embeddings. In Colab, node2vec may fall back to spectral graph embeddings if multiprocessing or serialization issues occur. Either way, the extension provides dense structural representations for downstream analysis. Isolation Forest then flags structurally unusual users using graph-derived features such as out-degree, hub score, and PageRank. The shallow decision tree explains these pseudo-labels, but this should be interpreted as structural outlier detection, not confirmed fraud detection.

## Big Takeaway

The main takeaway is that the Amazon Electronics dataset is not just large. It is **large, sparse, rating-inflated, and popularity-skewed**.

Frequent itemset mining, sequential pattern mining, and graph analysis all point to the same pattern: a small set of highly popular products dominates the observable structure. FP-Growth can recover co-review patterns, PrefixSpan can add temporal context, and PageRank can identify structurally central products, but all three methods must be interpreted carefully because popularity bias is extremely strong.

The most successful contribution of the project is the scalable layered pipeline:

- Spark handles the full interaction dataset.
- Bloom Filter provides a proposed probabilistic defense layer.
- MinHash LSH reduces pairwise comparison cost by about 99.97%.
- FP-Growth and PrefixSpan extract unordered and ordered product patterns.
- Bipartite graph analysis connects frequency with structural authority.
- Graph embeddings and Isolation Forest extend the project toward anomaly detection.
- A shallow decision tree provides an interpretable explanation layer.

Overall, the project shows that large-scale product mining is less about running one algorithm and more about building a pipeline that can clean, reduce, mine, compare, and interpret sparse interaction data.

## How to Reproduce

This project was developed in **Google Colab**.

### 1. Prepare the data

Place the extracted Amazon Electronics ratings CSV here:

```text
/content/drive/MyDrive/TAMU/CSCE676/data/ratings_Electronics.csv
```

If your file has a different name, update `DATA_DIR` or `CSV_PATH` in `main_notebook.ipynb`.

### 2. Open the notebook

Open:

```text
main_notebook.ipynb
```

Run the notebook from top to bottom. The first setup cell will:

- Mount Google Drive
- Install dependencies
- Start Spark
- Configure output paths

### 3. Export requirements

The final cell exports the Colab environment:

```python
!pip freeze > requirements.txt
from google.colab import files
files.download("requirements.txt")
```

Commit the exported `requirements.txt` to the repository root.

## Key Dependencies

The notebook was built in Google Colab. Main packages include:

| Package | Purpose |
|---|---|
| Python 3.x | Main runtime |
| PySpark 3.5.5 | Distributed data processing and mining |
| pandas | Tabular data handling |
| NumPy | Numeric operations |
| matplotlib | Visualization |
| scikit-learn | Isolation Forest and decision tree |
| NetworkX | Graph construction and centrality |
| node2vec | Graph embedding extension, with fallback if unavailable |

The full package list is stored in:

```text
requirements.txt
```

## Repository Structure

```text
.
├── README.md
├── main_notebook.ipynb
├── requirements.txt
├── checkpoints/
│   ├── checkpoint_1.ipynb
│   └── checkpoint_2.ipynb
├── assets/
│   ├── basket_size_distribution_log.png
│   ├── support_vs_pattern_count.png
│   ├── top10_product_pagerank.png
│  └── decision_tree_interpretability.png
```

The raw Amazon Electronics dataset is too large to commit. The `data/README.md` file should explain where to download it and where to place it locally or in Google Drive.

## Notes on Limitations

This project has several important limitations:

1. **Rating inflation:** Since most ratings are positive, `rating >= 4` is useful but still imperfect as a signal of preference.
2. **Extreme sparsity:** Most users have very short histories, limiting the strength of basket and sequence mining.
3. **Popularity bias:** The strongest frequent itemsets, sequential patterns, and PageRank results are dominated by head products.
4. **Sampled graph analysis:** PageRank, HITS, embeddings, and anomaly detection are run on a sampled bipartite graph for tractability.
5. **Anomaly labels are pseudo-labels:** Isolation Forest identifies structural outliers, not confirmed fake reviewers or bots.
6. **node2vec runtime stability:** In Colab, node2vec may fail due to multiprocessing serialization issues. The notebook includes a spectral embedding fallback to preserve the extension.
