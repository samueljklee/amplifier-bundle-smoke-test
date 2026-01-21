---
bundle:
  name: smoke-test
  version: 1.0.0
  description: Comprehensive sanity check for installed Amplifier CLI

# Full standalone experience
includes:
  # Foundation provides tools, agents, standard capabilities
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  # Our own behavior (DRY - agent/context registration lives there)
  - bundle: smoke-test:behaviors/smoke-test.yaml
---

# Amplifier Smoke Test Bundle

Comprehensive sanity check: "Does the installed Amplifier CLI generally work?"

## Installation

```bash
amplifier bundle add git+https://github.com/samueljklee/amplifier-bundle-smoke-test --app
```

## CLI Usage

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

@smoke-test:context/smoke-test-guide.md
