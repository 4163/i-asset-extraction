# Iterative asset extraction

An agent pipeline and workspace for scoped, reproducible asset extraction, categorization, and visualization specification using REA. See [examples and community extractions](#community-extractions).

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

The pipeline runs through four progressive stages. At each checkpoint, the agent pauses, reports deliverables, and awaits user confirmation before advancing. All stage specification documents are generated under `workspace/<project_slug>/specs/`.

While the skill and workflow itself is very comprehensive, it doesn't explore options and paths outside of the initial scope you have provided. So if you feel like the agent hasn't been comprehensive enough, provide it with a broader or more specific scope at stage 2 and keep iterating. This way the extraction process and its output meets your initial expections.

For example, asking  the agent (or directly linking the sources) to audit fandoms, forums, and other external sources for the scope you have provided, by following the workflow of stage 1 again.

| Stage | Focus | Primary deliverables | Checkpoint report |
| :--- | :--- | :--- | :--- |
| **Stage 1** | Entity and reference mapping | `specs/mapping.md`<br>`meta/manifest.json`<br>`meta/state.json` | Report resolved aliases, identifiers, and catalog verification results. |
| **Stage 2** | Target-mirrored extraction & user review | `assets/`<br>`gallery/index.html` | Host local gallery (`http://localhost:<port>`) for user review to inspect coverage, flag missing assets, and coordinate recovery. |
| **Stage 3** | Asset categorization | `assets/audio/`<br>`assets/textures/`<br>`assets/text/`<br>`specs/dependent-assets.md` | Report categorized standalone files, provenance ledger, and compound dependency clusters. |
| **Stage 4** | Visualization specification | `specs/visualization-spec.md` | Present runtime and rendering blueprints for final sign-off. |

- **Stage 1 (Mapping)**: Resolves target IDs, aliases, and container hashes across package tables. Verifies zero dangling references.
- **Stage 2 (Extraction & user review)**: Mirrors assets into `assets/` preserving original paths, generates `gallery/index.html`, and hosts it locally for user inspection. The user reviews extracted coverage to identify any missed or expected assets (such as past limited-time event audio or version-exclusive items). When assets were pruned from the current client, the user directs the agent to investigate patch notes or community wikis, locate historical package archives, and recover the missing files before advancing.
- **Stage 3 (Categorization)**: Organizes standalone files into `assets/audio/`, `assets/textures/`, and `assets/text/`, leaves compound rigs anchored in original paths, and documents the pre-organization directory trace in `specs/dependent-assets.md`.
- **Stage 4 (Specification)**: Documents material bindings, hierarchies, standalone presentation transforms, and runtime recommendations in `specs/visualization-spec.md`.

## Community extractions

| Game | Entity | Version | Repository | Assets | Contributor |
|---|---|---|---|---|---|
| Genshin Impact | Faruzan | v7.1.0 | [faruzan-assets](https://github.com/4163/faruzan-assets) | 1,660 | [@4163](https://github.com/4163) |
| Arknights: Endfield | Laevatain / Surtr | v1.6.3 | [laevatain-assets](https://github.com/4163/laevatain-assets) | 2,652 | [@4163](https://github.com/4163) |

Submissions are not limited to extraction repositories, feel free to open a pull request to add your project to the table. Projects that used the workflow, extracted assets, or found the (`specs/visualization-spec.md`) mapping and recommendendations useful, are more than welcome here.

## Contribute (optional)

If you choose to share your extraction with the community, you can manually publish your repository and open a pull request against the table above, or automate the staging and publishing process using the [contribute-output skill](.agents/skills/contribute-output/SKILL.md):

```text
/contribute-output "<project_slug>"
```

This skill sanitizes paths, deploys the interactive gallery to GitHub Pages, and indexes the project in the community table above.

## Directory layout

- `workspace/`: Extracted files, categorized assets, decompiled pseudocode, and generated stage specifications (`workspace/<project_slug>/specs/`). Each target project receives an isolated subfolder.
- `tools/`: Optional external reverse-engineering utilities and engines (such as Ghidra, JADX, or Kuna) when required as fallbacks.
- `cache/`: Temporary extraction dumps, streams, and evidence bundles.
- `.agents/`: Workspace rules and specialized agent skills, including the asset extraction workflow.

## License

[MIT](LICENSE)
