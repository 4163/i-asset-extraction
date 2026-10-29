# Iterative asset extraction

An agent pipeline and workspace for scoped, reproducible asset extraction, categorization, and visualization specification using REA.

## Prerequisites

REA is an independent reverse-engineering tool suite registered globally as an MCP server. Before using this workspace:

1. Follow the [REA setup guide](https://github.com/morluto/rea#readme) to install `rea-agents` and register the MCP server with your coding agent.
2. Verify that your agent client recognizes the REA toolset. Once registered globally, agents use REA tools directly without rerunning setup checks.

## Quick start

Clone the repository and start an agent session in the root folder:

```bash
git clone https://github.com/4163/i-asset-extraction.git
cd i-asset-extraction
```

Run the asset extraction pipeline:

```text
/asset-extraction "<path/to/game_or_project>" "<target_scope>"
```

Example:

```text
/asset-extraction "/path/to/game" "CharacterName"
```

## Directory layout

- `workspace/`: Extracted files, staged libraries, decompiled pseudocode, and generated specifications. Each target project receives an isolated subfolder.
- `tools/`: Optional external reverse-engineering utilities and engines (such as Ghidra, JADX, or Kuna) when required as fallbacks.
- `cache/`: Temporary extraction dumps, streams, and evidence bundles.
- `.agents/`: Workspace rules and specialized agent skills, including the asset extraction workflow.

## License

[MIT](LICENSE)
