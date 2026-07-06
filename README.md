# coding-rules — production rules for agent-written code

**Status: DRAFT** — extracted from a working ruleset used to drive coding
agents on real infrastructure projects. Published as-is.

One language-neutral **master** of obligations (`CR-x.y` numbered) plus
per-language **bindings** that supply native mechanism and the `[auto]`
lint/compiler promotions:

| File | Covers |
|------|--------|
| `coding-rules-master.txt` | Architecture/layering, types & data discipline, TDD (the Iron Law), IO & resilience, API/service layer, data access, security, observability, versioning, CLI contract, **generation density (anti-slop)** — the enforcement model, scope ladder, and §0 minimum set. |
| `binding-typescript.txt` | tsconfig/eslint promotions, Result discipline, Bun+Elysia stack section. |
| `binding-go.txt` | golangci-lint promotions, errors-as-values, sealed-interface variants, recorded floor deviations. |
| `binding-python.txt` | mypy --strict + ruff promotions, typed-exception taxonomy, frozen dataclasses. |
| `binding-php.txt` | PHPStan level-max + strict-rules target, backed enums + default-less match, legacy-adoption ratchet, Yii2 + Laravel stack sections. |

## The structure (the part worth stealing even if you reject every rule)

- **Master states WHAT is owed; a binding states HOW, in native idiom.**
  An agent loads the master plus exactly one binding. Conflicts are binding
  bugs. Languages without a binding run master-only (`[auto]` reads as
  `[review]`).
- **Every rule carries a tag** — `[auto: <named mechanism>]` (a red linter
  is the violation), `[review]` (human judgment), `[advisory]` (skippable).
  An `[auto]` naming no mechanism is invalid by definition.
- **`proof:` lines make narrated compliance count as non-compliance** —
  TDD requires the captured failing-run output, not the claim of one.
- **The projection check keeps bindings honest mechanically**: every CR
  token in the master must appear in every binding (bound or explicitly
  `not_applicable` with a reason), verified by a two-line grep+diff.
- **Exemplar slots (CR-13.4)**: density is judgment, so each binding anchors
  it on a named reference file from *your* codebase rather than a linter.

## Quick start with a coding agent (2 minutes)

**Claude Code:** copy `skills/claude-code/SKILL.md` to
`.claude/skills/coding-rules/SKILL.md` in your project and vendor the .txt
files beside it. The agent then loads the master + the right binding per
task, on demand.

**Codex / any AGENTS.md agent:** paste the block from
`skills/codex/AGENTS.md` into your repo's `AGENTS.md`.

Either way: pin the Iron Law of TDD, the secrets rule, and idempotency in
the agent's always-on instructions so they hold even when the ruleset is
not loaded; fill the CR-13.4 exemplar slot in your binding with a reference
file from YOUR codebase; run the projection check on every master edit.

## Use cases

1. **Drive a coding agent on real production work.** This is the origin
   use: the ruleset's home environment builds infrastructure tooling with
   agents writing 100% of the code under these rules — TDD evidence
   captured, exit-code contracts designed, atomic writes by default. The
   difference is measurable: a pre-ruleset tool audited against a controls
   corpus showed exactly the two defect classes (exit-code overload,
   in-place state writes) that a post-ruleset tool had already gotten right.
2. **PR review checklist.** The tag system makes review triage mechanical:
   if lint/typecheck is green, every `[auto]` rule is satisfied — spend
   human attention only on `[review]` rules. §0 (the minimum set) is a
   ten-line review card for small changes.
3. **Adopt on a legacy codebase.** The PHP binding ships the pattern: a
   committed analyzer baseline that only shrinks (the ratchet) — new and
   touched code meets the full standard, old code converges over time.
4. **Anti-slop review for agent output (§13).** Generation density rules
   name what makes LLM code rot a codebase while passing every lint:
   single-implementation interfaces, one-caller helpers, guards against
   type-proven impossibilities, speculative config. Review agent diffs
   against §13 explicitly.
5. **Fork it as your house style.** CC BY: take the master, keep the
   structure (tags, proof lines, projection check), swap rules you disagree
   with, add bindings for your stack. The structure is the transferable
   part; the projection check keeps YOUR fork honest mechanically.

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — see LICENSE.
