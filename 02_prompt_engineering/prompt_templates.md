# Prompt Templates

A **prompt template** is a reusable prompt with placeholders filled at runtime.

## Basic Pattern (f-string)
```python
def build_prompt(document: str, question: str) -> str:
    return f"""You are a helpful assistant.

Answer the question based only on the document below.

Document:
\"\"\"
{document}
\"\"\"

Question: {question}

Answer:"""
```

## Jinja2 Templates (for complex prompts)
```python
from jinja2 import Template

template = Template("""
You are {{ role }}.
{% if examples %}
Examples:
{% for ex in examples %}
Input: {{ ex.input }} → Output: {{ ex.output }}
{% endfor %}
{% endif %}
Task: {{ task }}
""")

prompt = template.render(role="analyst", task="summarize this", examples=[...])
```

## Template Best Practices
- Store templates as files or in a DB, not hardcoded in functions
- Version them (v1, v2) — prompts change like code
- Keep variable names descriptive (`{user_query}` not `{q}`)
- Test templates with edge cases (empty strings, very long inputs)

## Managing Templates
- Keep a `prompts/` directory with versioned prompt files
- Log which prompt version was used for each request
- Never change a prompt without running evals first
