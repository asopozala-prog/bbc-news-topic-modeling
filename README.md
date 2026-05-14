# bbc-news-topic-modeling
Topic modeling BBC News RSS with TF-IDF + NMF (Kaggle notebook)
### Summary – BBC News Topic Modeling (NMF)

- Loaded ~42k BBC RSS articles (2013–2024) and built TF‑IDF features from title + description.  
- Trained a 10‑topic NMF model and interpreted topics as Ukraine war, Israel–Gaza war, UK politics, cost of living, crime, sports, quizzes, etc.  
- Assigned each article a dominant topic, then analyzed topic distributions overall and by year (2022–2024).  
- Found that Ukraine‑war coverage declines over time while Israel–Gaza coverage spikes in 2023 and remains high in 2024.  
- Compared these patterns with external information (via Gemini) and saw good alignment between model‑derived topics and real‑world news shifts.
## Checking machine–human alignment

One key learning in this project was **how to compare the model’s topic assignments with human intuition**.

For each article, NMF produces a vector of topic weights (how much each topic contributes).  
For example, for the article titled:

> **"Ukraine war: PM to hold talks with world leaders"**

the model produced (simplified):

- Topic 2 (Ukraine war): weight ≈ 0.051  
- Topic 9 (UK politics / government): weight ≈ 0.054  

The dominant topic by `argmax` is **topic 9**, even though the title clearly mentions “Ukraine war”.  
When I look at this as a human, the article is actually **about the UK Prime Minister’s diplomatic role in the war**, so it is reasonable to see it as a **mixture** of “Ukraine war” and “UK politics”.

This example taught me that:
- Topic models do not produce a single “true” label; they produce **mixtures of themes**.  
- A “misassignment” often reveals an article that genuinely sits between two human categories.  
- Checking **one article at a time**, with its text and topic weights, is a good way to study the alignment (or tension) between **machine perception** and **human perception** of topics.
## Shifting attention: Ukraine vs. Gaza (2022–2024)

Using NMF topics on BBC RSS, I focused on two conflict‑related topics:

- **Topic 2 – Russia–Ukraine war**
- **Topic 6 – Israel–Gaza war**

From the notebook counts:

| Year | Ukraine (topic 2) | Gaza (topic 6) |
|------|-------------------|----------------|
| 2022 | 1189              | 121            |
| 2023 |  686              | 819            |
| 2024 |  491              | 518            |

This shows a **clear shift**: Ukraine dominates in 2022, Gaza “catches up and overtakes” in 2023, and by 2024 both conflicts are covered but Gaza remains the primary focus.

I then asked Gemini (with web access) to analyze BBC / UK media attention across these years.  
It summarized densities as:

- **2022:** Ukraine = *Extreme (Primary)*, Gaza = *Low*  
- **2023:** Ukraine = *High (Steady)*, Gaza = *Extreme (Post‑Oct)*  
- **2024:** Ukraine = *Moderate (Secondary)*, Gaza = *Extreme (Primary)*  

The qualitative story from Gemini aligns well with the **quantitative topic counts** from my model:  
Ukraine starts as the central conflict, then gradually becomes secondary as Gaza coverage spikes in 2023 and stays very high in 2024.
