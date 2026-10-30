---
name: asset-extraction
description: Trigger on /asset-extraction or whenever asked to map, extract, organize, or document targeted assets from a game or binary directory.
argument-hint: "<project_path> <entity_scope_query>"
---

# Asset extraction pipeline

A structured, non-destructive pipeline for targeted asset extraction, categorization, and presentation specification from game or application binaries.

## Invocation

```text
/asset-extraction <target_path> <scope_instruction>
```

Examples:
- Initial extraction: `/asset-extraction "/path/to/game_or_project" "CharacterName"`
- Weapon or item scope: `/asset-extraction "/path/to/game_or_project" "WeaponSlug"`
- Project patch update: `/asset-extraction "/path/to/game_or_project" "update CharacterName to v1.2"`

## Core principles and constraints

1. **Strictly non-destructive.** Never modify, overwrite, or delete source project files. All extractions and analyses write only to dedicated REA directories.
2. **Dedicated project namespaces.** Root folders in REA must not become scratch dumps. Every target project receives an isolated slug folder.
3. **Disk boundaries.** Keep all heavy analysis dumps and extraction outputs inside this repository. Never write heavy analysis dumps or game binaries to system drives or other drives.
4. **Divergence and audit tracking.** Never discard missing assets silently. When mapped files are missing on disk (such as purged events or delisted DLC) or cut by scope, document the cause in `meta/manifest.json` and surface notice cards in the visual gallery.
5. **Stage gating.** Complete each roadmap stage fully before proceeding. Stop and present stage findings to the user for discussion. Never finalize prematurely.
6. **Strict provenance.** Assets must originate exclusively from the target binary/project files or official endpoints referenced in code. Never scrape fan wikis, fandom sites, or third-party repositories. If official game code points to a third-party host or CDN, flag it in the stage document for user review.
7. **Direct REA tooling.** Use REA MCP tools (`open_binary`, `inspect_artifact`, `extract_artifact`, `inspect_il2cpp_metadata`, etc.) directly for analysis and extraction. Standalone engines in `tools/` serve only as fallbacks for proprietary containers.
8. **Clean path hygiene.** Never embed host machine absolute drive paths (such as `C:`, `E:\Games`, or `file:///`) in deliverable markdown specs, manifests, or templates. Record clean client titles (such as `Genshin Impact v7.1.0`) and relative repository links (`[specs/mapping.md](specs/mapping.md)`).

## Workspace layout

For a project identified as `<project_slug>`:

```text
<repository_root>/
├── workspace/
│   └── <project_slug>\
│       ├── meta\               # Project state, hash manifests, alias registries
│       │   ├── manifest.json   # Asset index and source container hashes
│       │   └── state.json      # Pipeline progress, entity IDs, divergence data
│       ├── assets\             # Project assets
│       │   ├── audio\          # BGM, one-shots, isolated voicelines
│       │   ├── textures\       # Standalone UI sprites, splash CGs, backgrounds
│       │   ├── text\           # Subtitle files, dialogue trees, localized strings
│       │   └── <compound_dirs>\# Preserved relative hierarchies for meshes, rigs, animations
│       ├── gallery\            # Stage 2 interactive visual confirmation gallery
│       │   └── index.html      # Self-contained visual confirmation page
│       └── specs\              # Stage deliverable documents
│           ├── mapping.md
│           ├── dependent-assets.md
│           └── visualization-spec.md
└── cache\
    └── <project_slug>\         # Temporary uncompressed streams, evidence dumps
```

### Patch updates and divergence tracking

Before extracting, check `meta/manifest.json`.

1. **Incremental patch updates.** When updating an existing project or advancing client versions:
   - Resolve update scope against official changelogs, package manifests, or user instructions.
   - Diff package container hashes against `meta/manifest.json` to isolate only new or modified archives.
   - Map, extract, and categorize newly introduced assets while leaving untouched files in place.
   - Append new items to deliverables in `specs/`, refresh `gallery/index.html` with update badges, and log the patch delta in `meta/state.json`.
2. **Divergence and missing assets.** When cataloged files are missing on disk (such as purged limited events or delisted DLC) or excluded by scope:
   - Record missing paths, categories, and causes in `meta/manifest.json` and `meta/state.json`.
   - Surface notice cards in `gallery/index.html` detailing the divergence so missing files are never silently dropped.

## Exploration loop: confidence ladder and verification

Do not report Stage 1 mapping as complete until every branch for the user-specified scope is fully explored. The agent must loop through inspection until both stopping criteria are met:

### 1. Confidence ladder

Ask and positively verify each of the following five conditions before concluding mapping:
- **Identity resolution.** Have all localized entity names (EN, JP, CN, KR), alternative character names, alternate personas, alter egos, titles, form variants (such as awakened, corrupted, or seasonal forms), internal developer codenames, numeric IDs, string slugs, voice actor tags, and hash keys been gathered from game configuration tables and string pools?
- **Catalog coverage.** Have all package indices, manifest tables, asset catalogs, or archive directories been queried against every resolved identifier?
- **Reference graph traversal.** For every matched asset, were dependency manifests, material bindings, and code cross-references followed to find dependent children or parent references?
- **Peripheral domain scan.** Were related domains (weapons, skills, companion entities, narrative lore, voice banks) inspected for links to this entity?
- **Negative search boundary.** Did targeted regex and hex sweeps across string pools and binary imports yield zero uncataloged occurrences of any resolved slug?

If any check is incomplete, continue looping through the project tables and binary references.

### 2. Concrete code verification

Run a deterministic query or script in the workspace to verify catalog completeness:
- Load the resolved alias and ID list.
- Cross-reference all entries in the game package index or archive manifest.
- Confirm that `set(cataloged_assets) == set(referenced_assets)` for the target scope.
- Verify zero unresolved or dangling asset paths exist for the entity.

## 4-Stage roadmap

### Stage 1: Entity and reference mapping

Discover every file and reference tied to the user instruction.

#### Search breadth
Do not limit queries to the literal name provided by the user. Resolve and track:
- Alternative character names, aliases, titles, and epithets. Characters often carry multiple identities, civilian versus combat personas, alter egos, or distinct transformation states (such as awakened, corrupted, boss, or seasonal variants).
- English, Japanese, Chinese, Korean, and other localized display names.
- Internal developer codenames, character production codes, and design slugs.
- Database primary keys, numeric IDs, and asset bundle tags.
- Voice actor identifiers and audio group names.

#### Output organization
Structure `specs/mapping.md` into two clear sections. All item types listed below are non-exhaustive examples to guide discovery rather than rigid boundaries. Include any asset types or data structures that emerge from the target game.

1. **Direct target assets.** Assets that belong directly to the entity and its known personas or forms (illustrative examples):
   - Primary meshes, textures, rigs, skins, costume variants, and alternate persona models.
   - Character voiceline audio, battle callouts, conversation dialog, and persona-specific voice tracks.
   - Character-specific animation sequences, cutscene emotes, and posture data.
   - Core configuration stats, skill tree data, and numerical parameters across all forms.
   - *Text, transcripts, and descriptions:*
     - Voiceline transcripts, battle callout lines, and subtitle tables across all localized languages.
     - Character profile biographies, lore summaries, flavor text, titles, and epithets.
     - Story dialogue scripts, quest conversation transcripts where the character speaks, and scene trees.
     - Lobby greetings, affinity and bond unlock lines, gift reaction text, and idle banter transcripts.
     - Skill descriptions, talent writeups, passive ability breakdowns, and status effect tooltips.
2. **Related and associated entities.** Items that relate to or reference the entity (illustrative examples; capture any associated game systems discovered):
   - *Signature weapons and equipment:* Weapons, relics, armor sets, exclusive accessories, and their attached lore text.
   - *Companions and summons:* Familiars, combat pets, drones, mounts, summoned minions.
   - *Combat skills and VFX:* Skill particle systems, projectile meshes, aura shaders, hit sparks.
   - *Audio banks:* Dedicated theme music, combat sound cues, ambient stingers.
   - *UI and branding:* Avatar portraits, dialog busts, gacha banners, ability badges, faction emblems.
   - *Narrative lore and external references:* Lore books, archival records, other characters' voicelines or dialogue about this entity, event quest transcripts, and achievement text.

**Checkpoint:** Save `specs/mapping.md`. Present the mapping findings and the verified alias table to the user. Wait for user discussion and confirmation before moving to Stage 2.

### Stage 2: Target-mirrored extraction

Extract cataloged assets into `assets/` preserving internal package paths.

#### Requirements
- Replicate internal package paths (e.g., `assets/Assets/Characters/<Codename>/...` or `assets/Game/Characters/...`) to preserve relative linkages.
- Use REA MCP tools (`open_binary`, `inspect_artifact`, `extract_artifact`).
- Check provenance. Reject third-party sources. If official assets reference an external CDN, document the URI and request confirmation before retrieving.

#### Visual confirmation gallery
Before proceeding to Stage 3, verify extracted coverage in an interactive local gallery:
- Build `gallery/index.html` from `resources/gallery-template.html`, linking directly to `assets/` files without copying.
- Assign an appropriate extracted icon or portrait as the page favicon.
- Surface notice cards for missing, purged, or out-of-scope assets.

**Checkpoint:** Save extraction counts, hashes, and divergence notes in `meta/manifest.json`. Host `gallery/index.html` on a local HTTP server, share the `http://localhost:<port>` URL, and wait for confirmation before moving to Stage 3.

### Stage 3: Asset categorization

Partition assets in `assets/` into standalone media and compound clusters.

#### Category A: Standalone assets
Files that function independently without runtime dependencies:
- **Audio:** Background music tracks, ambient loops, sound effects, voice clips (organized into `assets/audio/`).
- **Graphics:** Standalone UI icons, background CGs, splash illustrations, promotional art (organized into `assets/textures/`).
- **Text:** Localized subtitle files, voiceline transcripts, character bios, lore documents, dialogue trees (organized into `assets/text/`).

Move standalone files into their respective category directories under `assets/`.

*Dual-use items:* Assets that stand alone but are also referenced by compound assets (such as an icon reused on a 3D billboard) are organized into `assets/textures/` and cross-referenced in `specs/dependent-assets.md`.

#### Category B: Dependent and compound assets
Interdependent asset groups remain anchored in their original extracted paths to preserve relative file references:
- **3D skeletal rigs:** Meshes (`.fbx`, `.obj`, `.gltf`), texture sets, material definitions (`.mat`), skeletons, skinning weights, animations (`.anim`), LOD tiers, collision hulls, cloth configs.
- **2D skeletal puppets (Live2D / Spine):** Model definitions (`.moc3`, `.model3.json` or `.skel`, `.json`), texture atlases, coordinate files (`.atlas`), physics configs (`.physics3.json`), motion tracks (`.motion3.json`), expressions (`.exp3.json`).
- **VFX particle systems:** Emitter settings, flipbook textures, ribbon shaders, distortion maps.
- **Composite UI systems:** Prefabs, SDF font atlases, 9-patch slice definitions, UI animators.

#### Output organization
Structure `specs/dependent-assets.md` into three parts:
1. **Pre-organization snapshot:** The raw directory tree and file inventory as extracted in Stage 2.
2. **Provenance ledger:** A mapping table tracking `Original package path`, `Organized path`, and `SHA-256 hash` for all moved standalone files.
3. **Compound clusters:** Inventory of intact multi-file assemblies, their preserved relative paths, and required runtime dependencies.

**Checkpoint:** Save `specs/dependent-assets.md`. Present the categorization summary, provenance ledger, and compound asset inventory to the user. Discuss dual-use files and obtain confirmation before moving to Stage 4.

### Stage 4: Presentation and visualization specification

Create `specs/visualization-spec.md` to pave the way for rendering, previewing, or building a standalone repository for the compound assets identified in Stage 3.

#### Requirements
- **Factual asset clustering.** Document each compound asset group, file roles, and internal reference paths in `assets/` (e.g., which textures bind to which material slots, which bone hierarchy drives the mesh).
- **Runtime recommendations.** Provide concrete, accurate runtime and rendering recommendations matching the original engine representation. Listed runtimes are non-exhaustive examples to guide selection; recommend the exact runtime matching the discovered asset formats:
  - *Live2D:* Live2D Cubism Web SDK, PixiJS Live2D display, or official Cubism Viewer.
  - *Spine:* Spine Web Player or Spine-Pixi with matching runtime version.
  - *3D models:* Three.js, Babylon.js, or model-viewer with PBR workflow, or direct Godot/Unity scene import.
- **No speculative implementation code.** Do not generate full application boilerplate or repository code. Keep the specification factual and architectural, giving the user a blueprint to build upon.

**Checkpoint:** Present `specs/visualization-spec.md` to the user for final discussion and sign-off. Remind the user that the project is ready to be staged and published to a public community repository via `/contribute-output <project_slug>`.

---

## Contribute and publish

Once Stage 4 visualization specifications are approved, the project can be contributed to the public catalog:

```text
/contribute-output <project_slug>
```

This workflow verifies clean relative paths, stages an export repository with live GitHub Pages gallery, catalogs external tools used during extraction, and opens a pull request against `https://github.com/4163/i-asset-extraction` to index the project.
