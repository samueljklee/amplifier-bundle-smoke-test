# Amplifier Smoke Test Bundle

Comprehensive sanity check for the installed Amplifier CLI.

## Quick Start

```bash
# Install the bundle
amplifier bundle add git+https://github.com/samueljklee/amplifier-bundle-smoke-test --app

# Run from conversation
amplifier
> Run smoke tests

# Or invoke directly
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml
```

## What Gets Tested

### CLI Tests (No LLM Required) - ~30 seconds

| Phase | Tests |
|-------|-------|
| CLI Core | `--version`, `--help` |
| CLI Config | `bundle list/current`, `provider list/current`, `source list` |
| CLI Resources | `module list`, `agents list`, `session list`, `tool list` |
| Profile Commands | `profile list`, `profile current` |
| Recipe Validation | Validates recipe YAML structure |
| Source Override | `source list`, `add/remove --help` |

### LLM Tests (Require Provider) - ~3-5 minutes

| Phase | Tests |
|-------|-------|
| Multi-Provider | Tests all available providers (OpenAI, Anthropic, Azure, Ollama) |
| Session CRUD | Create, list, show, resume, delete session lifecycle |
| Mention Validation | Read files via @mention, extract known phrases |
| Provider Responds | Simple deterministic prompt response |
| Tool Execution | Bash tool via LLM |
| Agent Delegation | Task tool delegation to sub-agent |

## Usage Options

### From Conversation

```
"Run smoke tests"           # Full suite (~3-5 min)
"Smoke test --skip-llm"     # CLI only (~30 sec)
"Smoke test quick"          # Same as --skip-llm
```

### CLI Direct

```bash
# Full suite
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml

# Skip LLM tests
amplifier tool invoke recipes \
  operation=execute \
  recipe_path=@smoke-test:recipes/smoke-test.yaml \
  context='{"skip_llm": true}'
```

## Philosophy

**Safety check, not debugger.**

- **Pass** = Everything works, carry on
- **Fail** = Something needs attention, investigate

Tests your installed instance directly - no containers, no isolation.

## Output Example

```
============================================================
AMPLIFIER SMOKE TEST RESULTS (Extended)
============================================================

CLI Core:                 PASS
CLI Config:               PASS
CLI Resources:            PASS
Profile Commands:         PASS
Recipe Validation:        PASS
Source Override:          PASS
Multi-Provider:           PASS
Session CRUD:             PASS
Mention Validation:       PASS
Provider Responds:        PASS
Tool Execution:           PASS
Agent Delegation:         PASS

------------------------------------------------------------
SUMMARY: 12 passed, 0 failed, 0 skipped (of 12)
------------------------------------------------------------

============================================================
SMOKE TEST: PASS
============================================================

Safe to proceed: YES
```

## When to Run

- After installing Amplifier on a new machine
- After updating Amplifier
- After changing provider configuration
- Before starting important work
- When something feels "off"

## Requirements

- Amplifier CLI installed and on PATH
- For LLM tests: At least one provider configured with credentials

## License

MIT
