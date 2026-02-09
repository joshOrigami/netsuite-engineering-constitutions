# Contributing

Thank you for your interest in contributing to this repository.

This project contains **specifications and constitutions**, not executable code.
Contributions are welcome, but the bar for changes is intentionally high:
these documents define **non-negotiable invariants** used by real teams.

Please read this guide before opening an issue or pull request.

---

## What kinds of contributions are welcome?

We welcome contributions that improve:

- clarity and precision of language
- correctness of NetSuite-specific constraints
- coverage of real-world edge cases
- structure and readability
- consistency across documents
- examples that clarify intent (without adding implementation detail)

We are especially interested in:
- gaps discovered through real-world use
- NetSuite features or behaviors not yet covered
- ambiguous language that could be misinterpreted by humans or AI

---

## What is *not* a good fit?

These documents are **constitutions**, not best-practice lists.

We generally do not accept:
- stylistic rewrites without a clear rationale
- “optional” or “nice to have” guidance
- implementation-specific instructions
- vendor-specific tooling recommendations
- changes that weaken or soften invariants
- speculative content not grounded in NetSuite behavior

When in doubt, open an issue first.

---

## How to report issues or gaps

If you believe something is missing, unclear, or incorrect:

1. Open a GitHub Issue
2. Clearly describe:
   - the section and document affected
   - the real-world scenario that exposed the issue
   - why the current text is insufficient or misleading
3. If possible, propose wording or a direction for improvement

Issues that include concrete context are much easier to evaluate.

---

## How to suggest enhancements

Enhancements should strengthen the documents as **SDD artifacts**.

Good enhancement proposals:
- identify an invariant that should be explicit
- clarify an existing rule that could be misread
- tighten language to prevent unsafe assumptions
- add a missing NetSuite boundary or constraint

Please explain *why* the change matters, not just *what* you want to change.

---

## Pull request workflow

If you’d like to propose a change directly:

1. Fork the repository
2. Create a feature branch
3. Make focused, minimal edits
4. Open a pull request with:
   - a clear description of the change
   - the motivation behind it
   - any real-world context that informed it

Large or conceptual changes should reference a related issue.

---

## Review philosophy

Pull requests are reviewed with these questions in mind:

- Does this make assumptions more explicit?
- Does it reduce the risk of misinterpretation?
- Does it strengthen invariants rather than dilute them?
- Would this help both humans *and* AI agents behave more safely?

Not all well-intentioned changes will be accepted — that’s by design.

---

## Attribution

By contributing, you agree that your contributions may be:
- edited for clarity
- incorporated into the documents
- redistributed under the repository’s license

Attribution is preserved via Git history.

---

## Questions?

If you’re unsure whether something belongs here, open an issue and ask.
Thoughtful discussion is always welcome.
