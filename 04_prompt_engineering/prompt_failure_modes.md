# Prompt Failure Modes

Common ways prompts break in production.

## Underspecification
Task not clearly defined → model guesses what you want.
```
Bad:  "Summarize this."
Good: "Summarize in 3 bullet points. Each bullet ≤ 15 words. Focus on action items only."
```

## Format Drift
Model ignores the requested format, especially in long prompts.
- Fix: repeat format instruction at the end of the prompt
- Fix: use structured output APIs (JSON mode, tool use) instead of prompting for format

## Instruction Following Failures
Model follows the spirit but not the letter of instructions.
- "Do not mention competitors" → model still implies them
- Fix: be explicit, add examples of compliant output
- Fix: add output guardrails to validate post-generation

## Context Confusion
In long prompts, model confuses which part of the context applies.
- Fix: use clear delimiters (`<document>`, `<instructions>`)
- Fix: keep related content together

## Prompt Sensitivity
Small wording changes → very different outputs. Sign of a fragile prompt.
- "List 3 reasons" vs "Give me 3 reasons" can behave differently
- Fix: test variations; pick the most stable phrasing

## Over-instruction
Too many rules → model ignores half of them.
- Fix: prioritize. Only include rules that matter.
- Fix: if you need many rules, use structured output + separate validation

## Under-instruction
No guidance → default model behavior, which may not match your use case.
- Always specify: format, length, tone, what to do when uncertain.
