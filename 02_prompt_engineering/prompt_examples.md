# Prompt Examples

Reference library of prompt patterns by task type.

## Classification
```
You are a content moderation system.
Classify the following text as: SAFE, INAPPROPRIATE, or SPAM.
Respond with only the label.

Text: "{text}"
Label:
```

## Extraction
```
Extract all named entities from the text below.
Return a JSON object with keys: "people" (list), "organizations" (list), "locations" (list).
If none found, return an empty list for that key.

Text: "{text}"
```

## Summarization
```
Summarize the following article in exactly 3 bullet points.
Each bullet must be one sentence. Focus on key facts only — no opinions.

Article:
"""
{article}
"""
```

## Transformation
```
Convert the following informal customer message into a professional support ticket.
Preserve all factual details. Do not add information not present in the original.

Message: "{message}"
Professional version:
```

## Q&A with Grounding
```
Answer the question using only the context below.
If the answer is not in the context, say "I don't have enough information."

Context:
"""
{context}
"""

Question: {question}
Answer:
```

## Reasoning / Analysis
```
You are a product manager. Analyze the following user feedback and identify:
1. The main pain point
2. The implied feature request
3. Priority (High / Medium / Low) with one-sentence justification

Feedback: "{feedback}"
```
