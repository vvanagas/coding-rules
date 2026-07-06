# coding-rules — production rules for agent-written code

LLM-written code has failure modes that human code review was never
designed to catch — it looks clean, passes lint, and rots the codebase
anyway. This ruleset exists to kill those modes by name:

| Agent-written code tends to… | The counter-rule, in plain words |
|------------------------------|----------------------------------|
| say "all tests pass!" without having run them | Claims don't count; only captured output does. A behavior change needs the failing run and the passing run, pasted. No captured RED — no compliance, whatever the narration says. *(proof lines)* |
| write the code first, then write tests that agree with it | Code written before its failing test gets **deleted** — not adapted, not kept "for reference". Start over, test-first. *(the Iron Law)* |
| produce an interface with one implementation, a helper with one caller, a module that only forwards | Abstraction has to earn existence: inline is the default, extract on the *second* caller or a nameable domain concept — not before. *(§13 density)* |
| null-check a value the type system already proves non-null | A redundant guard isn't safety — it hides the real invariant and teaches readers to distrust the types. *(§13)* |
| add config for values nothing varies, and hooks for futures nobody stated | Carry-cost is permanent; delete-cost is a diff. If no call site varies it, it isn't a parameter. *(§13)* |
| wrap everything in try/catch and type none of it | Pick ONE failure mechanism per language; recoverable failures are typed and visible at the call boundary, bugs crash loudly. Wrapping a bug is as wrong as throwing a recoverable failure. *(failure taxonomy)* |
| retry whatever failed | Retrying a non-idempotent operation is a **correctness bug**, not a style issue. Where retry is legal: full jitter, named attempt limit, named retryable classes. *(idempotency)* |
| grow caches, queues, and buffers without limits | Nothing grows without a ceiling — and a ceiling without a named overflow behavior (block, reject, or drop-with-a-counter) is documentation, not behavior. *(bounds)* |
| cite a lint rule that doesn't exist | Every enforcement claim must name its real mechanism, or it downgrades to human review on the spot. Invented enforcement dies by definition. *(the `[auto]` tag)* |
| write Python-shaped Go and Java-shaped TypeScript | One binding per language, native idiom only: the master says WHAT is owed, the binding says HOW your language actually does it. *(bindings)* |
| reformat forty untouched lines while fixing one | The diff is as small as its slice allows; improvements outside the slice go to a ledger, not into the diff. *(minimal diff)* |
| exit `1` for everything | Exit codes are a contract — "it's broken" and "couldn't check" never share a code — and stdout carries data only; logs go to stderr. *(CLI contract)* |

**Opinionated on purpose.** The opinions are the value: TDD is
non-negotiable with no advisory escape; each language binding picks ONE
error mechanism and defends it (Result in TypeScript, typed exceptions in
Python — chosen per ecosystem, never blended); density is judged against a
named exemplar file from *your* codebase, not the model's taste. Where a
language can't enforce a rule, the binding records the weaker floor
honestly instead of pretending ("floor deviations") — disagreement with
reality is treated as a bug in the ruleset, not in reality.

**Status: DRAFT** — extracted from a working ruleset used to drive coding
agents on real infrastructure projects, where agents write 100% of the
code under these rules. Published as-is.

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
