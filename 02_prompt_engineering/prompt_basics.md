# Prompt Basics

A **prompt** is everything you send to the model in a single call. It has three parts:

## Structure
- **System prompt**: instructions, persona, rules, output format — set by the developer
- **User message**: the current request from the user
- **Assistant turn**: model's previous response (for multi-turn conversations)

## Zero-shot vs Few-shot
- **Zero-shot**: just instructions, no examples — "Classify this as positive or negative:"
- **One-shot**: one example before the task
- **Few-shot**: 2–10 examples — teaches the model the expected pattern

## Temperature
Controls randomness of output:
- `0.0` → deterministic, picks highest-probability token every time
- `0.7` → balanced (default for most use cases)
- `1.0+` → creative, unpredictable

Use low temperature for: extraction, classification, structured output.
Use higher temperature for: creative writing, brainstorming.

## Basic Best Practices
1. Be specific about what you want
2. Specify the output format explicitly
3. Put instructions before the content to process
4. Use delimiters (`---`, `"""`, `<text>`) to separate sections
5. Tell the model what NOT to do if needed
