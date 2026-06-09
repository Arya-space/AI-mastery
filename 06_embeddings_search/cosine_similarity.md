# Cosine Similarity

**Cosine similarity** measures the angle between two vectors, ignoring magnitude. Used to compare embeddings.

## Formula
```
cosine_similarity(A, B) = (A · B) / (||A|| × ||B||)
```
- Result range: -1 to 1
- 1.0 = identical direction (same meaning)
- 0.0 = orthogonal (unrelated)
- -1.0 = opposite direction (rare with embeddings)

## Why Cosine, Not Euclidean Distance?
- Embeddings can vary in magnitude (length) based on how text is encoded
- Cosine only measures direction (semantic meaning), not length
- More reliable for comparing text meaning across different document lengths

## Implementation in NumPy
```python
import numpy as np

def cosine_similarity(a: np.ndarray, b: np.ndarray) -> float:
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

# Usage
emb_a = np.array([0.1, 0.5, -0.3])
emb_b = np.array([0.2, 0.4, -0.2])
score = cosine_similarity(emb_a, emb_b)  # e.g. 0.97
```

## Dot Product as Fast Approximation
If vectors are normalized (unit vectors), dot product = cosine similarity:
```python
# Normalize
a_norm = a / np.linalg.norm(a)
b_norm = b / np.linalg.norm(b)
score = np.dot(a_norm, b_norm)  # same as cosine
```
Most vector DBs store normalized vectors and use dot product internally for speed.

## Interpreting Scores
| Score | Interpretation |
|-------|---------------|
| > 0.9 | Very high similarity |
| 0.7–0.9 | High — likely relevant |
| 0.5–0.7 | Moderate — maybe relevant |
| < 0.5 | Low — probably not relevant |

Thresholds vary by model. Always calibrate on your data.
