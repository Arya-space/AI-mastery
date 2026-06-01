# Function Schemas

A **function schema** is the JSON definition that tells the model what a tool does, what parameters it takes, and which are required. **The model reads your descriptions — write them for the model, not for humans.**

## Schema Structure
```json
{
    "type": "function",
    "function": {
        "name": "search_products",
        "description": "Search the product catalog by keyword. Use this when the user asks about available products, prices, or inventory.",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": "The search keyword or product name"
                },
                "category": {
                    "type": "string",
                    "enum": ["electronics", "clothing", "books"],
                    "description": "Product category to filter by. Omit to search all categories."
                },
                "max_price": {
                    "type": "number",
                    "description": "Maximum price in USD. Omit if no price limit."
                }
            },
            "required": ["query"]
        }
    }
}
```

## Writing Good Descriptions
- **Function description**: when should the model call this? Be explicit.
  - Bad: "Get weather"
  - Good: "Get current weather conditions for a city. Use when user asks about temperature, forecast, or weather."
- **Parameter descriptions**: what format, what units, what values are valid.

## Required vs Optional
- `required`: model must provide these — never optional
- Optional: model can omit; your code should handle None/missing values

## Enum Parameters
Use `"enum"` to constrain to specific values:
```json
"status": {"type": "string", "enum": ["open", "closed", "pending"]}
```
Prevents the model from hallucinating invalid values.

## Tips
- Keep names lowercase_snake_case
- One tool = one action (don't make multi-purpose tools)
- If the model keeps calling the wrong tool, improve the description
