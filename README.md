# Iterative asset extraction

An agent pipeline and workspace for scoped, reproducible asset extraction, categorization, and visualization specification using REA.

## Prerequisites

REA is an independent reverse-engineering tool suite registered globally as an MCP server. Before using this workspace:

1. Follow the [REA setup guide](https://github.com/morluto/rea#readme) to install `rea-agents` and register the MCP server with your coding agent.
2. Verify that your agent client recognizes the REA toolset. Once registered globally, agents use REA tools directly without rerunning setup checks.

## Quick start

1. Clone the repository and enter the directory:
   ```bash
   git clone https://github.com/4163/i-asset-extraction.git
   cd i-asset-extraction
   ```

2. Run the asset extraction pipeline via the [asset extraction skill](.agents/skills/asset-extraction/SKILL.md):
   ```text
   /asset-extraction "<path/to/game_or_project>" "<target_scope>"
   ```

   Example:
   ```text
   /asset-extraction "/path/to/game" "CharacterName"
   ```

## Pipeline roadmap and checkpoints

The pipeline runs through four progressive stages. At each checkpoint, the agent pauses, reports deliverables, and awaits user confirmation before advancing.

| Stage | Focus | Primary deliverables | Checkpoint report |
| :--- | :--- | :--- | :--- |
| **Stage 1** | Entity and reference mapping | `specs/mapping.md`<br>`meta/manifest.json`<br>`meta/state.json` | Report resolved aliases, identifiers, and catalog verification results. |
| **Stage 2** | Target-mirrored extraction | `assets/`<br>`gallery/index.html` | Host local gallery (`http://localhost:<port>`) to manually check files and flag missing assets. |
| **Stage 3** | Asset categorization | `assets/audio/`<br>`assets/textures/`<br>`assets/text/`<br>`specs/dependent-assets.md` | Report categorized standalone files, provenance ledger, and compound dependency clusters. |
| **Stage 4** | Visualization specification | `specs/visualization-spec.md` | Present runtime and rendering blueprints for final sign-off. |

- **Stage 1 (Mapping)**: Resolves target IDs, aliases, and container hashes across package tables. Verifies zero dangling references.
- **Stage 2 (Extraction)**: Mirrors assets into `assets/` preserving original paths. Flags client-purged or out-of-scope files in the local gallery.
- **Stage 3 (Categorization)**: Organizes standalone files into `assets/audio/`, `assets/textures/`, and `assets/text/`, leaves compound rigs anchored in original paths, and documents the pre-organization directory trace in `specs/dependent-assets.md`.
- **Stage 4 (Specification)**: Documents material bindings, hierarchies, and runtime recommendations in `specs/visualization-spec.md`.

## Directory layout

- `workspace/`: Extracted files, categorized assets, decompiled pseudocode, and generated specifications. Each target project receives an isolated subfolder.
- `tools/`: Optional external reverse-engineering utilities and engines (such as Ghidra, JADX, or Kuna) when required as fallbacks.
- `cache/`: Temporary extraction dumps, streams, and evidence bundles.
- `.agents/`: Workspace rules and specialized agent skills, including the asset extraction workflow.

## License

[MIT](LICENSE)
