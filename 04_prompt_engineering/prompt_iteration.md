# Prompt Iteration

Prompt engineering is empirical. Write, test, diagnose, fix.

## The Loop
```
1. Write initial prompt
2. Test on 10+ varied examples
3. Find failure patterns (not just one-offs)
4. Diagnose root cause
5. Fix one thing at a time
6. Re-test on full set
```

## Diagnosing Failures
| Symptom | Likely Cause |
|---------|-------------|
| Wrong format | Format not specified clearly enough |
| Off-topic answer | Task ambiguous or context misleading |
| Too verbose | No length constraint |
| Hallucinating | No grounding / no "say I don't know" instruction |
| Inconsistent | Temperature too high, or examples contradict |

## A/B Testing Prompts
- Run both prompts on the same test set
- Score outputs (automated or human)
- Only ship the winner
- Never change based on 1–2 examples

## Prompt Changelog
Keep a log of prompt versions:
```
v1: Basic instruction only — 62% pass rate
v2: Added output format spec — 78% pass rate
v3: Added 3 few-shot examples — 89% pass rate
```

## Key Rule
Change **one thing at a time**. If you change role + format + examples simultaneously, you don't know what helped.
