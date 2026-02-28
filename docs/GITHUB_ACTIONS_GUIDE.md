# 🚀 GITHUB ACTIONS - LEARN WHILE BUILDING

## What Are GitHub Actions?

Robots that work for you when things happen on GitHub.

**For Prometheus:** Auto-notify you when agents complete work!

---

## Your Use Case

**With Actions:**
```
Agent commits → GitHub Actions → SMS to Kevin!
```

No webhook server needed!

---
## Example: SMS Notification

**File:** `.github/workflows/notify-kevin.yml`

```yaml
name: Notify Kevin

on:
  push:
    branches: ['agent-*']

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Send SMS
        env:
          TELNYX_API_KEY: ${{ secrets.TELNYX_API_KEY }}
          KEVIN_PHONE: ${{ secrets.KEVIN_PHONE }}
        run: |
          AGENT=$(echo ${GITHUB_REF#refs/heads/} | sed 's/agent-//' | tr '[:lower:]' '[:upper:]')
          MESSAGE=$(git log -1 --pretty=%B)
          
          curl -X POST https://api.telnyx.com/v2/messages \
            -H "Authorization: Bearer $TELNYX_API_KEY" \
            -H "Content-Type: application/json" \
            -d '{
              "from": "+12065551234",
              "to": "'$KEVIN_PHONE'",
              "text": "🤖 '$AGENT' Agent: '$MESSAGE'"
            }'
```

---

## Setup Secrets

1. Go to repo Settings > Secrets > Actions
2. Add `TELNYX_API_KEY` (your Telnyx API key)
3. Add `KEVIN_PHONE` (your phone number)

---

## How to Add

```bash
cd prometheus-universal-gateway
mkdir -p .github/workflows
# Create notify-kevin.yml with content above
git add .github/workflows/
git commit -m "Add notifications"
git push
```

---

## Test It

```bash
git checkout -b agent-research
echo "test" > test.txt
git commit -m "Gate 1 complete"
git push origin agent-research

# You get SMS!
```

---

**Learn more:** https://docs.github.com/en/actions
