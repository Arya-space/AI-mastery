# Why Evals Matter

## Testing Code vs Evaluating LLM Systems
| Code Testing | LLM Evaluation |
|-------------|---------------|
| Deterministic: `assert f(x) == y` | Probabilistic: output varies |
| Binary pass/fail | Graded: 0–1 or rubric score |
| Fast | Slow + expensive (LLM calls) |
| Stable | Shifts with model updates |

You can't unit test an LLM. You need **evals**: a systematic process to measure how well your system works across many inputs.

## Why Informal Testing Fails
"Testing" on 3 examples manually is not evaluation.
- You miss edge cases
- You can't track regressions
- You can't compare two prompt versions objectively
- You can't catch model update breakage

## Evals Are the Foundation of Iteration
Without evals: you change a prompt and hope it's better.
With evals: you change a prompt and **measure** if it's better.

## Types of Evals
- **Unit eval**: one input → one expected output (format, content)
- **System eval**: end-to-end task completion
- **Regression eval**: did a change break anything that worked before?

## The Eval-Driven Development Loop
```
1. Define success criteria
2. Build eval dataset (input + expected output)
3. Run baseline → measure score
4. Make improvement (prompt, chunking, etc.)
5. Run evals → compare scores
6. Ship only if score improves
7. Add new failure cases to eval dataset
```

## Key Insight
Your eval quality determines how fast you can improve. Bad evals = flying blind. Good evals = clear signal on every change.
