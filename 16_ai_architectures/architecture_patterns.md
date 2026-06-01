# AI System Architecture Patterns

The 6 core patterns every AI engineer should know. Most production systems are combinations of these.

## Pattern 1: Single LLM Call
```
User Input → Prompt Template → LLM → Output
```
Simplest possible architecture. One call, one response.

**Use when**: single-turn tasks, summarization, classification, extraction, code generation.

**Example**: "Summarize this article" → LLM → summary

---

## Pattern 2: Prompt Chain
```
Input → LLM 1 → output 1 → LLM 2 → output 2 → Final Output
```
Output of one call becomes input to the next. Deterministic sequence.

**Use when**: multi-step processing where each step has a clear output.

**Example**: Extract entities → classify entities → format as JSON

```python
entities = llm.call(f"Extract entities from: {text}")
classified = llm.call(f"Classify these entities: {entities}")
formatted = llm.call(f"Format as JSON: {classified}")
```

---

## Pattern 3: RAG (Retrieval-Augmented Generation)
```
User Query → Embed → Vector Search → Top-k Chunks → LLM → Grounded Answer
```
Grounds the LLM in external knowledge at inference time.

**Use when**: private/proprietary data, updatable knowledge, need citations.

**Example**: Q&A over company docs, customer support bot.

---

## Pattern 4: Tool-Using Agent
```
User Goal → Agent Loop → [LLM decides → call tool → observe result → repeat] → Final Answer
```
LLM dynamically decides which tools to call and in what order.

**Use when**: open-ended tasks requiring multiple actions, real-world interactions.

**Example**: "Book me a flight to Tokyo" → search flights → check calendar → book → confirm

---

## Pattern 5: Multi-Agent (Orchestrator + Specialists)
```
User → Orchestrator Agent → routes to → Specialist Agent A
                                      → Specialist Agent B
                                      → Specialist Agent C
                          ← collects results ←
User ← Final Answer
```
Divide and conquer. Each agent has focused tools + instructions.

**Use when**: complex tasks spanning multiple domains, parallel workloads, specialized expertise needed.

**Example**: Research agent + Analysis agent + Writing agent → produce report

---

## Pattern 6: Human-in-the-Loop
```
Agent → proposes action → PAUSE → Human reviews → APPROVE/DENY → Agent continues
```
Agent handles reasoning, human approves consequential actions.

**Use when**: irreversible actions (send, delete, publish), high-stakes decisions, compliance requirements.

---

## Combinations
Most real systems combine patterns:
```
RAG + Tool Agent:     retrieve docs AND call APIs
Multi-Agent + RAG:    each specialist has its own retriever
Chain + Guardrails:   validate at each step
Agent + Human Loop:   autonomous until write action needed
```
