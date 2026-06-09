# What Is Tool Calling

**Tool calling** (also: function calling) lets an LLM request the execution of a predefined function. The model decides when and with what arguments to call a tool — you execute it and return the result.

## The Problem It Solves
LLMs can't:
- Access real-time data (weather, stock prices)
- Query databases
- Send emails, make API calls
- Run code

Tool calling bridges LLMs and the real world.

## The Tool Call Cycle
```
User: "What's the weather in Tokyo?"
    ↓
LLM sees tools list, decides to call get_weather
    ↓
LLM returns: {tool: "get_weather", args: {location: "Tokyo"}}
    ↓
Your code executes get_weather("Tokyo") → "25°C, sunny"
    ↓
You send result back to LLM
    ↓
LLM generates: "The weather in Tokyo is 25°C and sunny."
```

## OpenAI Tool Calling
```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current weather for a city",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "City name"}
            },
            "required": ["location"]
        }
    }
}]

response = client.chat.completions.create(
    model="gpt-4o", messages=[...], tools=tools
)

if response.choices[0].finish_reason == "tool_calls":
    tool_call = response.choices[0].message.tool_calls[0]
    args = json.loads(tool_call.function.arguments)
    result = get_weather(**args)
    # Send result back to LLM...
```

## Parallel Tool Calls
Model can call multiple tools in one response (e.g., weather for 3 cities simultaneously). Execute in parallel, return all results.

## Key Point
The LLM never executes anything. It only **requests** tool calls. Your code runs them. You control what gets executed.
