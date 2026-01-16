---
bundle:
  name: smoke-test
  version: 3.0.0
  description: Comprehensive sanity check for installed Amplifier CLI

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main#subdirectory=bundles/minimal.yaml
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main#subdirectory=behaviors/recipes.yaml
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main#subdirectory=behaviors/sessions.yaml
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main#subdirectory=behaviors/streaming-ui.yaml
  - bundle: smoke-test:behaviors/smoke-test-runner
  # Ecosystem expert behaviors (provides @amplifier: and @core: namespaces)
  - bundle: git+https://github.com/microsoft/amplifier@main#subdirectory=behaviors/amplifier-expert.yaml
  - bundle: git+https://github.com/microsoft/amplifier-core@main#subdirectory=behaviors/core-expert.yaml
  # Foundation behaviors
  - bundle: foundation:behaviors/sessions
  - bundle: foundation:behaviors/status-context
  - bundle: foundation:behaviors/redaction
  - bundle: foundation:behaviors/todo-reminder
  - bundle: foundation:behaviors/streaming-ui
  # External bundles
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main#subdirectory=behaviors/recipes.yaml
  - bundle: git+https://github.com/microsoft/amplifier-bundle-design-intelligence@main#subdirectory=behaviors/design-intelligence.yaml
  - bundle: git+https://github.com/microsoft/amplifier-bundle-python-dev@main
  - bundle: git+https://github.com/microsoft/amplifier-module-tool-skills@main#subdirectory=behaviors/skills.yaml
  - bundle: git+https://github.com/microsoft/amplifier-module-hook-shell@main#subdirectory=behaviors/hook-shell.yaml


session:
  orchestrator:
    module: loop-streaming
    source: git+https://github.com/microsoft/amplifier-module-loop-streaming@main
    config:
      extended_thinking: true
  context:
    module: context-simple
    source: git+https://github.com/microsoft/amplifier-module-context-simple@main
    config:
      max_tokens: 300000
      compact_threshold: 0.8
      auto_compact: true

tools:
  - module: tool-filesystem
    source: git+https://github.com/microsoft/amplifier-module-tool-filesystem@main
  - module: tool-bash
    source: git+https://github.com/microsoft/amplifier-module-tool-bash@main
  - module: tool-web
    source: git+https://github.com/microsoft/amplifier-module-tool-web@main
  - module: tool-search
    source: git+https://github.com/microsoft/amplifier-module-tool-search@main
  - module: tool-task
    source: git+https://github.com/microsoft/amplifier-module-tool-task@main
    config:
      exclude_tools: [tool-task]  # Spawned agents do the work, they don't delegate
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
