---
---
Refine ads.txt managerdomain compatibility fallback semantics:

- add a 404-only compatibility fallback path in `AdAgentsManager.validateDomain`:
  - when `https://{publisher}/.well-known/adagents.json` is missing, inspect `https://{publisher}/ads.txt`
  - parse eligible `MANAGERDOMAIN=` directives and attempt manager-hosted `/.well-known/adagents.json`
  - if manager validation succeeds, return that result while preserving the original publisher `domain` and `url`
- enforce fallback safety controls:
  - max fallback depth of one hop
  - cycle detection for manager lookups
  - `#noagents` trailing-token opt-out on managerdomain directive lines
  - no fallback on non-404 responses
- normalize directive semantics:
  - support only explicit `MANAGERDOMAIN=` directive lines (case-insensitive key)
  - ignore comment-only `# managerdomain=...` lines
  - when multiple eligible directives are present, use the **last** eligible entry in file order
- document this as a legacy compatibility fallback and keep managed-network guidance (`authoritative_location`) as the normative modern pattern
- include unit-test coverage for success, cycle/depth limits, noagents, non-404, comment-only ignore, invalid-token skip, and multiple-entry selection
