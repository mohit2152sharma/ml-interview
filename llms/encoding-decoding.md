# Encoding and Decoding

- Encoding is a process of converting raw input data (text, images) into a structured numerical representation that a machine learning model can understand.
- Tokenization is the process of converting the input document into tokens or units. These tokens (or atomic units) are then converted to numbers (which is called encoding). For example: "cats are better than dogs" this is an input sentence, a tokenizer (word based tokenizer) will split this into an array of words `["cats", "are", "better", "than", "dogs"]`.
- Different types of tokenizer (tokenization algorithms):

  - Word Tokenization (or whitespace tokenization): Split text at whitespaces or punctuation marks. May fail with out of vocabulary errors.
  - Character level tokenization: split texts at character. No out of vocabulary errors.
  - Byte pair encoding: Starts with individual characters as tokens and iteratively keep on merging most frequent adjacent pairs into new tokens until you reach the desired vocabulary size. This algorithm is very popular and used by GPT models
  - Wordpiece tokenization: Similar to BPE, it starts with a vocabulary of individual characters but instead of using counting frequencies as merging strategy for subwords it uses likelihood ratio which measures how often these token appear together versus separately. This algorithm is used by models like BERT, RoBERT, DistillBERT etc

  1. Is BPE tokenization algorithm better than Word Tokenization algorithm?

  - Word tokenization is simple. It's fast and very easy to implement. But it suffers with very high rate of out of vocabulary error. Out of vocabulary error happens when you encounter a word that is not available in the vocabulary. For example, during the training phase, your vocabulary included words like `happiness, sad, sadness` but while testing if it encounters the word `happy` it will fail.
  - BPE algorithm has low out of vocabulary error, it is able to represent rare words as a sequence of more common subwords. For example, the training corpus might not have the word `unhappiness`, but it will still be model it if the word `un, happi and ness` are in the training corpus.
  - In word tokenization, words like `small, smallest, large, largest` are treated as separate tokens, but with BPE this can be modelled as `small, large, est`. This allows BPE to achieve large coverage with small vocabulary size.
  - BPE naturally captures the morphological patterns (how a word is formed) of a word, often segmenting words into stems and affixes, enabling better generalization across related words.

- Decoding is the opposite of encoding, it takes the encoded vector (token id) and converts it to human-readable output. Typically, the flow is as follows:

  ```mermaid
  flowchart LR;
    text["How are you"]
    tokenization
    encoder
    embeddings
    decoder
    output["I am fine"]

    text --> tokenization --> encoder --> embeddings --> decoder --> output
  ```
