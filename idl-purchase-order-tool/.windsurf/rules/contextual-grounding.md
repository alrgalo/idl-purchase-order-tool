---
trigger: always_on
---

To make `contextual-grounding.md` useful and developer-facing inside Windsurf’s internal documentation (e.g., `/docs/system/contextual-grounding.md`), it should explain **why** this rule exists, **when** it applies, and **how** to implement or extend it.

Here’s a solid draft you can use or adapt:

---

# Contextual Grounding

> All AI-driven prompts in Windsurf must be grounded in relevant client and system documentation.

---

## Purpose

This rule ensures that Windsurf responses always reflect the **latest business context, domain language, and architectural assumptions** of the project. By enforcing contextual grounding, we reduce hallucination, improve accuracy, and align generated output with real client needs.

---

## Scope

Applies globally to:

* All prompt types (e.g., code review, UI inspection, text generation)
* All user roles (e.g., Developer, Designer, QA, Compliance)
* All modules of Windsurf

---

## Sources to Always Include

Before executing any prompt, Windsurf must automatically load and reason over the following sources:

1. **Client Docs**

   * Located in `/client-docs/`, `/inputs/`, or any configured `projectContextPaths`
   * Includes: feature specs, audit requirements, signed-off wireframes, etc.

2. **System Docs Folder**

   * Located in `/docs/`
   * Includes: architecture, style guides, design rules, workflows, and standards

These are treated as **required context**, not optional suggestions.

---

## Expected Behavior

* Prompts sent to the LLM must **inject key context** from both client docs and system documentation.
* If context is missing or unclear, Windsurf should respond with a clarification prompt (e.g., *“Missing design rule for spacing. Please check `/docs/design-guide.md`.”*)
* Downstream tasks (like form generation or UX reviews) should reference this context to explain design decisions or code suggestions.

---

## Implementation Notes

* 🔍 A preprocessing step (`applyContextualGrounding()`) runs before all prompt execution.
* ✅ Deduplicates and ranks documents based on relevance using embeddings.
* 🧠 Uses semantic search to inject only the **top N** most relevant context snippets (configurable).

---

## Related Rules

* `design-consistency`
* `form-validation-standards`
* `prompt-sanitization`
* `workflow-handoff-safety`

---

## Change Log

* **v1.0** – Initial release (July 2025)