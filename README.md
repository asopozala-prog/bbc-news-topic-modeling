

1. **BBC News Topic Modeling (NMF) – _first notebook_**  
   - **Data:** Raw BBC RSS dataset (titles + descriptions, 2013–2024, focused on 2022–2024).  
   - **Methods:** TF‑IDF → NMF (~10 topics), inspection of top words, topic assignment per article, topic counts and proportions by year (2022/2023/2024).  
   - **Key findings:** Clear topics for Ukraine war, Israel–Gaza war, cost of living, UK politics, quizzes, sports etc.; saw Ukraine coverage shrink while Gaza coverage surged in 2023–24, and compared this with Gemini’s external analysis.  
   - **Outputs:** Kaggle notebook + GitHub repo with a README summarizing the topic model and the shifting attention between conflicts.

2. **BBC Sentiment Classifier – _second notebook_**  
   - **Data:** Same BBC RSS data; built `sentiment` labels with VADER (NLTK).  
   - **Methods:** Turned VADER `compound` into labels (negative/neutral/positive), tightened thresholds (±0.6) to keep only strong positives/negatives, trained a `TfidfVectorizer + LogisticRegression` classifier.  
   - **Key findings:** Classifier reached ~95% accuracy on clear cases; we saw that VADER’s simple word-based sentiment sometimes disagrees with human/Gemini judgments on complex news; learned about label noise and threshold design.  
   - **Outputs:** Notebook and a short README section explaining the sentiment pipeline and its limitations.

3. **BBC Topic‑Feature Dataset Builder (NMF → clean CSV) – _third notebook_**  
   - **Data:** BBC RSS 2022–2024.  
   - **Methods:** NMF with 17 topics; computed topic weights, normalized to proportions; added dominance metrics: `max_topic_prop`, `dominant_topic`, `strongly_dominant`, `n_topics_prop_over_0_1`, `n_topics_to_cover_0_7`; filtered to “well‑aligned” articles (max ≥ 0.3); exported a clean feature table.  
   - **Key findings:** Many articles are clearly single‑topic; others need 2–3 topics to explain 70% of their content; we designed a careful dataset schema and a “topic dictionary” for all 17 topics.  
   - **Outputs:** New public Kaggle dataset **“BBC topic‑feature dataset”** (+ MIT license) and documentation in both Kaggle description and GitHub README.

4. **EDA on Topic Relations – isolated vs relational topics – _fourth notebook (on the new dataset)_**  
   - **Data:** `bbc_topics_features_clean.csv` (topic‑feature dataset).  
   - **Methods:** Analyzed strongly dominant articles by year and topic; counted most frequent 2‑topic pairs (`n_topics_to_cover_0_7 == 2`) and 3‑topic triples; built a per‑topic table: `single_strong_count`, `in_pairs_count`, `in_triples_count`, `pairs_plus_triples`; scatter plot of “strong alone vs in combinations”; heatmaps and bar charts.  
   - **Key findings:** Some topics (e.g. Ukraine war) are strong and relatively isolated; others (sports, politics, cost of living) appear frequently in mixed stories; we started thinking about “special topics” based on these statistics rather than names.  

5. **Ukraine‑like Article Detector (Decision Tree on topic features) – _fifth notebook_**  
   - **Data:** Topic‑feature dataset.  
   - **Methods:** Manually defined label `is_ukraine_like = strongly_dominant & (dominant_topic == 2)`; trained DecisionTreeClassifier with and without `topic_2_prop`, explored class imbalance, `class_weight="balanced"`, and cross‑validation; compared high‑recall vs high‑precision settings.  
   - **Key findings:** With balanced weights, the tree finds most Ukraine‑like articles (recall ≈ 0.93) but with many false positives; without balancing, it ignores the minority class; we learned how tree depth and class weighting trade recall vs precision on imbalanced data.

6. **Auto‑Discovered Special Topic Detector (full pipeline from raw text) – _sixth and most refined notebook_**  
   - **Data:** Raw BBC RSS 2022–2024 (no precomputed features).  
   - **Methods:**  
     - Rebuilt TF‑IDF + NMF(17) from scratch.  
     - Computed topic‑level metrics (strong dominance, pair/triple counts, year concentration) and combined them into a `special_score` to **automatically pick a “special” topic** (topic 2, without naming it).  
     - Defined `is_special` = strongly dominant and `dominant_topic == special_topic_id`.  
     - Trained a DecisionTreeClassifier on all 17 topic proportions to approximate `is_special`.  
     - Wrapped the whole thing into `predict_is_special(text)` that runs text → TF‑IDF → NMF → topic props → tree → True/False.  
   - **Key findings:** The auto‑scoring reliably selected the Ukraine‑war topic as “special”; the tree almost perfectly reproduces `is_special` on a held‑out set (≈100% accuracy, very high precision/recall), and manual inspection of predicted‑True articles showed only rare false positives.  
   - **Outputs:** Notebook `bbc_special_topic_detector_nmf_decisiontree.ipynb`, README section describing the end‑to‑end “special topic” detector.
