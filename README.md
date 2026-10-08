# JoPACC Payment Data Analysis, Clustering, RAG & Graph Mining

## Overview

This project analyzes payment-system data from the Jordan Payments and Clearing Company (JoPACC) using multiple data mining and AI techniques.

The project includes:

- Clustering of structured payment-system data
- PCA for dimensionality reduction
- Text mining and Retrieval-Augmented Generation (RAG)
- Sentence embeddings and cosine-similarity retrieval
- Gemini-based answer generation
- Graph mining and centrality analysis

The goal was to explore patterns in payment activity, retrieve information from monthly financial reports, and analyze transitions in transaction activity over time.

## Dataset

The structured dataset contains:

- 214 records
- 49 features
- 5 payment systems
- 44 distinct report months
- Time period: November 2022 to June 2026

The five payment systems are:

- ACH
- ECCU
- CliQ
- JoMoPay
- eFAWATEERcom

Data validation found:

- 0 duplicate system-month records
- 0 missing values
- 0 invalid report-month values
- 0 negative numeric values

## Feature Engineering

To make the different payment systems comparable, three common relative features were used:

- Transaction count
- Transaction value
- Average transaction value

The features were standardized separately within each payment system.

This allowed values to represent whether a month had above-average or below-average activity relative to that specific payment system.

## PCA

Principal Component Analysis (PCA) was used to reduce dimensionality while preserving most of the information in the selected features.

A 90% explained-variance threshold was used instead of manually choosing the number of components.

PCA retained two principal components:

- PC1: approximately 74% of variance
- PC2: approximately 24% of variance
- Total explained variance: approximately 98.46%

This allowed the clustering results to be visualized in two dimensions while preserving almost all of the original information.

## Clustering

The following clustering algorithms were tested:

- K-Means++
- K-Means with random initialization
- Agglomerative / Hierarchical Clustering
- BIRCH
- DBSCAN
- Gaussian Mixture Model

For K-based clustering methods, values from k = 2 to k = 8 were tested.

The clustering algorithms were evaluated using:

- Silhouette Score
- Davies-Bouldin Index
- Calinski-Harabasz Index

## Clustering Results

K-Means++ and K-Means with random initialization produced the strongest overall clustering results.

The best Silhouette Score was approximately:

**0.4518**

The highest Calinski-Harabasz score was:

**234.13**

K = 5 produced the strongest clustering structure for most K-based models.

Agglomerative Clustering also performed well with a Silhouette Score of approximately 0.4321.

Gaussian Mixture achieved a Silhouette Score of approximately 0.4093.

DBSCAN performed the weakest on this dataset, producing:

- Silhouette Score: 0.0771
- Davies-Bouldin Index: 0.9811
- Calinski-Harabasz Score: 11.38
- 20 observations classified as noise

K-Means++ with five clusters was selected as the final clustering model because it achieved strong evaluation results, produced no noise observations, and assigned every month to an interpretable cluster.

## Text Mining and RAG

Six English-language JoPACC monthly reports from January to June 2026 were used for the text-mining and RAG component.

Each PDF was:

- Processed page by page
- Converted to text
- Cleaned to reduce formatting noise
- Divided into smaller chunks
- Stored with source metadata

The chunk metadata included:

- Report name
- Month
- Year
- Page number

A total of **522 text chunks** were created.

Each chunk was converted into a sentence embedding, producing an embedding matrix with shape:

**(522, 384)**

## Retrieval-Augmented Generation

Cosine similarity was used to retrieve the most relevant text chunks for a user's question.

The retrieved evidence was then provided to Gemini to generate answers grounded in the JoPACC reports.

### Example 1

**Question:**  
How many eFAWATEERcom users were there in June 2026?

The highest-ranked chunk came from the June 2026 report, page 63.

- Similarity score: 0.8164
- Retrieved value: 5.29 million users

### Example 2

**Question:**  
Did the value of eFAWATEERcom transactions increase or decrease in May 2026?

The correct evidence appeared in the third-ranked result.

- Report: May 2026
- Page: 68
- Similarity score: 0.8165
- Result: transaction value decreased by 16.5%

This example showed an important limitation of semantic retrieval: reports with very similar wording can receive very similar similarity scores even when they belong to different months.

### Example 3

**Question:**  
What was the number of ACH Jordanian Dinar transactions in March 2026?

The correct March 2026 evidence was ranked first.

- Similarity score: 0.8872
- Page: 74
- Result: 1.12 million transactions

## Graph Mining

A graph-mining analysis was also performed using monthly eFAWATEERcom transaction totals from July 2025 to June 2026.

The monthly transaction values were divided into four activity levels:

- Low
- Medium
- High
- Very High

A directed graph was created where each node represents an activity level and an edge represents a transition from one month's activity level to the next.

The graph was analyzed using:

- Degree centrality
- PageRank
- Closeness centrality
- Betweenness centrality

A weighted version of the graph was also created to represent repeated monthly transitions.

## Key Graph Findings

In the weighted graph:

- Low had the highest PageRank score: **0.2821**
- Low had an out-closeness score of **1**
- Very High had an in-closeness score of **1**
- Low and Very High had the highest betweenness centrality values

The analysis showed that payment activity moved between different levels rather than following a simple low-to-high pattern.

## Technologies Used

- Python
- Pandas
- NumPy
- Scikit-learn
- PCA
- K-Means
- DBSCAN
- BIRCH
- Agglomerative Clustering
- Gaussian Mixture Models
- Sentence Embeddings
- Cosine Similarity
- RAG
- Gemini
- NetworkX
- Matplotlib

## Key Findings

- Two PCA components preserved approximately 98.46% of the variance.
- K-Means++ with five clusters produced the strongest overall clustering results.
- RAG successfully retrieved relevant evidence from JoPACC reports.
- Similar wording across monthly reports sometimes reduced retrieval precision.
- Graph mining revealed how eFAWATEERcom activity transitioned between low, medium, high, and very high levels over time.

## Limitations

- The clustering dataset is relatively small.
- DBSCAN was not well suited to the structure of this dataset.
- Similar wording across monthly reports can cause retrieval systems to rank the wrong month highly.
- The RAG implementation used only six monthly reports.
- The graph contains only four activity-level nodes, so some centrality measures have limited variation.

- Experiment with other embedding models.
- Compare additional clustering methods.
- Build an interactive dashboard for payment trends, clustering, and RAG queries.
