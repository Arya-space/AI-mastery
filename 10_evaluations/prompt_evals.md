# Prompt Evals

Evaluating whether a prompt produces the expected output quality.

## Exact Match
For deterministic outputs (classification, extraction):
```python
def eval_exact(output: str, expected: str) -> float:
    return 1.0 if output.strip().lower() == expected.strip().lower() else 0.0
```
Works for: labels, yes/no, enum values.

## Contains Check
Output must contain specific phrases or elements:
```python
def eval_contains(output: str, required: list[str]) -> float:
    hits = sum(1 for phrase in required if phrase.lower() in output.lower())
    return hits / len(required)
```

## LLM-as-Judge
Use another LLM to score the output against criteria:
```python
def llm_judge(question: str, answer: str, criteria: str) -> float:
    prompt = f"""
    Rate this answer on a scale of 1-5 for the following criteria: {criteria}

    Question: {question}
    Answer: {answer}

    Return only a number from 1 to 5.
    """
    score = int(llm.call(prompt))
    return score / 5.0
```
Use a different/stronger model as judge than the one being evaluated.

## Criteria-Based Scoring
Define rubric explicitly:
```python
criteria = {
    "factual_accuracy": "Does the answer contain only true statements?",
    "completeness": "Does it answer all parts of the question?",
    "conciseness": "Is it under 100 words?",
    "no_hallucination": "Does it avoid claims not in the context?"
}
```
Score each criterion separately → aggregate.

## A/B Testing Prompts
```python
results_a = [eval_case(prompt_a, case) for case in eval_dataset]
results_b = [eval_case(prompt_b, case) for case in eval_dataset]
print(f"Prompt A: {mean(results_a):.2f}, Prompt B: {mean(results_b):.2f}")
```
Only ship the prompt with higher score. Never deploy based on vibes.
