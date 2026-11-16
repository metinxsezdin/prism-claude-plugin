---
name: prism
description: Use for any task in a Prism Pipeline 2 project - finding assets, shots, scene files, products, versions, playblasts or renders; creating assets, shots, departments and tasks; applying task presets; setting masters, comments, descriptions, metadata, previews or frame ranges; omitting, renaming or deleting shots; or reports such as recent activity, assets missing a product, masters behind the latest version and empty tasks. Also when the user mentions Prism, publish, export, State Manager, master version or a pipeline.json project.
---

# Prism Pipeline 2

This plugin's `prism` MCP server works in the user's Prism project directly, with Prism's own Python and without any
Prism window. The tool list and the exact arguments are in [TOOLS.md](../../PrismMcp/TOOLS.md); read the section of a
tool before its first use in a session.

## Start of a session

1. Call `status`. It shows the Prism user, the active project and Prism's version.
   - `needs_input` with `missing: ["prismUser"]`: Prism has no user name yet. Ask the user for their name; they set it in
     Prism (Settings > User). Do not invent one: every change is saved under that name.
   - `projectProblem`: the project's drive is not connected. Tell the user; most tools need it.
2. If the user names another project, `list_projects`, then `set_project` with `name` or `configPath`. This switches only
   Claude's session. Pass `persist: true` only when the user asks to change their own Prism project too.

The first call boots Prism and can take several seconds; that is normal.

## Entities

Always Prism's own shape:

- asset: `{"type": "asset", "asset_path": "Props/Lotus"}`: category folders, then the asset name
- shot: `{"type": "shot", "sequence": "sq01", "shot": "sh010"}`
- sequence (only for `rename`): `{"type": "sequence", "sequence": "sq01"}`

Before creating anything, `list_entities` (with `path` or `contains`) to see the existing categories and naming, and follow
them. If a name is new to the project, suggest English letters, digits, `_` and `-` only; spaces and accented letters
break tools further down the pipeline.

## Rules that keep the user's data safe

- **`needs_confirmation` means nothing changed yet.** Show the user `details.summary` and `details.changes` in plain words
  and ask. Only after a clear yes, repeat the same call with `confirm: <details.confirmToken>`. Never confirm on your own,
  never reuse a token for another call, and if the repeat answers `needs_confirmation` again (someone changed it
  meanwhile), show the new preview and ask again.
- **Creating is safe:** creating something that exists answers `created: false` and changes nothing. When `ignored` is
  present, tell the user what was not applied and the tool the `note` names.
- **`rename` and `delete_shot`** list `dependents` (scene files elsewhere that use the shot) and folders. Always show
  them; their links break. Assets cannot be renamed.
- `delete_shot` moves folders to the project's `00_Pipeline/Trash`; tell the user that folder path so they can undo.
- Every change is logged to `Documents\Prism2\PrismMcp.log`.

## Errors

| code | do |
|---|---|
| `bad_args` | Fix the call; the message lists valid choices (departments, versions, presets). |
| `needs_input` | Ask the user for what `missing` names. For a missing department: ask, then `create_department`. |
| `source_unavailable` | A drive or file is not reachable. Tell the user which one. |
| `prism_unavailable` | Prism is not installed or did not start. Show the message. |
| `internal` | Prism refused, often a file open in another program (`details.locked`). Ask the user to close it. |

## Common jobs

- **"What happened this week?"** `report` with `kind: "recent_activity"` (optional `since`), then summarise by person
  (`byUser`) and by asset.
- **"Which assets have no model yet?"** `report` `kind: "missing_products"` with the team's product pattern, e.g. `*_model`.
- **"Which masters are out of date?"** `report` `kind: "master_behind"`; offer `set_master` for each, one confirmation each.
- **New asset ready for an artist:** `list_task_presets` → `create_entity` with `taskPreset`; or `create_entity`, then
  `create_department` / `create_task`.
- **Latest version of something:** `list_products` → `list_versions`; the newest is first, `isMaster` marks the master.
- **Playblasts and renders:** `list_media` for the entity; paths can be opened by the user.

## Unity

When the Unity Editor is open with the Prism Unity plugin, the `unity-mcp` server also offers `prism_import`,
`prism_check_updates`, `prism_update`, `prism_save_version` and `prism_playblast`. Use those for anything inside Unity;
the tools here never touch a Unity project.

Answer the user in their own language.
