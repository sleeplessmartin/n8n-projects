# n8n Workflow Builder

This project is a prompt-driven environment for building AI agents and automation workflows in n8n. The user describes what they want; Claude builds it using n8n-mcp MCP tools and n8n skills.

## What this project does

- Takes natural-language prompts and turns them into live n8n workflows
- Connects to the user's own n8n instance via the n8n-mcp MCP server
- Uses n8n skills for deep knowledge of node configuration, expressions, code, and patterns

## Setup

The n8n-mcp server is configured in `.mcp.json` at the project root. Credentials are set:
- **Instance:** `https://n8n-space-station.onrender.com`
- **API key:** configured in `.mcp.json` (do not commit to version control)

Run `n8n_health_check()` at the start of any session to verify the connection is live.

## Primary tools

### MCP tools (n8n-mcp)
Always consult the `n8n-mcp-tools-expert` skill before calling any MCP tool — it prevents common mistakes.

Key tools available:
- `search_nodes` — find nodes by keyword
- `get_node` — inspect node operations and config (use `detail: "standard"` by default)
- `validate_node` / `validate_workflow` — validate node or full workflow config
- `n8n_create_workflow` — create a new workflow
- `n8n_update_partial_workflow` — iteratively edit a workflow (most-used tool)
- `n8n_autofix_workflow` — auto-fix validation errors on an existing workflow
- `search_templates` / `get_template` — browse the 2,700+ template library
- `n8n_deploy_template` — deploy a template directly to the n8n instance
- `n8n_list_workflows` / `n8n_get_workflow` — inspect existing workflows
- `n8n_manage_credentials` — create, list, update, delete credentials
- `n8n_manage_datatable` — manage n8n data tables
- `n8n_audit_instance` — security audit
- `n8n_health_check` — verify API connectivity
- `ai_agents_guide()` — comprehensive guide for building AI agent workflows

### Skills (invoke with Skill tool)
- `n8n-mcp-tools-expert` — master guide for all MCP tools; consult first
- `n8n-workflow-patterns` — architectural patterns for common automations
- `n8n-node-configuration` — operation-specific required fields per node
- `n8n-expression-syntax` — `{{ }}` expression syntax and variable references
- `n8n-code-javascript` — writing JavaScript in Code nodes
- `n8n-code-python` — writing Python in Code nodes (use only when explicitly requested)
- `n8n-validation-expert` — interpreting validation errors and fixing them

## Standard workflow for building

1. **Understand the request** — clarify trigger, services, logic, and output if ambiguous
2. **Search for nodes** — `search_nodes` to find what's available
3. **Inspect node config** — `get_node` with `detail: "standard"`
4. **Build iteratively** — `n8n_create_workflow` then `n8n_update_partial_workflow` in steps
5. **Validate** — `validate_workflow` after each significant change
6. **Activate** — `activateWorkflow` operation when ready

Never try to build a complete workflow in a single API call. Build node-by-node, validate, then connect.

## Critical conventions

### nodeType format differs by tool
- Search/validate tools: `nodes-base.slack`, `nodes-langchain.agent`
- Workflow create/update tools: `n8n-nodes-base.slack`, `@n8n/n8n-nodes-langchain.agent`

`search_nodes` returns both formats — use `workflowNodeType` for workflow tools.

### updateNode uses `updates`, not `parameters`
```json
{ "type": "updateNode", "nodeName": "HTTP Request", "updates": { "url": "..." } }
```

### Credentials must be nested by type
```json
{ "credentials": { "httpHeaderAuth": { "id": "abc123", "name": "My Key" } } }
```

### Use branch/case for smart connections
```json
{ "type": "addConnection", "source": "IF", "target": "True Handler", "branch": "true" }
```

### Always include `intent` on workflow updates
```json
{ "id": "wf-123", "intent": "Add error handling", "operations": [...] }
```

## When the user asks for a new workflow

1. Check if a template exists first — `search_templates` or `n8n_deploy_template` for quick wins
2. For AI agent workflows, call `ai_agents_guide()` before building
3. For Code nodes, use the `n8n-code-javascript` skill
4. Validate with `profile: "runtime"` before activating

## Credentials

Check existing credentials with `n8n_manage_credentials({action: "list"})` before creating new ones. Use `getSchema` to discover required fields for a credential type.

## Scope

Do not modify system files, install packages, or run shell commands outside of MCP tool calls. All work happens through n8n-mcp tools and n8n skills.
