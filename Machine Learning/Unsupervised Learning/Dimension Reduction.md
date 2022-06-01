PCA
[2.5. Decomposing signals in components (matrix factorization problems) — scikit-learn 1.1.1 documentation](https://scikit-learn.org/stable/modules/decomposition.html#principal-component-analysis-pca)

- intrinsic dimension

![[intrinsic dimension.png]]
- word frequency (tf-idf)
- ![[word_freq.png]]
=> sparse matrix
- PCA don't work with sparse matrix so we need to use `TruncatedSVD`


### Non-negative matrix factoriztion

- Recomendation system
- cosine similarity