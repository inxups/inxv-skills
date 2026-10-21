---
name: style-installer
description: Install, link, update, sync, restore, remove, or troubleshoot STYLE rule packages for Codex or Claude Code with the STYLE CLI. Does not install the CLI executable or general agent skills.
---

# STYLE installer

Use the official [CLI reference](https://style.pixulse.com/docs/style-yaml/cli.md) and [diagnostics table](https://style.pixulse.com/docs/style-yaml/diagnostics.md) for the requested operation. These are Markdown pages; the HTML site renders client-side and is unreadable to fetch tools. Do not maintain a local manual. Check `style --version` and `style help COMMAND` before running commands.

## Workflow

1. Read the request, package, and installation records to determine the source or package name, target harnesses, required settings, and scope. Run `style list --json` before changing an existing installation. Ask only for information these sources do not establish and that affects the operation.
2. Before installing a source, run `style validate SOURCE --json` and `style plan SOURCE --harness HARNESS --json`. Check diagnostics, rule mappings, and budgets.
3. Run the requested command and read its actual preview. If it matches the authorized operation, enter `y` in an interactive terminal. A request for a preview or local files alone does not authorize installation.
4. Run `style list --json` in the same scope to verify the result. Report the changes and remaining steps. Use `style doctor --json` when installation problems need investigation.

## Commands

| Request | Command |
|---|---|
| Install a local directory, `.style.md` file, or Git source | `style install SOURCE --harness HARNESS --json` |
| Link a local package for development | `style link SOURCE --harness HARNESS --json` |
| Update a package from its recorded source | `style update NAME --json` |
| Apply development edits or recompile snapshots | `style sync NAME --json` |
| Restore offline from the lock and snapshots | `style restore --json` |
| Remove a package or development link | `style uninstall NAME --json` |

## Operating rules

- Use `style --project DIR COMMAND ...` for project operations. Place a custom `--style-home DIR` before the subcommand as well. Keep inspection, changes, and verification in the same scope. `validate` accepts neither global option.
- Without an explicit harness selection, install and link select all detected harnesses. Stay within the requested scope and use `--all` only when requested.
- Check source syntax, `--set`, routes, and advanced options in the documentation. Replace system instructions or enable `--fail-closed` only when the user intends that behavior. Do not alter rules or budgets to make installation pass.
- `--json` controls output format. Use `plan` to preview without applying changes. The CLI has no `--yes` option. Do not supply an unconditional stream of approvals.
- `sync` may write without prompting. Canceling approval partway through does not roll back packages already applied. Use `update` to refresh copied installations from their original sources.
- Check the exit code, `status`, `diagnostics`, and `result`. A failed command may have written files, and exit code 0 can still carry warnings. `registration-required` (an error for some plugin routes, a warning for others) and `refresh-required` mean the files exist but the harness has not loaded the current plugin. Follow the report's registration commands within the authorized scope.
- Do not use `doctor --reset-state` for routine verification. Do not report a successful preview as an active installation.
