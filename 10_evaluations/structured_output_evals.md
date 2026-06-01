# Structured Output Evals

Evaluating whether the model returns correctly structured, accurate outputs.

## Schema Compliance Rate
Does the output match the required schema?
```python
def schema_compliance_rate(results: list[dict]) -> float:
    valid = 0
    for result in results:
        try:
            MySchema.model_validate(result)
            valid += 1
        except ValidationError:
            pass
    return valid / len(results)
```
Target: 100% for strict structured output. If below 95%, your prompting or schema is wrong.

## Field Accuracy
For extraction tasks, how accurate are the individual fields?
```python
def field_accuracy(predicted: dict, ground_truth: dict) -> dict[str, float]:
    scores = {}
    for key in ground_truth:
        if key not in predicted:
            scores[key] = 0.0
        elif predicted[key] == ground_truth[key]:
            scores[key] = 1.0
        else:
            scores[key] = 0.0  # or partial credit for near-matches
    return scores
```

## Recall and Precision for Extraction
When extracting a list (entities, items), measure:

**Recall**: of all entities that should be extracted, how many were found?
```python
def recall(predicted: set, expected: set) -> float:
    return len(predicted & expected) / len(expected) if expected else 1.0
```

**Precision**: of all extracted entities, how many are correct?
```python
def precision(predicted: set, expected: set) -> float:
    return len(predicted & expected) / len(predicted) if predicted else 1.0
```

**F1**: harmonic mean of precision and recall:
```python
def f1(precision: float, recall: float) -> float:
    if precision + recall == 0:
        return 0.0
    return 2 * precision * recall / (precision + recall)
```

## Handling Partial Outputs
Model returns a partial schema (some fields missing):
- Count missing required fields as errors
- If optional fields missing, score 1.0 for that field
- Log which fields are most often missing → fix prompt or schema description

## Eval Pipeline
```python
for case in eval_dataset:
    raw_output = llm.call(case.prompt)
    try:
        parsed = MySchema.model_validate_json(raw_output)
        schema_valid = True
        field_scores = field_accuracy(parsed.dict(), case.expected)
    except ValidationError:
        schema_valid = False
        field_scores = {k: 0.0 for k in case.expected}

    log_result(case.id, schema_valid, field_scores)
```
