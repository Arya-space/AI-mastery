# Docker Basics

Docker packages your app + dependencies into a container that runs identically everywhere.

## Why Docker for AI Apps
- Reproducible environment (no "works on my machine")
- Easy deployment to any cloud
- Isolates Python deps, system libraries
- Enables horizontal scaling

## Dockerfile for FastAPI + LLM Service
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies first (for layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy app code
COPY . .

# Expose port
EXPOSE 8000

# Run
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Build and Run
```bash
docker build -t my-ai-app .
docker run -p 8000:8000 -e OPENAI_API_KEY=$OPENAI_API_KEY my-ai-app
```

## Managing Secrets in Containers
Never bake API keys into the image. Pass at runtime:
```bash
# Environment variable
docker run -e OPENAI_API_KEY=sk-... my-ai-app

# Or use a .env file
docker run --env-file .env my-ai-app

# Or in production: inject from secrets manager via orchestration
```

## Docker Compose (Local Dev)
```yaml
# docker-compose.yml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    env_file: .env
    volumes:
      - .:/app  # live reload for dev
  redis:
    image: redis:7
    ports:
      - "6379:6379"
```
```bash
docker compose up
```

## Image Size Optimization
- Use `python:3.11-slim` not `python:3.11` (saves ~700MB)
- Use `.dockerignore` to exclude: `.git`, `__pycache__`, `*.pyc`, `.env`
- Multi-stage builds for even smaller images
