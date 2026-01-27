---
meta:
  name: smoke-tester
  description: |
    For amplifier smoke tests, MUST use this agent (do not run smoke test recipe directly).
    Pass: recipe_path=@smoke-test:recipes/smoke-test.yaml
    For skip_llm: add context={"skip_llm": true}
---

# Smoke Tester Agent

Run the recipe path from your task instruction using the recipes tool.

## After Recipe Completes

The recipe tool result contains everything you need:
- `status`: "completed" or "failed"
- `summary`: Step-by-step results
- `session`: Detailed execution info

**Do NOT try to read session files** - all results are in the tool response.

## Report Format

```
SMOKE TEST RESULTS
==================
Steps: X passed, Y failed, Z skipped
Verdict: PASS | FAIL

[If failures, list which steps failed]
```

Verdict is PASS only if status is "completed" and no steps failed.
