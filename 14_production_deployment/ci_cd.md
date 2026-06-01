# CI/CD for AI Services

**CI (Continuous Integration)**: automatically test on every push.
**CD (Continuous Deployment)**: automatically deploy on merge to main.

## Why CI/CD Matters for AI Apps
- LLM output can degrade with prompt changes → run evals in CI to catch regressions
- Model API updates can break your app → catch in tests before production
- Fast, reliable deployment → ship improvements quickly

## GitHub Actions Workflow
```yaml
# .github/workflows/deploy.yml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run unit tests
        run: pytest tests/

      - name: Run evals (on PR only, skip on main push for speed)
        if: github.event_name == 'pull_request'
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: python run_evals.py --threshold 0.85

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t my-ai-app:${{ github.sha }} .

      - name: Push to registry
        run: docker push myregistry/my-ai-app:${{ github.sha }}

      - name: Deploy to cloud
        run: ./scripts/deploy.sh ${{ github.sha }}
```

## Running Evals in CI
Key principle: don't ship a prompt change that drops eval score below threshold:
```python
# run_evals.py
import sys
score = run_eval_suite()
print(f"Eval score: {score:.2f}")
if score < float(sys.argv[2]):  # --threshold
    print("FAIL: score below threshold")
    sys.exit(1)  # fails the CI job
```

## Secrets in CI
Store API keys as GitHub Secrets (Settings → Secrets and variables → Actions):
```yaml
env:
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```
Never hardcode in the workflow file.
