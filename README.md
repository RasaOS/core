# RasaOS · Core

**Canonical name:** `rasa.core`
**Repo / folder:** `core`
**Kind:** `core` (canon Spec §6 — singleton)
**Contract:** Element Contract v1.3.0
**Version:** 0.1.0 (Phase 1 shell — content extraction is Phase 2)
**Status:** First commit. Canon-shaped; content/ empty scaffolds. Phase 2 (separate session) extracts universal content from `rasa.domain.code`.

## What this Element is

The singleton L1 shared foundation every domain + orchestrator
Element depends on. Per canon Spec §6:

> "Exactly one: `core` (dotted name `rasa.core`). The shared bones
> every domain and orchestrator depends on. Vocabulary, output
> styles, stamp definitions, common rules. First-party-only."

`rasa.core` is one of two canon **singletons** (the other is
`rasa.kernel`). The name has no `.identifier` suffix — per Spec §3
the name regex has an exception: "OR be exactly `rasa.core` /
`rasa.kernel`."

## What this is NOT

- **Not `rasa.domain.core`** — that's the template every `domain`-kind
  Element forks from. Different kind, different role. Sister-Element
  names are coincidentally similar; the concerns are different.
- **Not a runtime executable** — `rasa.core` ships content
  (vocabulary, output styles, stamp definitions, common rules) that
  other Elements install into themselves. It's L1, not L2/L4.

## v0.1.0 (Phase 1) — canon-shaped shell

Phase 1 establishes the canon-required-files (§4) skeleton so the
Element exists, is discoverable, and validates against
`bin/check-manifest`. Content extraction is **Phase 2** (separate
session — see below).

What v0.1.0 ships:

```
core/
├── rasa.json                  # kind=core, contract_version=1.3.0, singleton name
├── VERSION                    # 0.1.0
├── README.md                  # this file
├── CLAUDE.md                  # per-repo session contract
├── CHANGELOG.md               # v0.1.0 entry
├── LICENSE                    # Apache-2.0 (matches domain-core lineage)
├── .gitignore
├── bin/
│   ├── init                   # canonical installer (from domain-core v1.0)
│   └── check-manifest         # pure-python validator (from domain-core v1.0)
├── content/                   # ALL EMPTY in v0.1.0 — Phase 2 populates
│   ├── skills/.gitkeep
│   ├── rules/.gitkeep
│   └── agents/.gitkeep
└── seed/
    ├── CLAUDE.md.template
    └── rasa.lock.json.template
```

## Phase 2 — content extraction (separate session, not started)

The actual value of `rasa.core` comes from extracting universal
content from `rasa.domain.code` (which has ~80 skills and ~27 root
rule files — a mix of engineering-specific and genuinely universal)
into this Element, then updating `rasa.domain.code` + `rasa.domain.legal`
+ future domain/orchestrator Elements to `requires.elements: [rasa.core]`.

**Phase 2 extraction candidates** (to evaluate when the work starts):

From `rasa.domain.code/content/`:
- **`vocabulary.md`** → universal (kit term definitions; should be canon-cited not duplicated)
- **`output-rules.md` + `output-styles.md`** → universal (34 output templates)
- **`stamps.md`** → universal (YAML frontmatter schemas)
- **`task-rules.md`** → mostly universal (engineering-flavored examples; could generalize)
- **`craft-rules.md`** → universal (build-it-right discipline)
- **`environment-rules.md`** → universal (env-var stamp model)
- **Skills folder candidates:** `audit/`, `save/`, `handoff/`, `bookmarks/`, `glossary/`, `peer-review/`, `stuck/` — review case-by-case
- **Agents:** `code-reviewer/`, `doc-scanner/` may be too engineering-specific — defer

From `rasa.domain.code/seed/`:
- Most templates (CLAUDE.md, AUDIT.md, PHASES.md, ROADMAP.md, RELEASES.md, MIGRATIONS.md, ENV.md, TESTS.md, environments.json, pact, bookmarks, playlists, runtime templates, test templates) are mostly universal — could become `seed-with-substitution` candidates if that policy lands.

**NOT extraction candidates** (stay in domain-code):
- iOS-specific: `ios-task-rules.md`, `ios-conventions.md`, `skills/ios-release/`
- Web-specific: `web-task-rules.md`, `web-conventions.md`
- Build pipeline (`content/build/`, `content/tests/`) — engineering-specific scaffolding
- Pipeline + migration + git-flow rules — engineering-specific
- ~70 of the 80 skill folders — engineering-specific

The extraction work needs careful per-file judgment. Better as a
dedicated session.

## How forks consume this

When Phase 2 lands, downstream Elements will:

```json
{
  "name": "rasa.domain.legal",
  "kind": "domain",
  "requires": {
    "elements": [
      { "name": "rasa.core", "version": ">=0.2.0" }
    ]
  }
}
```

Their `bin/init` (or the kernel ElementManager at runtime) resolves
the dependency, pulls `rasa.core`'s content alongside their own.

## See also

- Canon Spec §6 — the `core` kind definition (singleton)
- Canon Spec §3 — name regex singleton exception
- Canon Spec Part X §50 (SA-015) — multi-instance Element model (core is EXCLUDED from multi-instance per "first-party-only + exactly one")
- `~/rAI/rasa-os/elements/domain-core/` — the template this Element used as structural baseline (note: `rasa.domain.core` is a sister, NOT a parent — different kinds)
- `~/rAI/rasa-os/elements/domain-code/` — source of Phase 2 extraction candidates
- `~/rAI/rasa-os/canon/AUDIT.md` 2026-05-24 — this Element's authoring

## License

Apache 2.0. Same convention as `rasa.domain.core` v1.0+, `rasa.domain.code`, `rasa.domain.legal`, claude-kit lineage.
