# Visor Enterprise Edition

Commercial workflows for [Visor](https://github.com/probelabs/visor).

## Workflows

### assistant

High-level AI assistant workflow with declarative configuration. Combines intent routing, dynamic tool selection, knowledge embedding, and code exploration into a single configurable workflow.

**Features:**
- Declarative intent and tag configuration
- Automatic knowledge injection based on tags/intents
- Dynamic MCP server loading based on tags/intents
- Built-in code exploration integration
- No boilerplate - just declare your config

**Example:**
```yaml
imports:
  - visor-ee://workflows/assistant.yaml

checks:
  ask:
    type: human-input
    prompt: "How can I help?"

  chat:
    type: workflow
    depends_on: [ask]
    workflow: assistant
    args:
      question: "{{ outputs['ask'].text }}"

      # Define intents
      intents:
        - id: chat
          description: general Q&A
        - id: code_help
          description: code questions

      # Define tags
      tags:
        - id: codebase
          description: needs code exploration
        - id: jira
          description: needs Jira access

      # Knowledge injected based on tags
      knowledge:
        - tags: [capabilities]
          content: |
            ## Capabilities
            I can help with code exploration and Jira integration.
        - tags: [jira]
          content: |
            ## Jira Tools
            Use jira_get_issue to fetch tickets.

      # MCP servers loaded based on tags
      mcp_servers:
        - tags: [jira]
          name: atlassian
          server:
            command: uvx
            args: [mcp-atlassian]
            env:
              JIRA_URL: "${JIRA_URL}"

      # Code exploration config
      code_config:
        enabled: true
        architecture: "# My Architecture"
        docs_repo: my-org/docs
        projects:
          - id: backend
            repo: my-org/backend
            description: Backend services
    on_success:
      goto: ask
```

**Inputs:**
- `question`: The user's message
- `intents`: Array of `{id, description}` for intent classification
- `tags`: Array of `{id, description}` for tag classification
- `skills`: Array of skill objects (see Skill Configuration below)
- `knowledge`: Array of `{tags, intent, content}` for conditional knowledge injection
- `mcp_servers`: Array of `{tags, intent, name, server}` for conditional tool loading
- `code_config`: Object with `{enabled, architecture, docs_repo, projects, ...}`
- `system_prompt`: Base system prompt
- `guidelines`: Additional guidelines

**Skill Configuration:**

Each skill in the `skills` array supports:
- `id` (required): Unique skill identifier
- `description`: When this skill should activate (used by the classifier)
- `requires`: Array of other skill IDs to auto-activate
- `knowledge`: Context injected when the skill is active
- `tools`: Object mapping tool names to MCP server / workflow configs
- `allowed_commands`: Bash command patterns this skill may run (e.g., `['git:log:*', 'npm:test']`)
- `disallowed_commands`: Bash command patterns this skill must not run (e.g., `['git:push:--force']`)

When skills are activated, their `allowed_commands` and `disallowed_commands` are collected and passed to the AI agent via `ai_bash_config_js`, dynamically extending the static `bashConfig`.

**Outputs:**
- `text`: The AI response
- `intent`: Classified intent
- `tags`: Classified tags
- `topic`: Rewritten question

---

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
- `confidence`: evidence confidence (`high` | `medium` | `low`)
- `confidence_reason`: why confidence is not high (empty when confidence is high)
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
- Optional tag classification (capabilities/resources)
- Works with Slack/GitHub thread context

**Outputs (key fields):**
- `intent`: selected intent ID
- `topic`: short rewritten question
- `tags`: array of selected tag IDs (optional)

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
      tags:
        - id: jira
          description: request references Jira tickets or needs Jira data
        - id: confluence
          description: request references Confluence pages or needs Confluence data
```

## License

This software is proprietary and requires a commercial license. See [LICENSE](./LICENSE) for details.

## Support

Contact: hello@probelabs.com
