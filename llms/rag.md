# Rag

1. Explain the architecture of a RAG system?

- RAG architectures are continuously evolving. But a basic rag system has the following components:

  ```mermaid
  flowchart LR;

    docs
    doc-encoder
    query-encoder
    retriever
    context
    prompt
    input
    generator
    output

    docs --> doc-encoder --> retriever --> context --> generator --> output

    prompt --> generator

    input --> generator

    input --> query-encoder --> retriever
  ```

- The two main components of a rag system are:
  - Retriever: The role of retriever is to find relevant documents or passages from a large external corpus or index.
  - Generator: The generator is typically a pre-trained sequence to sequence model or a language model. It receives both the input query and the retrieved documents to generate the output sequence.

2. What techniques are used to encode the documents?

- Documents are encoded in chunks. Chunks can be a passage or a page or number of pages. The way you create chunks is something that is decided after trying out difference approaches.

3. Explain different types of methods used by retriever?

- Retriever methods generally fall into two categories. Dense retriever methods and sparse retriever methods.
  - Dense retriever methods: use neural networks to encode documents into dense vectors (also knowns as [[embeddings]]). And then use dot product or cosine [[similarity]] to find the [[similarity]] between the query and the documents.
    - Dense retriever methods excel at capturing the semantic similarity even if there is no exact word overlap.
    - Dense retriever methods requires pre-training to learn the effective representation of document corpus.
    - Dual encoder architecture: The encoding of input query and documents are computed independently using two different (but similar) encoding networks. And then use a similarity metric to compute the [[similarity]] score between the query and documents.
      - Dual encoders are much faster as the encoding vectors can be computed independently for both input query and documents. In fact, you can pre-compute the encoding on documents and store them in a vector database, no need to compute again at inference time.
  - Sparse Retriever methods: Sparse retrieval methods rely on finding matching terms or keywords between the query and documents.
    - TF-IDF and BM(25): calculate score for each document based on how often the query term appears in the document and how rare those terms are across the entire document collection.
  - Hybrid retriever: uses both sparse and dense retriever. First it uses sparse retriever to retrieve a large pool of documents and then uses dense retriever to narrow down the result.

4. When to retrieve documents?

- Different architectures explore different ways to retrieve documents. In traditional (frozen) rag, the documents are retrieved once and then passed as a context to the language model and the language model then generates the response. In some rag system, the documents are retrieved at every generation step. There are also adaptive retrieval systems (or on demand retrieval), in which the model learns to retrieve documents only when it is beneficial.

5. Evaluation strategies for a rag system?

- Evaluating a rag system involves evaluating both retriever and generator to measure that the system retrieves relevant information and generates accurate, coherent responses.
- To measure the retriever:
  - Calculate #precision to assess the fraction of relevant documents retrieved out of all retrieved documents.
  - Calculate #recall to assess the fraction of relevant documents retrieved.
- To measure the generator:
  - Measure the similarity between the generated text and reference text
  - Functional testing, if the llm model generates code like sql, python code, you can test the correctness of it by running the code or checking for syntax errors.
  - Simple regex matching for simple tests for example you can write tests to check if model is not outputting PII information.
