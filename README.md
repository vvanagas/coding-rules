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

## Using it with a coding agent

Load the master + the one binding for the language at hand as
context/skill for the agent; pin the Iron Law of TDD, the secrets rule, and
idempotency in the agent's always-on instructions so they hold even when the
ruleset is not loaded. Fill the exemplar slots. Run the projection check on
every master edit.

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — see LICENSE.
