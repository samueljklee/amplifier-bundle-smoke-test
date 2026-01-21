# Smoke Test Guide

## Purpose

Comprehensive sanity check: "Does the installed Amplifier CLI generally work?"

This is a **safety check, not a debugger**:
- **Pass** = Everything works, carry on
- **Fail** = Something needs attention, investigate

## Test Coverage

### Phase 1-6: CLI Tests (No LLM Required)

| Phase | Command | What's Verified |
|-------|---------|-----------------|
| CLI Core | `--version`, `--help` | CLI binary works |
| CLI Config | `bundle list/current` | Bundle system works |
| CLI Config | `provider list/current` | Provider system works |
| CLI Config | `source list` | Source system works |
| CLI Resources | `module list` | Module registry works |
| CLI Resources | `agents list` | Agent registry works |
| CLI Resources | `session list` | Session storage works |
| CLI Resources | `tool list` | Tool registry works |
| Profile Commands | `profile list/current` | Profile system works |
| Recipe Validation | `recipes operation=validate` | Recipe system works |
| Source Override | `source add/remove --help` | Source commands exist |

### Phase 7-12: LLM Tests (Require Provider)

| Phase | What's Tested | Expected Token |
|-------|---------------|----------------|
| Multi-Provider | All available providers respond | PROVIDER_*_OK |
| Session CRUD | Full session lifecycle | SESSION_CREATE_OK, SESSION_RESUME_OK |
| Mention Validation | @mention file reading | SMOKE_PHRASE_ALPHA, SMOKE_PHRASE_BETA |
| Provider Responds | Simple deterministic prompt | SMOKE_TEST_OK |
| Tool Execution | Bash tool via LLM | TOOL_OK |
| Agent Delegation | Task tool to sub-agent | AGENT_OK |

## Usage

### From Conversation

```
"Run smoke tests"           # Full suite
"Smoke test --skip-llm"     # CLI only, no API calls
"Smoke test quick"          # Same as --skip-llm
```

### Direct CLI Invocation

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

## Expected Output

### All Pass
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

### With Skips (No Provider Credentials)
```
------------------------------------------------------------
SUMMARY: 6 passed, 0 failed, 6 skipped (of 12)
------------------------------------------------------------

============================================================
SMOKE TEST: PASS
============================================================

Safe to proceed: YES
```

### On Failure
```
------------------------------------------------------------
SUMMARY: 10 passed, 2 failed, 0 skipped (of 12)
------------------------------------------------------------

FAILED TESTS:
  - Session CRUD
  - Agent Delegation

============================================================
SMOKE TEST: FAIL
============================================================

Safe to proceed: NO
```

## When to Run

- After installing Amplifier on a new machine
- After updating Amplifier
- After making changes to configuration
- After changing provider credentials
- Before starting important work
- When something feels "off"

## Troubleshooting

### LLM Tests Skipped

If LLM tests are skipped, check:
1. Provider credentials are set (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, etc.)
2. Provider is configured: `amplifier provider list`
3. Network connectivity to provider APIs

### Session CRUD Fails

Common causes:
- Session storage directory permissions
- Disk space issues
- Corrupted session files

Try: `amplifier session cleanup --force`

### Agent Delegation Fails

Common causes:
- Foundation bundle not installed
- Agent not found in registry
- Provider timeout

Check: `amplifier agents list` to verify foundation:explorer exists

## Multi-Provider Testing

The smoke test automatically detects and tests all available providers:

| Provider | Required Environment |
|----------|---------------------|
| OpenAI | `OPENAI_API_KEY` |
| Anthropic | `ANTHROPIC_API_KEY` |
| Azure OpenAI | `AZURE_OPENAI_ENDPOINT` + credentials |
| Ollama | Ollama running at localhost:11434 |

Providers without credentials are skipped (not failed).

## Timing

| Mode | Approximate Time |
|------|------------------|
| Full suite | 3-5 minutes |
| CLI only (`--skip-llm`) | 30 seconds |

The majority of time is spent on LLM tests due to API latency.
