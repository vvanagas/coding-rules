# coding-rules — AGENTS.md block (Codex / any AGENTS.md-convention agent)

Paste the section below into your repository's `AGENTS.md`, and vendor the
rule files (adjust paths if you place them elsewhere).

---

## Production code rules (coding-rules)

For ANY production code change in this repository (services, libraries,
handlers, data access, CLIs — not throwaway scripts):

1. Read `docs/coding-rules/coding-rules-master.txt` plus exactly ONE
   language binding (`binding-typescript.txt` / `binding-go.txt` /
   `binding-python.txt` / `binding-php.txt`). Master states WHAT is owed;
   the binding states HOW in native idiom and names the `[auto]` lint
   promotions. For a language with no binding, apply the master with every
   rule read as `[review]`.

2. THE IRON LAW OF TDD (no exceptions, no advisory escape):
   - No production code without a failing test first.
   - Watch the test fail, for the RIGHT reason; capture the failing output.
   - Code written before its failing test is DELETED, not adapted.
   - Vertical slices only: one behavior per RED→GREEN slice.

3. `proof:` lines mean evidence, not narration: green linter/typecheck
   output plus the captured failing-then-passing test output for any
   behavior change. Narrated compliance counts as non-compliance.

4. Always-on invariants: no secret in code, logs, output, or history
   (CR-8.5); retrying a non-idempotent operation is a correctness bug
   (CR-4.4); every accumulator has a bound AND a named overflow behavior
   (CR-6.6); file writes are atomic — temp + fsync + rename (CR-7.7);
   CLI exit codes are a documented contract and stdout carries data only
   (CR-12.1/12.2).

5. Generation density (§13): no interface with one implementation outside
   declared ports, no one-caller helpers that name nothing, no speculative
   configurability, no guards against type-proven impossibilities, diffs as
   small as their slice allows.

6. On any master edit, run the projection check (two-line grep+diff at the
   top of the master) — every CR token must appear in every binding.
