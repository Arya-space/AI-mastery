# Eval Dataset

An **eval dataset** is a collection of test cases: each case has an input + expected output or criteria for what "good" looks like.

## Structure
```python
eval_cases = [
    {
        "input": "What is the refund policy?",
        "expected_output": "Refunds processed within 5 business days",
        "expected_source": "refund_policy.pdf"
    },
    {
        "input": "How do I cancel my subscription?",
        "criteria": ["mentions cancellation page", "no upselling", "under 100 words"]
    }
]
```

## Golden Dataset
A **golden dataset** = input + manually verified ideal output. Ground truth.
- Created by domain experts or careful human review
- Used for regression testing
- Every time you change the system, run against golden dataset

## What Makes a Good Eval Dataset
- Covers common cases (80% of real traffic)
- Covers edge cases (tricky phrasing, missing info, ambiguous queries)
- Covers adversarial cases (prompt injection, jailbreak attempts)
- Large enough to be statistically meaningful (50–500+ cases)
- Balanced across categories

## Building the Dataset
1. Sample from real production traffic (anonymized)
2. Include known failure cases (cases you've seen fail)
3. Create synthetic adversarial cases
4. Have domain experts verify expected outputs

## Dataset Size
| System | Min Cases |
|--------|-----------|
| Simple classifier | 50 |
| RAG Q&A | 100–200 |
| Complex agent | 50+ (per capability) |

## Maintenance
- Version your eval dataset (v1, v2...)
- When you find a new failure mode in production → add to dataset
- Periodically re-verify expected outputs (model behavior changes)
- Keep it in version control alongside your code
