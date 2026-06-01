# Citations

Citations tell users where the answer came from. Critical for trust, verifiability, and debugging.

## Why Citations Matter
- User can verify the answer
- Debuggable: if answer is wrong, trace back to source chunk
- Required in legal, medical, enterprise applications
- Reduces perceived hallucination risk

## Approach 1: Inline References (Simple)
Number your chunks. Ask the model to cite by number:
```
Context:
[1] "Refunds are processed within 5 business days..."
[2] "To request a refund, email support@..."

Answer the question and cite sources as [1], [2], etc.
```
Output: "Refunds take 5 business days [1]. Contact support@... to initiate [2]."

## Approach 2: Structured Citation Output
```python
class AnswerWithCitations(BaseModel):
    answer: str
    citations: list[Citation]

class Citation(BaseModel):
    source_id: str
    quote: str  # exact quote from the source
```

## Approach 3: Post-processing
Generate answer first, then run a second LLM call to identify which chunks support each claim.

## Verifying Citation Accuracy
Citations can be hallucinated. Validate:
```python
def verify_citation(answer: str, cited_chunk: str) -> bool:
    # Check if the key claim in the answer appears in the cited chunk
    # Simple: substring check
    # Better: embedding similarity between answer claim and chunk
    return cited_chunk in answer or similarity(answer, cited_chunk) > 0.8
```

## Metadata to Include in Citations
- Source document name / URL
- Page number
- Section heading
- Date of the document
