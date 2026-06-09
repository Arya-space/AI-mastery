# Few-Shot Prompting

**Few-shot prompting**: provide examples of input → output before the actual task. The model infers the pattern and applies it.

## Why It Works
LLMs are trained to continue patterns. Examples demonstrate the exact behavior you want — tone, format, reasoning depth — better than instructions alone.

## Structure
```
Task: Classify sentiment.

Text: "The product broke after one day." → Negative
Text: "Works exactly as described." → Positive
Text: "It's okay, nothing special." → Neutral

Text: "{user_input}" →
```

## How Many Examples
- 1–3: usually enough for format/style
- 5–10: for complex classification or nuanced tasks
- More examples → more tokens → higher cost + latency
- Quality > quantity: bad examples hurt more than helping

## Chain-of-Thought (CoT) Examples
Show the reasoning, not just the answer:
```
Q: If a train travels 60 mph for 2 hours, how far?
A: 60 × 2 = 120 miles. Answer: 120 miles.

Q: If a train travels 80 mph for 1.5 hours, how far?
A:
```
CoT improves accuracy on reasoning, math, multi-step tasks.

## Dynamic Few-Shot
Instead of hardcoding examples, retrieve the most similar examples from a database at runtime using embeddings. Best examples = most similar to the current input.

## When Few-Shot Hurts
- Examples that contradict each other
- Examples that are too different from the actual task
- Using few-shot when zero-shot + clear instructions is enough
