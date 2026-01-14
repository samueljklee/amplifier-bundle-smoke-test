---
bundle:
  name: smoke-test
  version: 3.0.0
  description: Comprehensive sanity check for installed Amplifier CLI

includes:
  # recipes bundle already includes foundation
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main

agents:
  smoke-tester:
    path: agents/smoke-tester.md
---

# Amplifier Smoke Test Bundle

Comprehensive sanity check: "Does the installed Amplifier CLI generally work?"

## Usage

From any Amplifier conversation:

```
"Run smoke tests"
"Smoke test"
"Smoke test --skip-llm"    # CLI tests only, no API calls
```

Or invoke the agent directly:

```
task(agent="smoke-test:smoke-tester", instruction="Run full smoke tests")
```

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
