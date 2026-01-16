---
bundle:
  name: smoke-test
  version: 3.0.0
  description: Comprehensive sanity check for installed Amplifier CLI

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main#subdirectory=bundles/minimal.yaml
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main
  - bundle: smoke-test:behaviors/smoke-test-runner

agents:
  include:
    - smoke-test:smoke-tester

spawn:
  tools: [tool-recipes]  # ALL spawned agents ONLY get recipes tool
---

# Amplifier Smoke Test Bundle

Comprehensive sanity check: "Does the installed Amplifier CLI generally work?"

## CRITICAL: Delegation Rule

**When the user asks to run smoke tests, ALWAYS delegate to the `smoke-test:smoke-tester` agent.**

```
task(agent="smoke-test:smoke-tester", instruction="Run full smoke tests")
```

For skip-llm mode:
```
task(agent="smoke-test:smoke-tester", instruction="Run smoke tests with skip_llm=true")
```

| User Says | Action |
|-----------|--------|
| "run smoke test" | Delegate to `smoke-test:smoke-tester` |
| "smoke test" | Delegate to `smoke-test:smoke-tester` |
| "smoke test --skip-llm" | Delegate with skip_llm instruction |
| "quick smoke test" | Delegate with skip_llm instruction |

**DO NOT** try to run the recipe directly or explore the repo. The agent knows what to do.

## Usage

**In conversation:**
```
"run smoke test"           # Full suite
"smoke test --skip-llm"    # CLI only (faster)
```

**Direct invocation:**
```bash
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml

# Skip LLM tests (faster, CI-friendly)
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml \
  context='{"skip_llm": true}'
```

## What Gets Tested

| Category | Tests |
|----------|-------|
| CLI Core | --version, --help |
| CLI Config | bundle, provider, source commands |
| CLI Resources | module, agents, session, tool commands |
| Recipe System | Recipe validation |
| Source Override | source list, add/remove |
| Multi-Provider | All available providers (OpenAI, Anthropic, Azure, Ollama) |
| Session CRUD | Create, list, show, resume, delete |
| @Mentions | File reading via @mention |
| Provider | Simple prompt response |
| Tool Execution | Bash tool via LLM |
| Agent Delegation | Task tool to sub-agent |

## Philosophy

**Safety check, not debugger.**
- Pass = Everything works, carry on
- Fail = Something needs attention, investigate

## Timing

- Full suite: ~3-5 minutes
- CLI only (--skip-llm): ~30 seconds

---

@smoke-test:context/smoke-test-guide.md
