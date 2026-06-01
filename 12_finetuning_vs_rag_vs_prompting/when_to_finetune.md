# When to Fine-tune

## Legitimate Use Cases
1. **Consistent output format**: model must always return a very specific structure that prompt engineering can't reliably enforce
2. **Proprietary style/tone**: brand voice, industry-specific language patterns
3. **Task specialization**: model needs to be very good at one narrow task (medical note writing, legal summarization)
4. **Efficiency**: fine-tuned smaller model replaces expensive larger model + long system prompt
5. **Classification**: specialized sentiment/intent classifier from a base model

## When NOT to Fine-tune
- You haven't tried prompt engineering first (almost always try this first)
- You need knowledge injection (use RAG)
- Your data is less than ~500 high-quality examples (not enough to train reliably)
- Data changes frequently (fine-tuned model is static)

## Data Requirements
- **Minimum**: 50–100 examples (basic style adaptation)
- **Good**: 500–1000 examples
- **Strong**: 5000+ examples for complex tasks
- Format: JSONL of `{"prompt": "...", "completion": "..."}` pairs
- Quality > quantity: bad examples corrupt the model

## PEFT and LoRA
**Parameter-Efficient Fine-tuning**: instead of retraining all weights, train a small adapter.
- **LoRA (Low-Rank Adaptation)**: add small trainable matrices on top of frozen weights
- Much cheaper: trains ~1% of parameters
- Almost same quality as full fine-tuning for most tasks

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(r=8, lora_alpha=16, target_modules=["q_proj", "v_proj"])
model = get_peft_model(base_model, config)
```

## Fine-tuning Evaluation
Always eval your fine-tuned model vs the base model + prompt:
- Does it outperform on your specific task?
- Does it regress on general tasks?
- Is the format more consistent?
