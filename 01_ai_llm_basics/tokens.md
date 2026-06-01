# Tokens

A **token** is the basic unit an LLM reads and writes. Not a word — a subword chunk.

## Tokenization (BPE)
**Byte Pair Encoding**: starts with characters, merges frequent pairs into subword units.
- `"unhappiness"` → `["un", "happ", "iness"]` (3 tokens)
- Common words = 1 token. Rare words = many tokens.
- ~1 token ≈ 0.75 words in English

## Why Tokens Matter
- **Cost**: APIs charge per token (input + output separately)
- **Context limit**: measured in tokens, not words
- **Latency**: output tokens are generated sequentially — more output = slower response

## Token Quirks
- Numbers: `"1234567"` → multiple tokens (each digit or pair)
- Code: usually more tokens than prose
- Non-English: more tokens per word (e.g., Arabic, Chinese)
- Whitespace and punctuation: each can be a token

## Counting Tokens
```python
import tiktoken  # OpenAI's tokenizer
enc = tiktoken.encoding_for_model("gpt-4o")
tokens = enc.encode("Hello, world!")
print(len(tokens))  # 4
```

## Failure Modes From Tokenization
- Spelling tasks fail (model sees tokens, not letters)
- Arithmetic errors (numbers split across tokens)
- Prompt injection via unusual token boundaries
