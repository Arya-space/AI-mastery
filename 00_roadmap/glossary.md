# Glossary

Definitions for core AI Engineering terms. Written to be precise and practical — each definition includes context for where the term matters in a real system.

Organized alphabetically within sections.

---

## A

**Agent**
A system where an LLM controls the execution flow: it can decide what tool to call next, observe the result, and continue reasoning until it reaches a final answer. Unlike a fixed pipeline, the model chooses the sequence of steps at runtime. See [09_agents/](../09_agents/what_is_an_agent.md).

**Agent loop**
The cycle an agent runs through: receive input → reason → decide action → execute tool → observe result → reason again → decide next action or return final answer. The loop continues until a stopping condition is met. See [09_agents/agent_loop.md](../09_agents/agent_loop.md).

**Attention mechanism**
The core computation in transformer models. Allows each token in a sequence to "attend to" (incorporate information from) every other token in the context. Enables the model to capture long-range dependencies. See [01_ai_llm_basics/transformers_high_level.md](../01_ai_llm_basics/transformers_high_level.md).

**Autoregressive generation**
How LLMs produce text: one token at a time, left to right, each new token conditioned on all previous tokens. The model does not generate the full response at once — it samples tokens sequentially until it reaches a stop token or the max token limit.

---

## B

**BPE (Byte-Pair Encoding)**
A common tokenization algorithm. Starts with individual characters, then iteratively merges the most frequent pairs of characters or character sequences into single tokens. Used by GPT models. See [01_ai_llm_basics/tokens.md](../01_ai_llm_basics/tokens.md).

**Batch processing**
Running multiple inputs through a model in one API call or forward pass, instead of one at a time. More efficient for embedding large document sets.

---

## C

**Chain-of-thought (CoT)**
A prompting technique where the model is instructed (or shown via examples) to reason through a problem step by step before giving a final answer. Improves accuracy on multi-step reasoning tasks. See [02_prompt_engineering/few_shot_prompting.md](../02_prompt_engineering/few_shot_prompting.md).

**Chunking**
Splitting a document into smaller pieces (chunks) before embedding and storing in a vector database. Chunking strategy significantly affects retrieval quality. Common strategies: fixed-size, sentence-based, recursive, semantic. See [06_rag/chunking.md](../06_rag/chunking.md).

**Citations**
Attaching source references to an LLM response, indicating which retrieved documents supported each claim. Important for grounded generation and user trust. See [06_rag/citations.md](../06_rag/citations.md).

**Context window**
The maximum number of tokens an LLM can process in a single call (input + output combined). Determines how much conversation history, document context, or tool results can be included. See [01_ai_llm_basics/context_window.md](../01_ai_llm_basics/context_window.md).

**Cosine similarity**
A measure of similarity between two vectors, computed as the cosine of the angle between them. Ranges from -1 (opposite) to 1 (identical direction). Used to compare embeddings during semantic search. See [05_embeddings_semantic_search/cosine_similarity.md](../05_embeddings_semantic_search/cosine_similarity.md).

**Cross-encoder**
A model that takes a query and a document together as input and outputs a relevance score. More accurate than bi-encoders for ranking but too slow for initial retrieval. Used as a reranker after initial vector search. See [06_rag/reranking.md](../06_rag/reranking.md).

---

## D

**Dense retrieval**
Retrieving documents using embedding vectors and approximate nearest neighbor search. Captures semantic meaning, not just keyword overlap. Contrast with sparse retrieval (BM25/TF-IDF). See [05_embeddings_semantic_search/](../05_embeddings_semantic_search/what_are_embeddings.md).

**Document loader**
A component that reads raw documents from a source (PDF, web page, database, file system) and converts them into a standard format for processing. See [06_rag/document_loading.md](../06_rag/document_loading.md).

---

## E

**Embedding**
A dense vector representation of text (or other data). Sentences or chunks with similar meaning have vectors that are close together in the embedding space. Used for semantic search, clustering, and classification. See [01_ai_llm_basics/embeddings_intro.md](../01_ai_llm_basics/embeddings_intro.md).

**Embedding model**
A model trained specifically to convert text into embedding vectors. Separate from the generative LLM. Examples: `text-embedding-3-small` (OpenAI), `all-MiniLM-L6-v2` (sentence-transformers).

**Eval (evaluation)**
A test that measures the quality of an LLM system's output. Can be deterministic (exact match), model-graded (LLM judges the output), or human-graded. See [10_evaluations/](../10_evaluations/why_evals_matter.md).

---

## F

**Few-shot prompting**
Including examples of the desired input/output format in the prompt. Helps the model understand the expected behavior without fine-tuning. See [02_prompt_engineering/few_shot_prompting.md](../02_prompt_engineering/few_shot_prompting.md).

**Fine-tuning**
Training a pre-trained model on additional data to adjust its weights for a specific task or style. More expensive and complex than prompting or RAG. See [12_finetuning_vs_rag_vs_prompting/when_to_finetune.md](../12_finetuning_vs_rag_vs_prompting/when_to_finetune.md).

**Function calling** *(also: tool calling)*
The ability of an LLM to generate a structured request to call an external function/tool, rather than generating prose. The model outputs a tool name and arguments in a defined format; the application executes the tool and returns the result. See [07_tool_calling/](../07_tool_calling/what_is_tool_calling.md).

---

## G

**Grounded generation**
Generating a response that is anchored to retrieved source documents, rather than relying solely on the model's training. Reduces hallucination. Used in RAG systems. See [06_rag/grounded_generation.md](../06_rag/grounded_generation.md).

**Guardrail**
A constraint applied to an AI system's inputs or outputs to prevent unsafe, incorrect, or off-topic behavior. Can be implemented via prompting, model-based classifiers, regex, or external validation. See [13_safety_guardrails/](../13_safety_guardrails/input_guardrails.md).

---

## H

**Hallucination**
When an LLM generates text that is factually incorrect or unsupported by the provided context, stated confidently. A key failure mode. Mitigated by RAG, grounding, output validation, and evals. See [13_safety_guardrails/hallucination.md](../13_safety_guardrails/hallucination.md).

**Handoff**
In a multi-agent system, the act of one agent passing control to another agent for a specific subtask. The receiving agent takes over with its own instructions and tools. See [09_agents/handoffs.md](../09_agents/handoffs.md).

**Hybrid search**
Combining dense retrieval (embeddings) and sparse retrieval (keyword/BM25) to improve recall and precision. Particularly useful when documents have domain-specific terminology. See [05_embeddings_semantic_search/hybrid_search.md](../05_embeddings_semantic_search/hybrid_search.md).

---

## I

**Inference**
Running a trained model to generate output, as opposed to training it. All production LLM usage is inference. Inference cost scales with token count.

**Input guardrail**
A check applied to user input before it reaches the LLM. Examples: topic classification, toxicity detection, length limits, PII detection. See [13_safety_guardrails/input_guardrails.md](../13_safety_guardrails/input_guardrails.md).

---

## J

**JSON mode**
An LLM API setting that forces the model to output valid JSON. Useful for structured output extraction when you need reliable parsing. Contrast with structured outputs (schema-constrained). See [03_structured_outputs/json_outputs.md](../03_structured_outputs/json_outputs.md).

---

## L

**Latency**
The time between sending an API request and receiving the complete response. For LLMs: time-to-first-token (TTFT) and total generation time both matter. See [14_production_deployment/latency.md](../14_production_deployment/latency.md).

**LLM (Large Language Model)**
A neural network trained on large amounts of text to predict the next token. Used as the core reasoning component in AI applications. Examples: GPT-4o, Claude 3.5 Sonnet, Gemini 1.5 Pro. See [01_ai_llm_basics/what_is_an_llm.md](../01_ai_llm_basics/what_is_an_llm.md).

**LLM-as-judge**
Using an LLM to evaluate the output of another LLM call. Common in automated evals. Requires careful prompt design to avoid bias. See [10_evaluations/prompt_evals.md](../10_evaluations/prompt_evals.md).

---

## M

**MCP (Model Context Protocol)**
An open protocol that standardizes how AI applications connect to external tools, data sources, and services. Defines a client-server architecture where MCP servers expose capabilities and MCP clients (built into apps/agents) consume them. See [08_mcp/](../08_mcp/what_is_mcp.md).

**Memory (agent memory)**
The mechanism by which an agent retains information across turns or sessions. Short-term memory = current context window. Long-term memory = external storage (database, vector DB). See [11_memory_context/](../11_memory_context/short_term_memory.md).

**Multi-agent system**
A system composed of multiple agents, each with specialized capabilities, that can hand off tasks to each other or run in parallel. See [09_agents/agent_types.md](../09_agents/agent_types.md).

---

## O

**Output guardrail**
A check applied to LLM output before it reaches the user. Examples: fact-checking against retrieved context, toxicity filtering, schema validation, PII redaction. See [13_safety_guardrails/output_guardrails.md](../13_safety_guardrails/output_guardrails.md).

---

## P

**Pydantic**
A Python library for data validation using type annotations. Widely used in AI engineering to define structured output schemas and validate LLM responses. See [03_structured_outputs/pydantic_validation.md](../03_structured_outputs/pydantic_validation.md).

**Prompt injection**
An attack where malicious content in a user input or retrieved document attempts to override the system prompt or redirect the LLM's behavior. A key security concern for agents and RAG systems. See [13_safety_guardrails/prompt_injection.md](../13_safety_guardrails/prompt_injection.md).

**Prompt template**
A reusable prompt structure with placeholders for dynamic content (user input, retrieved context, tool results, etc.). Keeps prompts consistent and testable. See [02_prompt_engineering/prompt_templates.md](../02_prompt_engineering/prompt_templates.md).

---

## R

**RAG (Retrieval-Augmented Generation)**
A pattern where relevant documents are retrieved from a knowledge base and injected into the prompt before the LLM generates a response. Enables the model to answer questions based on external, up-to-date, or private data without fine-tuning. See [06_rag/](../06_rag/what_is_rag.md).

**Reranker**
A model that takes an initial set of retrieved documents and re-scores them for relevance to the query. More accurate than the initial retriever but slower. Typically a cross-encoder model. See [06_rag/reranking.md](../06_rag/reranking.md).

**RLHF (Reinforcement Learning from Human Feedback)**
A training technique used to align LLMs with human preferences. Human raters score model outputs; those scores are used to train a reward model; the LLM is then fine-tuned using RL to maximize the reward. Used in ChatGPT, Claude, and other SOTA models.

---

## S

**Semantic search**
Searching based on meaning rather than exact keywords. Uses embedding similarity to find documents that are conceptually related to the query, even if they share no exact words. See [05_embeddings_semantic_search/keyword_vs_semantic_search.md](../05_embeddings_semantic_search/keyword_vs_semantic_search.md).

**Streaming**
Receiving LLM output token-by-token as it is generated, rather than waiting for the complete response. Improves perceived latency for the user. Requires streaming-aware API clients and UIs.

**Structured output**
A response from an LLM that conforms to a defined schema (JSON object, specific fields, types). Enables reliable downstream processing. See [03_structured_outputs/](../03_structured_outputs/why_structured_outputs.md).

**System prompt**
Instructions given to an LLM at the start of a conversation, separate from the user's messages. Defines the model's role, behavior, constraints, and output format. See [02_prompt_engineering/prompt_basics.md](../02_prompt_engineering/prompt_basics.md).

---

## T

**Temperature**
A parameter controlling the randomness of LLM output. Temperature = 0 makes the model deterministic (always picks the highest-probability token). Higher temperature introduces more variation. For structured outputs and factual tasks, use low temperature.

**Token**
The basic unit an LLM processes. Not always a word — can be a word, word fragment, punctuation, or whitespace. Input and output are both measured and billed in tokens. See [01_ai_llm_basics/tokens.md](../01_ai_llm_basics/tokens.md).

**Tool** *(in LLM context)*
A function or capability that an LLM can invoke during generation. Defined by a schema (name, description, parameters). The model decides when to call a tool and what arguments to pass. See [07_tool_calling/](../07_tool_calling/what_is_tool_calling.md).

**Tracing**
Recording the full execution of an agent run: each LLM call, tool call, input, output, timing, and error. Essential for debugging multi-step agents. See [09_agents/tracing.md](../09_agents/tracing.md).

**Transformer**
The neural network architecture underlying all modern LLMs. Uses attention mechanisms to process sequences in parallel (unlike RNNs). See [01_ai_llm_basics/transformers_high_level.md](../01_ai_llm_basics/transformers_high_level.md).

---

## V

**Vector database**
A database optimized for storing and querying high-dimensional embedding vectors. Supports approximate nearest neighbor (ANN) search. Examples: FAISS, Chroma, Pinecone, pgvector, Weaviate. See [05_embeddings_semantic_search/vector_databases.md](../05_embeddings_semantic_search/vector_databases.md).

---

## Z

**Zero-shot prompting**
Asking the model to perform a task without providing any examples. Works well for clear, well-defined tasks. Contrast with few-shot prompting. See [02_prompt_engineering/prompt_basics.md](../02_prompt_engineering/prompt_basics.md).

---

*Add personal notes and new terms as you encounter them throughout the course.*
