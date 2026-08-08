## Information Retrieval using MetaSearchEngine ##

This repository contains the implementation of a basic **Metasearch Engine (MSE)** designed to aggregate, rank, and evaluate search results from multiple search engines for the query **"Apple Inc."** across three categories: **Technology, Sci/Tech, and Business**.

I have selected two news search engines 'Google news' and 'Bing' and aggregated the results from these two search engines for the above mentioned query

## 📋 Problem Statement

Web search engines return different sets of results with varying rankings for the same query. A **Metasearch Engine (MSE)** aims to query multiple search engines, aggregate the returned documents, filter duplicates, and apply ranking algorithms to present a unified, high-quality ranked list to the user.

In this assignment, the goals are:
1. **Document Extraction**: Scrape the top 20 news results from **Google News** and **Bing News** for the query `"Apple Inc."` within a specific date range.
2. **Aggregated Collection**: Consolidate results across search engines and categories, remove duplicates based on document titles, and assign a unique document ID to each distinct article.
3. **Document Ranking**:
   - **Approach 1 (Rank-Merge)**: Rank aggregated documents by merging their original search ranks, giving Google results priority in case of ties.
   - **Approach 2 (Content-Relevance)**: Rank documents using a term frequency-based scoring function over the query terms:
     $$\text{Score}(q, d) = \prod_{q_i \in q} \left(0.5 \times \frac{\text{Term Frequency}(q_i, d)}{\text{Length}(d)} \times \alpha\right)$$
4. **Evaluation**: Compute **Precision@K** (where $K \in \{5, 10, 15, 20, 25, 30\}$) and **Mean Average Precision (MAP)** for both approaches, plotting their comparison curves.

---

## 🛠️ Repository Structure

* **`MetaSearchEngine/`**:
  * [MetaSearchEngine.ipynb](file:///Users/aditya/Downloads/M.Tech%20Assignments/IR%20Assignment%201%20-%20Group%2072/AIM-I/MetaSearchEngine.ipynb): Main Jupyter notebook containing the full implementation of scraping, ranking, plotting, and evaluation.
  * [MetaSearch-Approach.txt](file:///Users/aditya/Downloads/M.Tech%20Assignments/IR%20Assignment%201%20-%20Group%2072/AIM-I/MetaSearch-Approach.txt): Brief overview of class functions and project dependencies.
  * `Apple Inc._*.txt`: Text files containing the extracted raw search results.
  * `RankedDocuments.txt`: Consolidated unique documents.
  * `ResultantRanks_A1.txt` & `ResultantRanks_A2.txt`: Ranked documents using Approach 1 and Approach 2.

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

### 2. Error-Free Execution Design (Fallback Scraper Mechanism)
> [!IMPORTANT]
> Live search engine scraping is highly volatile because of dynamically changing HTML structures, IP rate limits, and CAPTCHAs. 
> To guarantee **error-free execution**, we have built a **robust fallback mechanism** into the notebook:
> - If live scraping of Google News/Bing News returns 0 results (due to blocks or changed selectors), the code automatically detects this and loads the cached data from the local text files (e.g., `Apple Inc._google_tech.txt`).
> - It reconstructs all required DataFrame columns transparently so that the ranking, plotting, and evaluation cells can run smoothly without any `KeyErrors` or missing variables.
> - The query is also pre-cleaned to ensure correct term-matching in Approach 2, preventing identical line overlaps on the precision plot.

### 3. Step-by-Step Instructions
1. Open a terminal and navigate to the `AIM-I` folder (where the assignment code files are located).
2. Start the Jupyter Notebook interface:
   ```bash
   jupyter notebook
   ```
3. Open the notebook **`MetaSearchEngine.ipynb`**.
4. In the Jupyter toolbar, select **Kernel** -> **Restart & Run All Cells**.
5. The notebook will run all cells sequentially, generate the comparison plot for both ranking approaches, and output the computed MAP scores.
