# audit-ablacaocc â€” ablation audit for Claude Code

**ðŸ‡§ðŸ‡· [Português](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [Espaê±ol](README.es.md)**

A **diagnostic** (read-only) skill that audits your `CLAUDE.md`, skills, and hooks, classifies each instruction, and proposes a minimal version â€” without touching any files.

> Every line in your configuration is **guilty of complexity until proven useful**.

## ðŸ“– User Guide

Complete guide (landing page + walkthrough): **https://linnoff88-dev.github.io/

- Skill: [`SKILL.md`](SKILL.md)
- Conceptual foundation: [`doc/`](doc/) â€” summary of Boris Chernyâ€™s talk (Y Combinator, one day after the launch of Opus 5)

---

## Table of Contents

1. [Borisâ€™s perspective: why delete your own config](#1-borisâ€™s-perspective-why-delete-your-own-config)
2. [Why this cleanup is useful in practice](#2-why-this-cleanup-is-useful-in-practice)
3. [What the skill does (and never does)](#3-what-the-skill-does-and-never-does)
4. [Installation](#4-installation)
5. [How to use it](#5-how-to-use-it)
6. [Usage tip: call skills all the time](#6-usage-tip-call-skills-all-the-time)
7. [Recommended cycle](#7-recommended-cycle)
8. [Practical guide: publishing to Git and the portal](#8-practical-guide-publishing-to-git-and-the-portal)

---

## 1. Borisâ€™s perspective: why delete your own config

Boris Cherny created Claude Code at Anthropic. His thesis, in summary:

**Configuration gets stale.** Every instruction you write is a fix for a specific modelâ€™s weakness. When the next model no longer has that weakness, the instruction becomes dead weight â€” and keeps being reread every time, taking up context and limiting the model.

Thatâ€™s literally what happened in Claude Code: much of the system prompt existed only to correct behaviors the model should have been able to handle on its own. With Opus 5, the team **removed more than 80% of the system prompt**. What remains is almost entirely safety, permissions, static analysis, and interface.

**Ablation** is the method. Itâ€™s a research term: you remove something to measure its impact. Applied to prompts, it means deleting everything and bringing it back one line at a time, discovering what each line actually does. Borisâ€™s advice for anyone who only *uses* Claude Code:

> Every ~6 months, especially with a major model release: delete your `CLAUDE.md`, delete your skills, delete your hooks â€” and see what the model does without them.

Rebuilding has four steps, and the fourth is the one that matters:

1. Delete the config.
2. Use the tool on **real work**, not a hypothetical test.
3. Observe where it does well and where it stumbles.
4. Only bring back an instruction **after youâ€™ve seen the model repeatedly fail at the same thing**.

There are two reasons for this discipline: youâ€™re a poor predictor of which instructions the model needs, and every retained line costs context on every run.

The other pillars of his perspective:

- **Over-specification is the most common mistake** â€” and itâ€™s more common among people with years of engineering experience. Scripting â€œdo A, then B, then Câ€ worked with older models; today it keeps the model from finding a better way. Write **task + guardrails + completion criteria**, then come back to check.
- **Verification is the lever almost everyone gets wrong.** A short prompt with a real way for the model to check its own work beats a huge prompt without verification. The classic example: rewrite the Electron app in Swift, run the original in a VM, take a screenshot, compare pixel by pixel, and donâ€™t stop until itâ€™s done. Produce â†’ observe â†’ compare â†’ stopping condition.
- **Aim above the ceiling.** Give it a task slightly harder than you think the model can handle, and retest your old failures with every new version â€” the reason for the failure may have disappeared.
- **Working with models has become an empirical science**, not a theoretical one. Itâ€™s more like getting to know a living creature than configuring a system. The right level of instruction is the same as what youâ€™d use to guide a coworker.
- **Thereâ€™s no secret trick.** The cycle is: hard task â†’ ways to verify â†’ observe where it gets stuck â†’ fix *that* (better prompt, or a skill, or an MCP) â†’ repeat.

To test this yourself, Boris mentioned two levers: the system-prompt flag at startup, and `CLAUDE_CODE_SIMPLE=1` (removes all system prompts, including those for tools â€” the ablation baseline used internally). Counterintuitive detail: without the prompts, the model gets *slightly smarter*; theyâ€™re kept because they make the product behave as people expect.

## 2. Why this cleanup is useful in practice

Anyone who has used Claude Code for a while accumulates the same layer of sediment:

| Symptom | What it usually is |
|---|---|
| A 300-line `CLAUDE.md` with dated exceptions | fixes for models that are no longer around |
| The same rule in `CLAUDE.md` and in 3 skills | redundancy that causes conflict when one copy changes |
| A skill that teaches â€œhow to thinkâ€ in 400 lines | generic reasoning the model already does on its own |
| A rigid 12-step walkthrough | micromanagement that blocks a better route |
| Two rules that contradict each other | the model picks one, and you donâ€™t know which |
| Nothing saying *how to check* that itâ€™s correct | the most costly gap of all |

The cost is real and compounded: context burned on every call, reduced autonomy, inconsistent behavior, and zombie rules no one dares delete because no one knows what theyâ€™re holding together anymore.

The benefits of cleanup:

- **Context returned** to the real work.
- **Less contradiction** â€” a rule that exists in only one place canâ€™t conflict with anything.
- **More autonomy** â€” completion criteria let the model find a better path than yours.
- **Auditable config** â€” you know *why* each line is there because each one survived a question.
- **Verification is part of the equation** â€” the audit exposes where thereâ€™s no objective way to prove the work is done correctly.

The goal is **not** to maximize reduction. Itâ€™s to maximize:

**quality + autonomy + verifiability ê· complexity**

Thatâ€™s why the skill explicitly preserves what the model canâ€™t infer: project identity, paths and sources of truth, branding, security, compliance, integrations, interface contracts, and internal conventions.

## 3. What the skill does (and never does)

**It does:**

- read `CLAUDE.md` (project + global), skills, hooks, and `settings.json`;
- classify each instruction â€” `CONTEXT`, `GUARDRAIL`, `QUALITY CRITERION`, `VERIFICATION`, `INTEGRATION/TOOL`, `REPEATABLE PROCEDURE`, `MICROMANAGEMENT`, `REDUNDANCY`, `LEGACY/OBSOLETE`, `AMBIGUOUS/UNPROVEN`;
- decide `KEEP` / `SIMPLIFY` / `MOVE` / `MERGE` / `TEST` / `REMOVE` (when in doubt, `TEST`);
- audit each skill â€” `KEEP` / `SIMPLIFY` / `MERGE` / `SPLIT` / `LOAD-ON-DEMAND` / `CONVERT-TO-CONTEXT` / `DELETE-CANDIDATE`;
- deliver a report in 10 sections: executive summary, metrics, issues by file, removal candidates, redundancies and conflicts, skills, proposed minimal `CLAUDE.md`, proposed skills, ablation test plan (versions A/B/C), and top 10 changes by impact ê· risk.

**It never does:** edit, move, delete, overwrite config, or commit. Itâ€™s diagnostic. Applying changes is your decision, in a separate request â€” by design: an audit that starts changing things is one you canâ€™t trust.

Nearby distinction: `memory-audit` handles session **memory**. This one handles **agent configuration**.

## 4. Installation

```bash
git clone https://github.com/linnoff88-dev/audit-ablacaocc.git
mkdir -p ~/.claude/skills/audit-ablacao
cp audit-ablacaocc/SKILL.md ~/.claude/skills/audit-ablacao/SKILL.md
```

Or, to install only in the current project:

```bash
mkdir -p .claude/skills/audit-ablacao
cp /caminho/audit-ablacaocc/SKILL.md .claude/skills/audit-ablacao/SKILL.md
```

Restart the Claude Code session so it loads the skill.

## 5. How to use it

Direct invocation:

```
/audit-ablacao
```

Or trigger it using natural language:

- â€œaudit my global CLAUDE.md for ablationâ€
- â€œaudit this projectâ€™s skills, my prompt is bloatedâ€
- â€œwhat can I remove from my config without breaking anything?â€

Scopes worth auditing:

| Scope | When |
|---|---|
| Global (`~/.claude/`) | every ~6 months or with a major model release |
| One project | when its `CLAUDE.md` exceeds ~150 lines |
| A set of skills | when 3+ skills compete for the same trigger |

After the report, a healthy workflow is: choose the **Top 10 by impact ê· risk**, apply them in another session, and run the **ablation plan** (versions A / B / C) on real project tasks before deciding the change is good.

## 6. Usage tip: call skills all the time

This is a practice Iâ€™ve adopted that changes the result more than any prompt tweak: **use skills as much as possible â€” by calling them directly or within projects.**

Why:

- **A skill is a procedure, not a global instruction.** A rule in `CLAUDE.md` is read on *every* run, including the 90% that have nothing to do with it. The same rule inside a skill costs context only when the task calls for it. Moving a procedure from `CLAUDE.md` into a skill is the cheapest way to slim down the config without losing anything â€” thatâ€™s exactly what `MOVE` and `LOAD-ON-DEMAND` mean in the report.
- **Calling it directly (`/skill-name`) eliminates trigger roulette.** You stop depending on the description matching your phrasing. When several skills compete on the same subject, explicit invocation breaks the tie â€” and removes the need for a routing rule in `CLAUDE.md`.
- **A skill inside the project (`.claude/skills/`) is versioned with the code.** The procedure travels with the repo, goes into the PR, is reviewable, and doesnâ€™t leak into other projects.
- **A skill is the right unit for a fix.** In Borisâ€™s cycle, when the model stumbles, you have three remedies: a better prompt (the instruction was unclear), a **skill** (a repeatable procedure is missing), or an MCP (context is missing that it canâ€™t reach). Choosing the right remedy prevents the reflex to dump another rule into `CLAUDE.md`.
- **A skill is easy to audit and retire.** It has a boundary, name, and scope. You can disable a skill and measure; disabling â€œthat paragraph in the middle of `CLAUDE.md`â€ is much harder.

In practice: keep `CLAUDE.md` only for what is true **always** (identity, guardrails, sources of truth, security). Everything else â€” procedure, format, recipe, integration â€” becomes a skill, invoked directly when you know what you want.

## 7. Recommended cycle

1. **Run** `/audit-ablacao` on the chosen scope.
2. **Cut** the Top 10 by impact ê· risk (in a separate session â€” the skill doesnâ€™t apply them).
3. **Use it on real work** for a few days. Not on a hypothetical test.
4. **Record failures** and bring back an instruction only when the same failure repeats â€” in the shortest form possible.
5. **Repeat** every ~6 months or with every major model release.

## 8. Practical guide: publishing to Git and the portal

The process followed by this repository itself. It applies to any ARCKHÉ skill/project.

### 8.1 Repo structure

```
audit-ablacaocc/
â”œâ”€â”€ SKILL.md        # the skill (root = installable with one cp)
â”œâ”€â”€ README.md       # this file
â”œâ”€â”€ capa/capa.png   # 1280x720 cover (capa-ARCKHÉ skill)
â”œâ”€â”€ guia/index.html # self-contained landing page + guide (projetos-landing-guia skill)
â”œâ”€â”€ doc/            # source material
â””â”€â”€ .github/workflows/pages.yml   # GitHub Pages deployment via Actions
```

A rule that prevents rework: **the guide goes in `guia/`, never in the root** â€” the root is for code/skills. And itâ€™s **one page inside the repo itself**, never a separate repository. Remote repo name = local folder name.

### 8.2 Git â€” from scratch to push

```bash
cd ~/projetos/audit-ablacaocc

git init -b main
git config user.name  "linnoff88-dev"
git config user.email "linnoff88-dev@gmail.com"   # author matches the destination account
git add -A
git commit -m "feat: skill audit-ablacao + guia"

gh repo create linnoff88-dev/audit-ablacaocc --public --source=. --remote=origin --push
```

Time-saving check: confirm `git config user.email` **before** committing. An author that doesnâ€™t match the destination account is the #1 reason deployments get blocked.

If the push fails because of `.github/workflows/*.yml` (missing `workflow` scope on the HTTPS token), push that commit over SSH:

```bash
git push git@github.com:linnoff88-dev/audit-ablacaocc.git main
```

### 8.3 GitHub Pages

Publish **via GitHub Actions**, not the legacy build (â€œdeploy from a branchâ€): the legacy queue gets stuck, with builds stuck at `building` for hours and deployment returning `Deployment failed, try again later`.

Create `.github/workflows/pages.yml` serving from the root (the guide remains at `/guia/`):

```yaml
name: Deploy static site to Pages
on:
  push: { branches: [main, master] }
  workflow_dispatch:
permissions: { contents: read, pages: write, id-token: write }
concurrency: { group: pages, cancel-in-progress: false }
jobs:
  deploy:
    environment: { name: github-pages, url: "${{ steps.deployment.outputs.page_url }}" }
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with: { path: '.' }
      - id: deployment
        uses: actions/deploy-pages@v4
```

Then point Pages to Actions and watch the run:

```bash
gh api -X POST repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow \
  || gh api -X PUT repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow
gh run watch <run-id> --repo linnoff88-dev/audit-ablacaocc --exit-status
```

Confirm before saying itâ€™s published:

```bash
curl -sI https://linnoff88-dev.github.io/ | head -1   # expects 200
```

The first run takes ~1 min. Since the guide is in `guia/`, the public URL ends in `/guia/`.

### 8.4 Portal (ARCKHÉ AI)

With the Pages URL responding, publish to the 3 ARCKHÉ surfaces (portal, ARCKHÉbuscas, and PRO catalog):

```
/atualiza-portal https://linnoff88-dev.github.io/
```

The skill handles the card, cover, and commit + push to all three repositories.

### 8.5 Definition of â€œpublishedâ€

Publishing = **commit + push to `origin`**. Deployment is automatic via git webhook â†’ Vercel (and GitHub Pages, when applicable) and isnâ€™t under our control: donâ€™t open the dashboard, check the status, or trigger another deployment. Itâ€™s done when the push reaches `origin` and, for Pages, when the file responds.

---

## Credits

Method and perspective: Boris Cherny (Anthropic), talk at Y Combinator about cutting 80% of Claude Codeâ€™s system prompt. Summaries and source material in [`doc/`](doc/). Packaged as a skill by ARCKHÉ.
