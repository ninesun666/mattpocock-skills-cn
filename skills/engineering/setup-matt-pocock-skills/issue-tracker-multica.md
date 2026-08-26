# Issue tracker: Multica

Issues and specs for this repo live as issues in a Multica workspace. Use the `multica` CLI for all operations, always with `--profile oppo` as recorded below, and `--output json` for every read.

- Instance: `https://ee-multica.oppoer.me/` (our self-hosted deployment; never `desktop-ee-multica.oppoer.me`). Issue links are `https://ee-multica.oppoer.me/<workspace-slug>/issues/<KEY>`.
- Profile: `oppo` (the authenticated local CLI profile for this instance)
- Workspace: `<workspace-id>` (team default: ColorOS AgentWorks, id `37f7fcf6-b58b-4841-842f-05c350824026`, slug `coloros-agentworks`)
- Project new issues file under: `<project-id, or "the workspace default">`

Write all issue titles and bodies in Chinese. Machine tokens stay in English: triage label values, `Status:` and `Blocked by:` lines, and the mention protocol.

## Conventions

- **Create an issue**: write the body to a UTF-8 markdown file first, then `multica --profile oppo issue create --title "<title>" --description-file <body.md> [--project <project-id>] [--parent <parent-id>] [--stage <n>] [--status todo] --output json`. Never pass long bodies inline: the shell rewrites backticks, `$()` expressions, and newlines before the CLI receives them.
- **Read an issue**: `multica issue get <id-or-key> --output json`. Both the numeric id and the key (like `MUL-123`) resolve.
- **Read comments**: `multica issue comment list <id> --recent 10 --output json`, or `--thread <comment-id> --tail 30` for a single thread.
- **List issues**: `multica issue list [--status <s>] [--project <id>] [--limit N] --output json`.
- **Update a body**: read the current description, edit it, write to a file, then `multica issue update <id> --description-file <body.md>`. Always read before writing: other sessions may be editing the tracker concurrently.
- **Comment**: write to a UTF-8 file, then `multica issue comment add <id> [--parent <comment-id>] --content-file <comment.md>`.
- **Children**: `multica issue children <id> --output json`.
- **Linked pull requests**: `multica issue pull-requests <id> --output json`.

## Statuses and side effects

Multica statuses: `backlog | todo | in_progress | in_review | done | blocked | cancelled`. `done` and `cancelled` are terminal.

Status changes and assignment are side-effecting: on an issue assigned to a Multica agent they can enqueue an agent run. Keep the assignee a human member unless the user explicitly wants to trigger the Multica runtime.

For ordered sub-issues, use `--stage` (earlier tickets get lower stages) and park later tickets in `backlog`; they move to `todo` as their blockers clear.

## Triage labels

Multica has no native labels. The five triage roles (see `triage-labels.md`) and the two category roles live in issue metadata:

- `multica issue metadata set <id> --key triage --value needs-triage` (or `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`)
- `multica issue metadata set <id> --key category --value bug` (or `enhancement`)
- Read them back with `multica issue metadata list <id> --output json`

When a skill says "apply a label", set the metadata key. When it says "close as wontfix", post the closing comment first, then `multica issue status <id> cancelled` (cancelled, not done: nothing was built).

## Pull requests as a triage surface

**PRs as a request surface: no.** Multica reads linked PRs per issue (`issue pull-requests`); external-PR triage is not part of this repo's queue.

## When a skill says "publish to the issue tracker"

Create a Multica issue (body via `--description-file`). For a set of tickets, create one issue per ticket in dependency order (blockers first), each with `--parent` set to the spec or parent issue where one exists and `--stage` set to its position in that order; later tickets start as `backlog`. Set the `ready-for-agent` triage metadata key unless instructed otherwise. Do not close or modify the parent issue.

## When a skill says "fetch the relevant ticket"

`multica issue get <id-or-key> --output json`, plus `multica issue comment list <id> --recent 10 --output json` for the discussion.

## Blocking edges

Multica has no native issue-dependency API, so record each edge in two places:

1. **Metadata (machine-read)**: `multica issue metadata set <id> --key blocked_by --value "MUL-101,MUL-102"`, a comma-separated list of blocking issue keys.
2. **Body (human-read)**: a `Blocked by: MUL-101, MUL-102` line near the top of the ticket body, or `None (can start immediately)`.

A ticket is unblocked when every issue in its `blocked_by` metadata has status `done` or `cancelled`; check each with `multica issue get <key> --output json`.

## Wayfinding operations

Used by `/wayfinder`. The **map** is a single parent issue with **child** issues as tickets.

- **Map**: an issue marked by metadata `wayfinder=map` (`multica issue metadata set <id> --key wayfinder --value map`), its body holding the 目标 / 说明 / 已确定的决策 sections. Create it with the body via `--description-file`.
- **Child ticket**: created with `--parent <map-id> --stage <n>`, its body carrying the question. The type lives in metadata: `--key wayfinder_type --value research|prototype|grilling|task`. Once claimed, the ticket is assigned to the driving dev (a human member).
- **Frontier query**: `multica issue children <map-id> --output json`, keeping those that are open (status not `done`/`cancelled`), unassigned, and without an open blocker in their `blocked_by` metadata; lowest `stage` wins.
- **Claim**: `multica issue assign <id> --to <dev>` plus `multica issue status <id> in_progress`, the session's first write, before any work.
- **Resolve**: post the answer as a comment (`--content-file`), then `multica issue status <id> done`, then read-modify-write the map body to append a pointer (the ticket's name wrapping its link, per wayfinder's "Refer by name") to the 已确定的决策 section.
