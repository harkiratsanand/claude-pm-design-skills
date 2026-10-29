# PM & Design Skills Export

Exported from personal Claude Code instance on 2026-10-28.

## Install on the work machine

1. Copy the files into the global Claude directory:

   ```bash
   mkdir -p ~/.claude/skills ~/.claude/commands
   cp -R skills/* ~/.claude/skills/
   cp commands/* ~/.claude/commands/
   ```

2. The four core skill files are NOT auto-loaded by their presence alone —
   they must be imported from `~/.claude/CLAUDE.md`. Add these lines
   (create the file if it doesn't exist):

   ```markdown
   @skills/product-management.md
   @skills/requirements-discipline.md
   @skills/design.md
   @skills/interface-design.md
   ```

3. `plan-ceo-review/` and `plan-eng-review/` are directory-style skills —
   they load on demand via the Skill tool / slash invocation, no CLAUDE.md
   import needed.

4. Commands are available immediately as `/prd`, `/prd-lite`, `/discover`,
   `/plan`, `/uat`, `/retro`, `/eval`, `/localbacklog`.

5. Restart any running Claude Code sessions to pick up the new CLAUDE.md.

## Contents

### skills/
- product-management.md — PRD/spec structure, discovery, metrics, roadmap,
  GTM, compliance prerequisites, accumulated build learnings
- requirements-discipline.md — load-bearing decision interview, PRD critique,
  blocking rules, single-source-of-truth rules
- design.md — design critique, WCAG AA accessibility, UX writing, handoff,
  accumulated UI learnings
- interface-design.md — interface craft system (anti-default discipline,
  tokens, layering, typography)
- plan-ceo-review/ — CEO/founder-mode plan review
- plan-eng-review/ — eng-manager-mode plan review

### commands/
- prd.md, prd-lite.md — PRD writing partner (full and solo-project versions)
- discover.md — solo project discovery
- plan.md — solo build plan
- uat.md — user acceptance testing
- retro.md — session retrospective
- eval.md — eval-first domain specification
- localbacklog.md — project backlog capture

## Not included (bring separately if you adopt /orchestrate)
- orchestrate.md, agent-discipline.md, contract-completeness.md — the
  multi-agent build pipeline. These are coupled to each other; move them
  as a set or not at all.
