---
name: style-creator
description: Create or edit STYLE rule packages with the STYLE CLI. Use when the user requests a STYLE.yaml file, a .style.md package, or conversion of coding conventions into STYLE rules. Does not apply to CSS styling or general agent skill creation.
---

# STYLE creator

Create local STYLE packages that express the user's requested rules. Validate the files and preview their behavior for the confirmed target harnesses.

## Confirm the requirements

Before creating or editing a package, ask the user to confirm its name, destination path, target harnesses, package format, and rule behavior, including triggers and actions. Present known values together and ask for any missing values. Wait for a reply before writing files. Do not replace confirmation with assumptions or defaults. Ask again only if these choices change.

Recommend a directory package with `STYLE.yaml` and the content files it needs. Offer `NAME.style.md` for a single independent ordinary rule. A single-file package cannot contain multiple rules or `native_hooks`.

Read existing files before editing them. Preserve the confirmed structure and existing content. Deliver local files unless the user requests installation, linking, or synchronization. Creating or validating a package does not authorize those operations.

## Read the documentation

Use the official STYLE documentation for specifications and examples. Fetch the Markdown pages below, not the HTML site, which renders client-side. [llms.txt](https://style.pixulse.com/llms.txt) lists every page. Read the pages needed for the task. Do not maintain a local copy of the manual.

- Read [overview](https://style.pixulse.com/docs/style-yaml/overview.md) and [authoring](https://style.pixulse.com/docs/style-yaml/authoring.md) for package formats and authoring workflows.
- Read [package](https://style.pixulse.com/docs/style-yaml/package.md), [requires](https://style.pixulse.com/docs/style-yaml/requires.md), [budget](https://style.pixulse.com/docs/style-yaml/budget.md), and [defaults](https://style.pixulse.com/docs/style-yaml/defaults.md) for metadata, dependencies, settings, budgets, and defaults.
- Read [rules](https://style.pixulse.com/docs/style-yaml/rules.md), [target](https://style.pixulse.com/docs/style-yaml/target.md), [action](https://style.pixulse.com/docs/style-yaml/action.md), [when](https://style.pixulse.com/docs/style-yaml/when.md), and [fallback](https://style.pixulse.com/docs/style-yaml/fallback.md) for sources, content, targets, actions, conditions, and fallback behavior.
- Read [native hooks](https://style.pixulse.com/docs/style-yaml/native-hooks.md) when the package must run commands or scripts.
- Read [CLI](https://style.pixulse.com/docs/style-yaml/cli.md) and [diagnostics](https://style.pixulse.com/docs/style-yaml/diagnostics.md) for CLI usage and reported errors.

Check `command -v style`, `style --version`, and `style --help` to identify the executable. Use `style help COMMAND` to check its options. If the binary differs from the documentation, explain the difference and use the features that version supports. If the website is unavailable, identify the details you could not verify. If the CLI is unavailable, write the confirmed package and report that validation was not run. Do not install dependencies without authorization.

## Write the package

For a new directory package, run `style init NAME` in the intended parent directory. Use `style init NAME --single` for a single-file package. `NAME` is a package name, not a path. Edit an existing package without initializing it again. Replace generated examples with the requested content and remove unused files generated during this task.

Choose the rule target that matches the confirmed behavior.

| Intent | Configuration |
|---|---|
| Persistent coding or communication conventions | Use `target: system`. |
| Reminders before or after a tool call | Use `target: tool` and set `at: before` or `after`. |
| Rules for an MCP tool or an existing skill | Use `target: mcp` with `mcp: server/tool`, or `target: skill` with `skill: name`. |
| A trigger on a normalized event | Use `target: event` with an event such as `tool.pre`. |
| Command or script execution | Use top-level `native_hooks` with a mapping for each target harness. |

Use `inject` to insert content, `warn` to issue a warning, and `block` to prevent execution. Match the requested level of enforcement. Use `mode: replace` only when the user intends to replace existing system instructions.

Define conditions in `when` using fields available for the selected tool and event. Give blocking rules specific conditions. Prepare a matching example and a non-matching example. If a compiled index matches the current package, use `style explain --index ...` with test payloads to inspect the result. It does not read source packages. Do not validate a new package against an unrelated index, install it without authorization, or execute commands meant to be blocked.

Set `repeat: every` for every execution, or use `turn` or `session` for the requested frequency. Include `reason` when an `inject` rule uses `every` or `turn`. `repeat` has no effect on blocking rules.

Use inline `text` for short content and `file: prompts/NAME.md` for longer content. To keep rule metadata with its body, place both in `rules/NAME.md` and reference the file with `from`. Preserve existing IDs and keep IDs unique across rules and native hooks. Declare settings in `settings` and reference them with `{{settings.NAME}}`. Supply reasonable defaults where possible and keep credentials out of the package.

Verify native hook scripts and paths. The harness receives `command` unchanged, without STYLE template expansion. Do not assume the package directory is the working directory. Explain any path configuration needed on other machines.

## Validate and deliver

Run these commands with the package path and confirmed harnesses.

```bash
style validate ./my-style --json
style plan ./my-style --harness codex,claude-code --json
```

Use the `.style.md` path for a single-file package. Explicit harness selection permits offline planning. It does not prove the harness is installed or its installation route is available. For a requested project preview, place the global option before the subcommand, as in `style --project /path/to/project plan ./my-style --harness codex --json`. Use boolean flags such as `--json` without a value.

Check the exit code, `status`, `diagnostics`, rule mappings, and token budgets. Review `degraded`, `partial`, `skipped`, `never-trigger`, and `install-error` results for changes to the requested behavior. Fix file, path, and condition errors. Report capability limits without changing `block` to `warn`, increasing budgets, or setting `fallback: skip` to hide the problem. A native hook without a harness mapping is skipped even with `fallback: error`.

Report the file paths, rule behavior, validation results, and relevant harness differences. Identify checks you did not run. A preview does not install or activate the package. The `--json` flag controls output format and does not make a command a dry run.
