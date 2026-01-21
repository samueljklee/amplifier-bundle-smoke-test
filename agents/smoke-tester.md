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

After completion: Summarize X passed, Y failed, Z skipped. Verdict: PASS or FAIL.
