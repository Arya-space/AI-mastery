# Role / Task / Context / Format

A reliable 4-part structure for writing system prompts.

## Role
Define who the model is. Sets tone, expertise, and behavior.
```
You are a senior software engineer specializing in Python and distributed systems.
```
- Be specific. "You are an expert" is weaker than "You are a staff engineer at a fintech company."
- Role shapes how the model filters and frames information.

## Task
State exactly what to do. Use imperative verbs.
```
Analyze the following code snippet and identify potential performance bottlenecks.
```
- One clear task per prompt
- Specify scope: "Only analyze X, do not suggest refactors."

## Context
Give the model what it needs to do the task well.
```
The code runs in a latency-sensitive API handler. We use Python 3.11 and FastAPI.
```
- Include relevant constraints, background, user profile
- Don't overload — only context that changes the output

## Format
Tell the model exactly how to respond.
```
Respond as a JSON array of objects with keys: "issue", "severity", "suggestion".
```
- Specify: JSON / bullet list / table / paragraph / numbered steps
- Specify: length constraints ("max 3 bullets", "under 100 words")
- Specify: what to include / exclude

## Full Example
```
You are a senior Python engineer. [ROLE]

Analyze the code below and identify the top 3 performance issues. [TASK]

The code is part of a high-traffic FastAPI endpoint handling 1000 req/s. [CONTEXT]

Respond as a numbered list. Each item: one sentence describing the issue and one sentence for the fix. [FORMAT]

Code:
"""
{code}
"""
```
