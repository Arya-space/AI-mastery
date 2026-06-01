# Transformers — High Level

The **transformer** is the architecture behind all modern LLMs. Introduced in "Attention Is All You Need" (2017).

## Why Transformers Replaced RNNs
- RNNs processed tokens sequentially → slow, forget long dependencies
- Transformers process all tokens **in parallel** → fast training, handles long context

## Self-Attention
The core mechanism. Each token looks at every other token and learns how much to "attend" to it.

Example: In "The bank by the river was steep", the word "bank" attends strongly to "river" (not "money") → model understands context.

**Q, K, V**: each token generates a Query, Key, Value vector.
- Attention score = Q · K (dot product)
- Output = weighted sum of V vectors

## Architecture Components
- **Positional encoding**: tells model where each token sits in sequence (transformers have no inherent order)
- **Multi-head attention**: runs attention multiple times in parallel, each head learns different relationships
- **Feed-forward layers**: transform each token's representation
- **Layer norm + residuals**: stabilize training

## Encoder vs Decoder vs Encoder-Decoder
| Type | Examples | Use |
|------|----------|-----|
| Encoder-only | BERT | Classification, embeddings |
| Decoder-only | GPT, Claude, Llama | Text generation (LLMs) |
| Encoder-Decoder | T5, BART | Translation, summarization |

**As an AI Engineer**: you don't need to implement transformers. Know that LLMs are decoder-only transformers, embedding models are encoder-only, and self-attention is why they understand context so well.
