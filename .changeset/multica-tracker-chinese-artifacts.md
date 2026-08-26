---
"mattpocock-skills": patch
---

Add Multica as a first-class issue tracker option (new `issue-tracker-multica.md` seed template driving the `multica` CLI; labels and blocking edges live in issue metadata, wayfinder maps as parent issues with `--stage` ordering). Localize human-facing artifacts to Chinese: the spec template, both ticket templates, agent briefs, triage notes, `CONTEXT.md`/ADR formats, and the wayfinder map body, with machine tokens (triage label values, `Status:`/`Blocked by:` lines, statuses) kept in English. Setup now records an Artifact language rule in the `## Agent skills` block, and the architecture review report is written in Chinese.
