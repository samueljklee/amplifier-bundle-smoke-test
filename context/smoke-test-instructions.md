# Smoke Test Instructions

## CRITICAL: Agent Delegation Required

**When the user asks to run smoke tests, you MUST delegate to the `smoke-test:smoke-tester` agent.**

DO NOT attempt to run the recipe directly. The bundle path `smoke-test:recipes/...` will NOT resolve correctly.

### Trigger Phrases

Any of these user requests require immediate delegation:

| User Says | Action |
|-----------|--------|
| "run smoke test" | Delegate to smoke-test:smoke-tester |
| "smoke test" | Delegate to smoke-test:smoke-tester |
| "run smoke tests" | Delegate to smoke-test:smoke-tester |
| "smoke test --skip-llm" | Delegate with skip_llm instruction |
| "quick smoke test" | Delegate with skip_llm instruction |

### How to Delegate

**Full smoke test:**
```
task(agent="smoke-test:smoke-tester", instruction="Run full smoke tests")
```

**Skip LLM tests (faster):**
```
task(agent="smoke-test:smoke-tester", instruction="Run smoke tests with skip_llm=true")
```

### Why Delegation is Required

1. The smoke-tester agent knows how to find the recipe in the cache
2. It uses absolute paths that always resolve correctly
3. It handles all the complexity of recipe execution
4. It provides a clean summary without verbose output

### What NOT to Do

- DO NOT call `recipes(operation="execute", recipe_path="smoke-test:recipes/...")` directly
- DO NOT try to find or run the recipe yourself
- DO NOT attempt to debug or retry if the agent fails

Just delegate and report what the agent returns.
