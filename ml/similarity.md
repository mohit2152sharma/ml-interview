# Measures of Similarity

Measuring similarity (mostly between text strings) can be divided into three broad groups:
### Character based or sequence based measures (edit distance metrics)
- These measures compare two #strings at #character level and compute the minimum number of operations required to convert one #string into another. Operations are: insertion, deletion, substitution, transposition (swapping two adjacent characters). Common [[metrics]] are: 
	- #levenshtein: measures the minimum number of single character edits required to convert one string to another. Operations include: insertion, deletion, substitution. 
	- #damerau-levenshtein: Like #levenshtein but also includes transposition. Operations include: insertion, deletion, substitution, transposition.
	- #hamming: Counts number of differing characters at same position, works only when the two strings are of same length. 
	- #jaro: use the formula $distance = \frac{1}{3} \left( \frac{m}{|s_1|} + \frac{m}{|s_2|} + \frac{m - t}{m} \right)$ where $m$ is total number of matching characters (characters which are in both strings). $t$ is number of transpositions divided by 2. $s_i$ is the length of strings. Matching window is given by $\text{matching\ window} = \left\lfloor \frac{\max(|s_1|, |s_2|)}{2} \right\rfloor - 1$. Matching window is used to calculate matching characters. A character is considered match if it is within in matching window in both strings. If the jaro distance is zero that means no match and if it is 1 that means perfect match. 
	- #jaro-winkler: It builds on #jaro distance but gives more weight if the both string starts with same pattern. $d_w = d_j + \ell \cdot p \cdot (1 - d_j)$ where $d_j$ is jaro distance. $l$ is length of common prefix and $p$ is constant generally set to 0.1. 
	- #needleman-wunsch: it computes the global alignment. Used in bioinformatics for DNA analysis
	- #smith-waterman: it computes the local alignment. Used in bioinformatics for DNA analysis. It is useful for computing the most similar subsequences in two strings. 
### Token based measures (set based metrics)
These methods compare **sets of tokens (words, characters, n-grams)** and are insensitive to token order.
- #jaccard: Ratio of number of tokens that are there in both strings over total number of tokens. 
### Vector based measures
- #cosine: measures the angle between two vectors. Ranges between -1 to 1. 0: no [[similarity]], 1: identical. Suitable for #tf-idf, [[embeddings]], #sparse embedding space. 
- #euclidean: measures the straight line distance between two vectors. Lower value means more similar. Sensitive to vector length. Suitable for #dense embedding space.
- #manhattan: calculates the sum of absolute differences between coordinates of two vectors. Also known as #l1-norm. Lower means more similar. Suitable for #sparse, high dimensional spaces.


