---
bundle:
  name: smoke-test
  version: 3.0.0
  description: Comprehensive sanity check for installed Amplifier CLI

includes:
  # recipes bundle already includes foundation
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main
  # Include smoke test runner behavior (loads delegation instructions)
  - bundle: smoke-test:behaviors/smoke-test-runner

agents:
  smoke-tester:
    path: agents/smoke-tester.md
---

# Amplifier Smoke Test Bundle

Comprehensive sanity check: "Does the installed Amplifier CLI generally work?"

## CRITICAL: Delegation Rule

**When the user asks to run smoke tests, ALWAYS delegate to the `smoke-test:smoke-tester` agent.**

The smoke-tester agent knows how to:
1. Find the recipe location in the cache
2. Execute it with the correct absolute path
3. Handle skip_llm mode

**DO NOT try to run the recipe directly.** The bundle path `smoke-test:recipes/...` won't resolve correctly when loaded via git URL.

### How to Invoke

```
task(agent="smoke-test:smoke-tester", instruction="Run full smoke tests")
```

For skip-llm mode:
```
task(agent="smoke-test:smoke-tester", instruction="Run smoke tests with skip_llm=true")
```

## User Commands

| User Says | Delegate To |
|-----------|-------------|
| "run smoke test" | smoke-test:smoke-tester |
| "smoke test" | smoke-test:smoke-tester |
| "smoke test --skip-llm" | smoke-test:smoke-tester (with skip_llm instruction) |
| "quick smoke test" | smoke-test:smoke-tester (with skip_llm instruction) |

## What Gets Tested

### CLI Tests (No LLM Required)

| Phase | Tests |
|-------|-------|
| CLI Core | --version, --help |
| CLI Config | bundle list/current, provider list/current, source list |
| CLI Resources | module list, agents list, session list, tool list |
| Profile Commands | profile list, profile current |
| Recipe Validation | Validate recipe YAML structure |
| Source Override | source list, add/remove --help |

### LLM Tests (Require Provider)

| Phase | Tests |
|-------|-------|
| Multi-Provider | Tests all available providers (OpenAI, Anthropic, Azure, Ollama) |
| Session CRUD | Create, list, show, resume, delete session lifecycle |
| Mention Validation | Read files via @mention, extract known phrases |
| Provider Responds | Simple deterministic prompt response |
| Tool Execution | Bash tool via LLM |
| Agent Delegation | Task tool delegation to sub-agent |

## Philosophy

**Safety check, not debugger.**
- Pass = Everything works, carry on
- Fail = Something needs attention, investigate

Tests your installed instance directly - no containers, no isolation.

## Direct Recipe Invocation

```bash
# Full test suite
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml

# Skip LLM tests (faster, CI-friendly)
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml \
  context='{"skip_llm": true}'
```

## Timing

- Full suite: ~3-5 minutes
- CLI only (--skip-llm): ~30 seconds

---

@smoke-test:context/smoke-test-guide.md
