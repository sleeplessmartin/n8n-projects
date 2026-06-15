# n8n Workflow Builder

A prompt-driven environment for building AI agents and automation workflows in n8n. Describe what you want, and Claude builds it using n8n-mcp MCP tools and n8n skills.

## 🎯 Overview

This project enables rapid workflow creation through natural language:
- **Prompt-based workflow creation** — Describe your automation in plain English
- **Live n8n integration** — Connect directly to your n8n instance via the n8n-mcp MCP server
- **Expert knowledge** — Leverages specialized n8n skills for node configuration, expressions, code patterns, and validation

## 📁 Project Structure

- **`workflows/`** — Contains n8n workflow JSON files exported and managed within this project
- **`.mcp.json`** — Configuration for the n8n-mcp MCP server
- **`Claude.md`** — Documentation and setup guide for this project

## ⚙️ Configuration

### Prerequisites
- Access to an n8n instance
- n8n-mcp MCP server configured and credentials set

### Setup
The n8n-mcp server is configured in `.mcp.json` at the project root:
- **Instance:** `https://n8n-space-station.onrender.com`
- **API key:** Configured in `.mcp.json` (do not commit credentials to version control)

### Verification
Run `n8n_health_check()` at the start of any session to verify the connection is live.

## 🛠️ Available Tools

### MCP Tools (n8n-mcp)
Core tools for workflow management and node operations:
- `search_nodes` — Find nodes by keyword
- `get_node` — Inspect node operations and configuration
- `validate_node` / `validate_workflow` — Validate node or full workflow config
- `n8n_create_workflow` — Create new workflows
- `n8n_update_partial_workflow` — Iteratively edit workflows (primary workflow tool)
- `n8n_autofix_workflow` — Auto-fix validation errors
- `search_templates` / `get_template` — Browse 2,700+ template library
- `n8n_deploy_template` — Deploy templates directly to your instance
- `n8n_list_workflows` / `n8n_get_workflow` — Inspect existing workflows
- `n8n_manage_credentials` — Create, list, update, delete credentials
- `n8n_manage_datatable` — Manage n8n data tables
- `n8n_audit_instance` — Security audit
- `ai_agents_guide()` — Comprehensive guide for building AI agent workflows

**Important:** Always consult the `n8n-mcp-tools-expert` skill before calling any MCP tool to prevent common mistakes.

### Specialized Skills
Domain-specific knowledge for workflow development:
- `n8n-mcp-tools-expert` — Master guide for all MCP tools
- `n8n-workflow-patterns` — Architectural patterns for common automations
- `n8n-node-configuration` — Operation-specific required fields per node
- `n8n-expression-syntax` — `{{ }}` expression syntax and variable references
- `n8n-code-javascript` — Writing JavaScript in Code nodes
- `n8n-code-python` — Writing Python in Code nodes (when explicitly requested)
- `n8n-validation-expert` — Interpreting and fixing validation errors

## 📋 Standard Workflow Building Process

Follow this sequence when building new workflows:

1. **Understand the request** — Clarify trigger, services, logic, and output
2. **Search for nodes** — Use `search_nodes` to find available nodes
3. **Inspect node config** — Use `get_node` with `detail: "standard"` to review operations
4. **Build iteratively** — Start with `n8n_create_workflow`, then use `n8n_update_partial_workflow` incrementally
5. **Validate** — Run `validate_workflow` after each significant change
6. **Activate** — Use `activateWorkflow` operation when ready

**Best practice:** Never attempt to build a complete workflow in a single API call. Build node-by-node, validate, then connect.

## 🔑 Critical Conventions

### Node Type Formats
Different tools expect different nodeType formats:
- **Search/validate tools:** `nodes-base.slack`, `nodes-langchain.agent`
- **Workflow create/update tools:** `n8n-nodes-base.slack`, `@n8n/n8n-nodes-langchain.agent`

Use `workflowNodeType` when calling workflow tools.

### Node Updates
Always use `updates` for updateNode operations, not `parameters`:
```json
{ "type": "updateNode", "nodeName": "HTTP Request", "updates": { "url": "..." } }
```

### Credentials
Credentials must be nested by type:
```json
{ "credentials": { "httpHeaderAuth": { "id": "abc123", "name": "My Key" } } }
```

### Connections
Use branch/case for smart connections:
```json
{ "type": "addConnection", "source": "IF", "target": "True Handler", "branch": "true" }
```

### Workflow Updates
Always include `intent` on workflow updates:
```json
{ "id": "wf-123", "intent": "Add error handling", "operations": [...] }
```

## 📦 Workflow Creation Quick Start

### Building a New Workflow
1. **Check for templates first** — Use `search_templates` or `n8n_deploy_template` for quick wins
2. **For AI agents** — Call `ai_agents_guide()` before building to understand patterns
3. **For Code nodes** — Consult `n8n-code-javascript` skill for proper syntax
4. **Validate carefully** — Use `validate_workflow` with `profile: "runtime"` before activating

### Managing Credentials
1. **List existing credentials** — `n8n_manage_credentials({action: "list"})`
2. **Create new credentials** — Use `getSchema` to discover required fields for credential types
3. **Security reminder** — Never commit credentials to version control; store in `.mcp.json`

## 📚 Additional Resources

- **Detailed reference** — See [Claude.md](Claude.md) for in-depth documentation
- **Template library** — Access 2,700+ pre-built workflow templates
- **Node documentation** — Use `get_node` to inspect any node's operations and requirements

## 🚀 Getting Started

1. Verify the connection:
   ```bash
   n8n_health_check()
   ```

2. Describe your workflow in natural language

3. Claude will build it using the tools and patterns documented above

## 📝 Notes

- All work happens through n8n-mcp tools — no system file modifications or external package installations needed
- The project is designed for rapid iteration and validation at each step
- Use the skills liberally to ensure correct syntax and configuration before deployment
