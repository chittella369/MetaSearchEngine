# Meta Search Engine

This repository contains the implementation of a basic **Metasearch Engine (MSE)** designed to aggregate, rank, and evaluate search results from multiple search engines for any user-defined query/company (e.g., **Amazon, Tesla, SpaceX, Nvidia, Apple Inc.**) across specific categories: **Technology and Business**.

---

## 📋 Problem Statement

Web search engines return different sets of results with varying rankings for the same query. A **Metasearch Engine (MSE)** aims to query multiple search engines, aggregate the returned documents, filter duplicates, and apply ranking algorithms to present a unified, high-quality ranked list to the user.

In this assignment, the goals are:
1. **Dynamic Document Extraction**: Accept a dynamic company name query from the user, create a dedicated subdirectory for it, and scrape the top 20 news results from **Google News** and **Bing News** for the query within a specific date range.
2. **Aggregated Collection**: Consolidate results across search engines and categories, remove duplicates based on document titles, and assign a unique document ID to each distinct article.
3. **Document Ranking**:
   - **Approach 1 (Rank-Merge)**: Rank aggregated documents by merging their original search ranks, giving Google results priority in case of ties.
   - **Approach 2 (Content-Relevance)**: Rank documents using a term frequency-based scoring function over the query terms:
     $$\text{Score}(q, d) = \prod_{q_i \in q} \left(0.5 \times \frac{\text{Term Frequency}(q_i, d)}{\text{Length}(d)} \times \alpha\right)$$
4. **Evaluation**: Compute **Precision@K** (where $K \in \{5, 10, 15, 20, 25, 30\}$) and **Mean Average Precision (MAP)** for both approaches, plotting their comparison curves.

---

## 🧮 Ranking Approaches

The Metasearch Engine employs two different methodologies to rank the aggregated search results:

### 1. Approach 1: Rank-Merge (CombMNZ / Search Position Fusion)
This approach leverages the rank scores assigned by the primary search engines (Google and Bing). It assumes that the search position on industrial search engines is a strong indicator of document quality.
* **Mechanism**: Merges the results based on their original search ranks.
* **Tie-Breaking**: If a document has the same rank across both search engines or category results, the rank assigned by **Google News** is given priority to break the tie.

### 2. Approach 2: Content-Relevance (Term Frequency Model)
This approach ignores the primary search engine ranks and scores documents purely based on their textual content (title + snippet) using a normalized term frequency product over the query terms.
* **Relevance Scoring Formula**:
  $$\text{Score}(q, d) = \prod_{q_i \in q} \left(0.5 \times \frac{\text{Term Frequency}(q_i, d)}{\text{Length}(d)} \times \alpha\right)$$
  Where:
  * $q_i$: Individual term in the query $q$ (e.g. for query `'Amazon'`, the terms are `'amazon'`).
  * $\text{Term Frequency}(q_i, d)$: Occurrences of the query term $q_i$ in the document's cleaned title + snippet.
  * $\text{Length}(d)$: Total count of space-separated words in the document text.
  * $\alpha$: Scaling parameter (set to $2$).
* **Mechanism**: If any query term is absent in the document text, the overall score falls to $0.0$. Documents are sorted in descending order of their computed score.

---

## 🛠️ Repository Structure

* **`MetaSearchEngine/`**:
  * [MetaSearchEngine.ipynb](file:///Users/aditya/Downloads/M.Tech%20Assignments/IR%20Assignment%201%20-%20Group%2072/MetaSearchEngine/MetaSearchEngine.ipynb): Main Jupyter notebook containing the full implementation of scraping, ranking, plotting, and evaluation.
  * [MetaSearch-Approach.txt](file:///Users/aditya/Downloads/M.Tech%20Assignments/IR%20Assignment%201%20-%20Group%2072/MetaSearchEngine/MetaSearch-Approach.txt): Brief overview of class functions and project dependencies.
  * **`[company_name]/`**: Directory dynamically created for the query (e.g., `Amazon`, `Tesla`, `SpaceX`, `Nvidia`) containing:
    * `[company_name]_google_tech.txt`, `[company_name]_bing_tech.txt`: Scraped raw results for the Technology category.
    * `[company_name]_google_business.txt`, `[company_name]_bing_business.txt`: Scraped raw results for the Business category.
    * `RankedDocuments.txt`: Consolidated unique documents with duplicates removed.
    * `[company_name]_ResultantRanks_A1.txt` & `[company_name]_ResultantRanks_A2.txt`: Ranked results under Approach 1 and Approach 2.
* **`AIM-II/`**:
  * `AIM-II-Information Retrieval.docx`: Documentation/report regarding the second part of the assignment.

---

## 📊 Findings & Discussion

### Evaluation Metrics Comparison

| Metric | Approach 1 (Rank-Merge) | Approach 2 (Content-Relevance) |
| :--- | :---: | :---: |
| **Precision@5** | 0.60 | 0.00 |
| **Precision@10** | 0.60 | 0.30 |
| **Precision@15** | 0.53 | 0.27 |
| **Precision@20** | 0.50 | 0.45 |
| **Precision@25** | 0.48 | 0.40 |
| **Precision@30** | 0.67 | 0.67 |
| **Mean Average Precision (MAP)** | **0.56** | **0.30** |

### Key Observations & Insights

1. **Superiority of Rank-Merge (Approach 1)**:
   - **Approach 1** achieved a significantly higher Mean Average Precision (**0.56**) compared to **Approach 2** (**0.30**).
   - This occurs because commercial search engines (Google and Bing) utilize sophisticated ranking signals beyond basic text matching—such as hyperlink structures (PageRank), click logs, domain authority, and user engagement. Merging these pre-computed ranks preserves this underlying quality.

2. **Limitations of Basic Term Frequency (Approach 2)**:
   - The custom formula in **Approach 2** is based entirely on the raw term frequencies of the query terms (`apple` and `inc`). This model is highly susceptible to vocabulary mismatches and document lengths.
   - For example, at $K=5$, Approach 2 had a precision of **0.00**, showing that documents containing highly frequent query terms in a short title/snippet were not necessarily the most relevant or high-quality articles. 

3. **High-K Convergence**:
   - At $K=30$, both approaches converge to a precision of **0.67**. Since the total pool of retrieved documents is relatively small and finite (36 unique documents), as we retrieve more documents ($K$ increases), the subset of relevant documents retrieved by both methods becomes similar.

---

## 🚀 How to Run the Code (Without Errors)

### 1. Prerequisites
Ensure you have Python 3 installed. Install the required external libraries using `pip`:

```bash
pip install numpy pandas matplotlib beautifulsoup4 requests python-dateutil
```

### 2. Robust Query Execution & Local Fallback
> [!IMPORTANT]
> - **IP & Scraper Volatility**: Live search engine scraping is highly volatile due to CAPTCHAs and structural HTML changes. If live scraping fails or yields 0 results, the notebook fallback mechanism loads pre-cached results for the company.
> - **Pre-Cleaned Queries**: The user input query is pre-cleaned using the text normalization functions prior to TF relevance scoring in Approach 2. This ensures correct term-matching and generates distinct precision lines for both ranking algorithms.

### 3. Step-by-Step Instructions
1. Open a terminal and navigate to the `MetaSearchEngine` folder (where the assignment code files are located).
2. Start the Jupyter Notebook interface:
   ```bash
   jupyter notebook
   ```
3. Open the notebook **`MetaSearchEngine.ipynb`**.
4. Run the cells. When prompted at the cell:
   ```python
   company_name = input("enter the company name : ")
   ```
   Enter the desired company name (e.g. `Amazon`, `Tesla`, `SpaceX`, `Nvidia`, `Cerebras`, or `Apple Inc.`).
5. A subdirectory named after the entered company will be automatically created (if not already present) to store the scraped raw text files, deduplicated documents, and final ranked lists.
6. The notebook will generate the comparison plot for both ranking approaches and print their computed Mean Average Precision (MAP) scores.
