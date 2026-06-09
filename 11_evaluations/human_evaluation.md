# Human Evaluation

Automated evals are fast but imperfect. Human evals are slow but can catch what automation misses.

## When Automated Evals Aren't Enough
- Output quality is subjective (tone, style, helpfulness)
- LLM-as-judge has biases (prefers longer answers, its own style)
- Novel failure modes automated evals aren't designed for
- Compliance and safety review (high-stakes decisions)

## Designing Human Eval Rubrics
Give evaluators a clear, specific rubric. Avoid vague criteria.

```
For each response, score 1–5:

Accuracy (1=factually wrong, 5=completely accurate)
Helpfulness (1=doesn't address question, 5=fully solves problem)
Clarity (1=confusing, 5=clear and easy to understand)
Conciseness (1=far too long/short, 5=appropriate length)

Notes: (optional, flag anything unusual)
```

## Inter-Rater Reliability
Multiple human raters often disagree. Measure agreement:
- **Cohen's Kappa**: agreement beyond chance (> 0.6 = good)
- **Krippendorff's Alpha**: for ordinal scales

If raters disagree significantly → rubric is unclear. Clarify and re-train.

## Using Human Evals to Calibrate LLM-as-Judge
1. Human raters score a sample of outputs
2. Run LLM-as-judge on same samples
3. Measure correlation between human scores and LLM scores
4. If low correlation → adjust judge prompt
5. Once calibrated → use LLM-as-judge at scale

## Cost and Scale Tradeoffs
| Type | Cost | Scale | Quality |
|------|------|-------|---------|
| Human expert | High | Low | Highest |
| Crowdsourced | Medium | Medium | Medium |
| LLM-as-judge | Low | High | Good |

**Practical approach**: human evals for calibration + critical review, LLM-as-judge for daily iteration.
