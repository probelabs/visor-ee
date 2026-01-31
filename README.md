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
- Output includes routed project IDs and detailed metadata

**Outputs (key fields):**
- `answer`: final response object
- `references`: file references
- `projects_explored`: list of routed project IDs
- `projects_explored_details`: list of `{id, repo, description, reason}`

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

### intent-router

Reusable intent classification workflow for assistant routing.

**Features:**
- Configurable intent catalog (id + description + guidance)
- Short question rewriting (topic)
- Works with Slack/GitHub thread context

**Outputs (key fields):**
- `intent`: selected intent ID
- `topic`: short rewritten question

**Example:**
```
checks:
  route-intent:
    type: workflow
    workflow: intent-router
    args:
      question: "Please summarize this thread and highlight action items."
      intents:
        - id: chat
          description: general Q&A or small talk
        - id: thread_summary
          description: user explicitly asks for a summary of the thread
        - id: code_help
          description: questions about code, debugging, or implementation details
```

## License

This software is proprietary and requires a commercial license. See [LICENSE](./LICENSE) for details.

## Support

Contact: hello@probelabs.com
