# CHANGELOG — `rasa.core`

Reverse-chronological. Each entry is a version bump.

---

## 0.1.1 — 2026-07-09

### Added generic `/sync` + `/promote` + `/kit`-aware `bin/init` (canon SA-024)

- `bin/init` now clones the Element source into `<project>/kit/<element>/`; `/sync` smart-pulls upstream, `/promote` smart-pushes local edits back upstream (both directory-mirror → installed into consumers).

## 0.1.0 — 2026-05-24 — Phase 1 shell (canon-shaped, content empty)

Initial commit. Closes the longest-running Tier 1 dependency hole
in the substrate (`rasa.core` had been a README-only stub since
the workspace was scaffolded).

### Origin

User direction during 2026-05-24 workspace-dogfood session, in
response to the macro-level gaps audit: "Author rasa.core Element
(Tier 1 — biggest dependency hole)."

`rasa.core` is one of two canon singletons (the other is
`rasa.kernel`), per Spec §6: "Exactly one — `core` (dotted name
`rasa.core`). The shared bones every domain and orchestrator
depends on. Vocabulary, output styles, stamp definitions, common
rules. First-party-only." Spec §3's name regex has the
singleton exception clause for `rasa.core` / `rasa.kernel`.

### Phase 1 vs Phase 2

This release (v0.1.0) is **Phase 1** — the canon-required-files
shell so the Element exists, is discoverable, and validates against
`bin/check-manifest`. **Content/ subdirs are empty scaffolds** —
they're registered in rasa.json#element.files[] as opt-in for
check-manifest coverage, but ship no actual files yet.

**Phase 2** (separate dedicated session, not started) will:

1. Extract universal content from `rasa.domain.code/content/`
   (vocabulary, output-styles, stamps, craft-rules,
   environment-rules, and audit/save/handoff/glossary skills — case
   by case) into this Element's content/.
   **NOTE (2026-06-18):** `task-rules` was REMOVED from this list — task
   management shipped instead as the opt-in Element `rasa.module.tasks`
   (first `module`-kind Element). It is not core content: core is
   mandatory-for-all; task management is opt-in per parent via
   `requires.elements[]`. See `elements/module-tasks/`.
2. Bump rasa.json#element.files[] policies from `opt-in` to
   `directory-mirror` for populated subdirs
3. Bump rasa.core → v0.2.0 (minor — new content categories)
4. Update `rasa.domain.code` to declare `requires.elements: [{name: "rasa.core", version: ">=0.2.0"}]` and remove the extracted files from domain-code's own manifest (domain-code bumps minor)
5. Same for `rasa.domain.legal`
6. Same for any future Element that wants to inherit the universal content

Phase 2 needs careful per-file judgment ("is this genuinely universal
or engineering-specific?") + careful coordination across multiple
Elements + dependency declaration semantics. Justifies a fresh
focused session.

### Required files (canon §4)

All present:

- `rasa.json` — kind=core, contract_version=1.3.0, singleton name `rasa.core` (no `.identifier` suffix), `rasa.singleton: true`, `rasa.role: "L1-shared-foundation-singleton"`, `rasa.conforms_to: "rasa.domain.core@v1.0.0"` (declares the template used as structural baseline)
- `VERSION` (0.1.0)
- `README.md` — full description + Phase 2 extraction plan
- `CLAUDE.md` — per-repo session contract; Phase 2 plan; don'ts
- `CHANGELOG.md` — this file
- `LICENSE` — Apache-2.0 (matches domain-core + domain-code + domain-legal lineage)
- `.gitignore` — macOS + editor cruft + `.claude/rasa.lock.json`

### Reused from `rasa.domain.core v1.0.0`

- `bin/init` (canonical installer, ~130 lines pure-python)
- `bin/check-manifest` (cross-platform pure-python validator)
- `seed/CLAUDE.md.template` (placeholder-free per-project contract)
- `seed/rasa.lock.json.template` (substitution-placeholder lockfile)
- `LICENSE` (Apache-2.0 full text)
- `.gitignore`

`rasa.core` structurally resembles a domain Element (per Spec §6
kind-specific shape variation: "core: same shape as
domain/orchestrator, no `requires.elements` — it depends on
nothing"). `rasa.domain.core` v1.0 was the template; this Element's
`rasa.json#rasa.conforms_to: "rasa.domain.core@v1.0.0"` declares the
inheritance. Future divergences (if core ever needs different
structure than a domain) will fork that relationship.

### Smoke-tested

- `python3 -c "import json; json.load(open('rasa.json'))"` → valid JSON
- `bin/check-manifest` → expected to pass (0 tracked files in content/+seed/ at first commit; check-manifest's coverage check passes when all element.files[] entries match tracked files)

### Why singleton, why content-empty for v0.1.0

**Singleton:** canon-locked. There's exactly one `rasa.core`. Every
domain + orchestrator declares it as a dependency. Multi-instance
(SA-015) doesn't apply — there is no "kernel-wide vs project-embedded
rasa.core" distinction.

**Content-empty for v0.1.0:** the Element existing matters
independently of the content being populated. Future canon Recipes
will reference `rasa.core` as a dependency; that reference resolves
once this Element is published to `RasaOS/core` with a v0.1.0 tag.
The content extraction is the larger work; doing it under a fresh
dedicated session (Phase 2) avoids cramming a careful judgment
exercise into the substrate-orchestration session that authored
the shell.

### What's deliberately NOT shipped here

- No skills/rules/agents (Phase 2 extraction)
- No `requires.elements[]` — core depends on nothing (canon §6 lock)
- No multi-instance support (singleton)
- No `bin/lint` (domain-code-specific tooling)
- No tasks/ folder (Element-level concern)
- No docs/ folder (Element-level concern)

### Surfaced gap (flagged in canon AUDIT, not blocking)

`bin/new-element` (in orchestrator-workspace v0.3.0) doesn't yet
support `kind=core` — only `kind=domain` and `kind=orchestrator`.
This Element was scaffolded manually (not via `bin/new-element`)
because of that gap. Extending `new-element` to handle `core` +
`frontend` + `recipe` kinds is a future orchestrator-workspace
enhancement (probably v0.3.1 or v0.4.0).
