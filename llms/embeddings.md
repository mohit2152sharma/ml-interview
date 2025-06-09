# Embeddings

- Embeddings are learned during training process of a neural network model. Pre-trained embeddings also exists like GloVE, seq2seq etc
- Embedding matrices (or embedding layer) is essentially a lookup table. Each row is a word and the columns are embedding dimensions.
  - The number of rows in embedding matrix is equal to the number of tokens you have in your corpus, i.e. vocabulary size.
  - The number of columns is a hyperparameter it is known as embedding size or embedding dimension and it is usually tuned during training process.
  - The matrix is initialized by random values and during training the values are updated. They are treated as model parameters (weights) and gets updated during training via back propagation. The model learns to adjust embedding values such that the words which are contextually similar gets pulled closer.
