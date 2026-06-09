# Schemas

A **schema** defines the structure, types, and constraints of your expected output.

## JSON Schema Basics
```json
{
  "type": "object",
  "properties": {
    "name": {"type": "string"},
    "age": {"type": "integer", "minimum": 0},
    "tags": {"type": "array", "items": {"type": "string"}}
  },
  "required": ["name", "age"]
}
```

## OpenAI Structured Outputs with Schema
```python
response = client.chat.completions.create(
    model="gpt-4o",
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "person_schema",
            "schema": {
                "type": "object",
                "properties": {
                    "name": {"type": "string"},
                    "age": {"type": "integer"}
                },
                "required": ["name", "age"],
                "additionalProperties": False
            },
            "strict": True
        }
    },
    messages=[...]
)
```

## Claude: Tool Use Pattern for Structured Output
Claude doesn't have a native JSON schema mode. Use tool calling:
```python
tools = [{
    "name": "extract_person",
    "description": "Extract person info",
    "input_schema": {
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "age": {"type": "integer"}
        },
        "required": ["name", "age"]
    }
}]
# Force the model to call the tool
tool_choice = {"type": "tool", "name": "extract_person"}
```

## Nested Schemas
```json
{
  "type": "object",
  "properties": {
    "invoice": {
      "type": "object",
      "properties": {
        "vendor": {"type": "string"},
        "items": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "description": {"type": "string"},
              "amount": {"type": "number"}
            }
          }
        }
      }
    }
  }
}
```
