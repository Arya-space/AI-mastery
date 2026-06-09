# RAG Evals

RAG has two independently failing components: retrieval and generation. Evaluate each separately.

## Retrieval Metrics
Given a query, does the retriever return the right chunks?

**Recall@k**: of all relevant docs, how many appear in top-k?
```python
def recall_at_k(relevant: set, retrieved: list, k: int) -> float:
    top_k = set(retrieved[:k])
    return len(relevant & top_k) / len(relevant)
```

**Precision@k**: of the top-k returned, how many are relevant?
```python
def precision_at_k(relevant: set, retrieved: list, k: int) -> float:
    top_k = retrieved[:k]
    return sum(1 for d in top_k if d in relevant) / k
```

**MRR (Mean Reciprocal Rank)**: how high is the first relevant result?
```python
def mrr(relevant: set, retrieved: list) -> float:
    for i, doc in enumerate(retrieved):
        if doc in relevant:
            return 1 / (i + 1)
    return 0.0
```

## Generation Metrics
Given the retrieved chunks + query, is the answer good?

**Faithfulness**: does the answer only contain claims supported by the context?
- LLM judge: "Is every claim in this answer supported by the provided context?"

**Answer Relevance**: does the answer address the question?
- LLM judge: "Does this answer fully address the question?"

**Context Relevance**: are the retrieved chunks actually relevant to the query?
- LLM judge: "Is this chunk relevant to answering this question?"

## RAGAS Framework
Automated RAG evaluation framework:
```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_recall

results = evaluate(
    dataset=eval_dataset,
    metrics=[faithfulness, answer_relevancy, context_recall]
)
```

## Eval Pipeline
```
Query → Retriever → top-k chunks
         ↓
Measure: Recall@k, Precision@k (retrieval eval)
         ↓
Chunks + Query → LLM → Answer
         ↓
Measure: Faithfulness, Relevance (generation eval)
```
