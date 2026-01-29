# Visor Enterprise Edition

Commercial workflows for [Visor](https://github.com/probelabs/visor).

## Workflows

### code-talk

AI-powered code exploration workflow that answers questions about your codebase with references.

**Features:**
- Multi-project support
- Automatic project routing
- Code references with file paths and line numbers
- GitHub integration for documentation

## Usage

### As a Standard Workflow

```yaml
imports:
  - visor-ee://workflows/code-talk.yaml

checks:
  explore:
    type: workflow
    workflow: code-talk
    args:
      question: "How does authentication work?"
      architecture: |
        # Your project architecture docs
      projects:
        - id: my-project
          repo: your-org/your-repo
```

See `examples/code-talk-workflow.yaml` for a complete example.

### As an AI Custom Tool

```yaml
imports:
  - visor-ee://workflows/code-talk.yaml

checks:
  assistant:
    type: ai
    prompt: "Help the user with their question"
    ai_custom_tools:
      - workflow: code-talk
        args:
          projects:
            - id: my-project
              repo: your-org/your-repo
```

See `examples/code-talk-as-tool.yaml` for a complete example.

## License

This software is proprietary and requires a commercial license. See [LICENSE](./LICENSE) for details.

## Support

Contact: enterprise@probelabs.io
