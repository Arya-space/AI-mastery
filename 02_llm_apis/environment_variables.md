# Environment Variables

## Never Hardcode API Keys
```python
# BAD — key in source code, will be committed to git
client = OpenAI(api_key="sk-abc123")

# GOOD — read from environment
import os
client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])
```

## python-dotenv
```bash
pip install python-dotenv
```
```
# .env file
OPENAI_API_KEY=sk-abc123
ANTHROPIC_API_KEY=sk-ant-xyz
DATABASE_URL=postgresql://...
```
```python
from dotenv import load_dotenv
load_dotenv()  # loads .env into os.environ

import os
key = os.environ["OPENAI_API_KEY"]
```

## .env Best Practices
- Add `.env` to `.gitignore` immediately
- Commit `.env.example` with empty values as a template
- Never log or print env vars

## FastAPI with env vars
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    openai_api_key: str
    anthropic_api_key: str
    debug: bool = False

    class Config:
        env_file = ".env"

settings = Settings()
```

## Production: Secret Management
- AWS: Secrets Manager or Parameter Store
- GCP: Secret Manager
- Azure: Key Vault
- Kubernetes: Secrets
- Never put secrets in Docker images or CI/CD logs
