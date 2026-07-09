# CLAUDE.md — `rasa.core`

> **Who you are (SA-025).** `rasa.core` — the RasaOS L1 shared foundation every Element depends on. Substrate: **RasaOS**; role: **core**. On install `bin/init` renders this into `.claude/rasa-identity.md`; `/whoami` composes the full identity with the project's deployment layer.


Per-repo working contract for Claude sessions opened inside this
folder. Extends `~/.claude/CLAUDE.md` and the workspace
`~/rAI/rasa-os/CLAUDE.md` (the `rasa.tenant.rasaos` tenant contract);
does not override them.

## What you are when you're in this folder

You are working on **`rasa.core`** — the singleton L1 shared
foundation every domain + orchestrator Element depends on. One of
two canon singletons (the other is `rasa.kernel`); has no
`.identifier` suffix in the name.

Two distinct levels:

- **This Element** (this folder) — the source of universal content
  that downstream Elements pull from. Authoring + version bumps +
  CHANGELOG happen here.
- **Downstream Elements** (rasa.domain.code, rasa.domain.legal,
  future orchestrator-firm/clinic, etc.) — declare
  `requires.elements: [rasa.core]` and inherit universal content.
  Their work isn't this Element's concern.

## v0.1.0 → v0.2.0 (Phase 2 plan)

v0.1.0 is the canon-shaped Phase 1 SHELL — content/ scaffolds are
empty. v0.2.0 lands the Phase 2 extraction from
`rasa.domain.code/content/` (vocabulary, output-styles, stamps,
universal rules). v0.2.0 will:

1. Move/copy universal content from domain-code/content/ → core/content/
2. Add elements to rasa.json#element.files[] (change policy from opt-in to directory-mirror)
3. Bump rasa.core version 0.1.0 → 0.2.0 (minor — new content category)
4. Then in domain-code: declare `requires.elements: [rasa.core@>=0.2.0]`, remove the moved files from domain-code's own rasa.json
5. Bump domain-code accordingly + ship
6. Same for domain-legal
7. AUDIT entries documenting the extraction + dependency declaration

Phase 2 is its own session. Get a fresh dedicated context for it —
the per-file judgment ("is this universal or engineering-specific?")
benefits from a clean focus.

## Source of truth

- **`~/rAI/rasa-os/canon/`** — authoritative. Spec §6 defines `core`
  kind as a singleton. Spec §3 name regex has the singleton exception.
- **`~/rAI/rasa-os/CLAUDE.md`** — workspace orientation; role-split
  is locked there.
- **This folder's `README.md`** — full description + Phase 2 plan.
- **This folder's `rasa.json`** — formal declaration with singleton
  metadata (`rasa.singleton: true`).

## Don'ts

- **Don't add a `.identifier` suffix to the name.** It's `rasa.core`,
  not `rasa.core.something`. Singleton per canon. The name regex
  Spec §3 explicitly carves an exception for this.
- **Don't add `requires.elements[]`** to rasa.core itself. Per canon
  §6 kind-specific shape variation: "core: no `requires.elements`
  (it depends on nothing)." `rasa.core` is the dependency floor.
- **Don't ship content/ files without registering them in rasa.json.**
  Per the manifest-driven install discipline (canon ELEMENT_CONTRACT
  §7) + `bin/check-manifest` validation. Every file under content/
  + seed/ MUST be registered.
- **Don't conflate with `rasa.domain.core`.** Different kind (core
  vs domain); different role (singleton dependency vs domain
  template). They're sisters by naming coincidence only.
- **Don't ship engineering-specific content here.** Phase 2 extraction
  is a careful "is this universal or vertical-specific?" judgment.
  When in doubt, leave it in domain-code or domain-legal. The bar
  for entering rasa.core is HIGH (gets pulled into every domain
  Element via dependency).
- **Don't bump major version casually.** v0.x is "establishing the
  Element." v1.0.0 is the lock-down (probably after Phase 2 lands +
  the universal content set stabilizes after one or two refinements).

## How a version bump works

Per orchestrator-core's general orchestrator versioning rules (this
Element follows the same pattern since it's structurally similar):

- **Patch (0.1.x)** — bug fix in shipped content; README clarification;
  bin tool fix. No structural change.
- **Minor (0.1.x → 0.2.0)** — new content extraction batch, new
  capability, new universal rule. Forks may adopt opportunistically;
  bumping their `requires.elements[].version` constraint.
- **Major (0.x.x → 1.0.0)** — first stable lock-down of the universal
  content surface (probably after Phase 2 + first review pass). Forks
  REQUIRED to migrate.

Each bump: edit VERSION, update rasa.json#version, write CHANGELOG
entry. Commit + tag + push. Update `~/rAI/rasa-os/elements/REGISTRY.md`
+ `elements/CHANGELOG.md`.

## What success looks like for this Element

- Phase 2 lands: real universal content shipped (vocabulary, output
  styles, stamps, common rules). domain-code and domain-legal both
  declare `requires.elements: [rasa.core]` and pull the universal
  content automatically.
- Adding a new domain Element (e.g., `rasa.domain.health`) gets the
  universal content for free — no per-vertical re-derivation of
  vocabulary, stamps, output styles.
- The universal content set stabilizes (v0.x → v1.0.0). After v1.0,
  bumps are surgical and non-breaking.
- No engineering-specific or legal-specific content leaks in. The
  Element stays universal.
