---
name: coding-rules
description: Mandatory production coding rules — architecture/layering, type & data discipline, TDD, IO & resilience, API/service layer, data access, security, observability, versioning, CLI contract, generation density (anti-slop). Use when writing, reviewing, refactoring, or planning any production code (services, libraries, handlers, data access, CLIs). Skip for one-off scripts and throwaway prototypes.
---

# Coding Rules — router

This skill is a loader; the rules live beside this file (vendor the .txt
files next to this SKILL.md, or adjust paths). Do BOTH steps now:

1. Read `coding-rules-master.txt` (always) — language-neutral obligations,
   `CR-x.y` numbered, including the enforcement model, scope ladder, §0
   minimum set, and §13 generation density.
2. Read exactly ONE binding matching the language of the code at hand:
   - TypeScript → `binding-typescript.txt`
   - Go         → `binding-go.txt`
   - Python     → `binding-python.txt`
   - PHP        → `binding-php.txt`
   The binding supplies native mechanism and the `[auto]` toolchain
   promotions; the master wins on obligation, the binding on mechanism.
   A change spanning two languages loads both bindings.

No binding for the language (shell, SQL-only, …): master-only mode — no
`[auto]` promotions exist; every rule reads at its master tag and you
enforce the stated principle by judgment.

Non-negotiables even when this skill is not loaded (worth pinning in your
always-on instructions): the Iron Law of TDD (CR-3.1–3.4 — no production
code without a captured failing test first; code written before its test is
deleted), no horizontal slicing (CR-3.6), the single secrets rule (CR-8.5),
and idempotency (CR-4.4–4.5).

Before your first session: fill the CR-13.4 exemplar slot in your binding
with a reference file from YOUR codebase — density review anchors on it.
