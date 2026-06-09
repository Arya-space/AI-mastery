# FastAPI Basics

FastAPI is the standard Python framework for building LLM API backends.

## Minimal Setup
```bash
pip install fastapi uvicorn
```
```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class ChatRequest(BaseModel):
    message: str

class ChatResponse(BaseModel):
    reply: str

@app.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    # call LLM here
    reply = call_llm(request.message)
    return ChatResponse(reply=reply)
```
```bash
uvicorn main:app --reload
```

## Async Endpoints (critical for LLM calls)
LLM calls are I/O-bound — always use `async def`:
```python
@app.post("/chat")
async def chat(request: ChatRequest):
    response = await async_llm_call(request.message)
    return {"reply": response}
```
Sync endpoints block the server thread. Async lets FastAPI handle other requests while waiting for LLM response.

## Auto-generated Docs
FastAPI auto-generates OpenAPI docs at `/docs` (Swagger UI) and `/redoc`.

## Dependency Injection
```python
from fastapi import Depends

def get_llm_client():
    return OpenAI()

@app.post("/chat")
async def chat(request: ChatRequest, client: OpenAI = Depends(get_llm_client)):
    ...
```
Use for: database connections, API clients, auth.

## Key Patterns
- Always use Pydantic models for request + response
- Use `async def` for all LLM endpoints
- Return HTTP 422 automatically if request model validation fails
- Add `/health` endpoint for monitoring
