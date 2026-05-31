---
name: rewrite-prompt
description: Automatically rewrites a user's short or vague prompt into a more descriptive, clear, and actionable prompt — without asking any clarifying questions. Use this skill whenever a user asks to "improve my prompt", "rewrite this prompt", "make this prompt better", "fix my prompt", or pastes a short/unclear prompt and wants it enhanced. Also trigger when a user says things like "this prompt isn't working" or "help me get better results from AI". Always execute immediately — infer all missing context from the prompt itself and produce both a short and detailed rewritten version right away.
---

# Rewrite Prompt Skill

## Core behavior

**Never ask clarifying questions.** Always auto-infer and execute immediately.

When this skill triggers:

1. Silently analyze the input prompt using the inference rules below.
2. Produce a **Short version** (1–2 sentences) and a **Detailed version** (3–6 sentences).
3. Optionally append one brief inference note (1 line) explaining any assumption that meaningfully shaped the rewrite.

---

## Auto-inference rules

Derive all missing context directly from the prompt. Apply these rules in order:

| Missing field       | How to infer it                                                                                                                      |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Audience / role** | Match to the domain: code → developer; UI/copy → designer or marketer; data → analyst; general → generic AI assistant                |
| **Goal**            | Extract the core verb + object (e.g. "write", "summarize", "build") and make it explicit                                             |
| **Constraints**     | Default to: output in structured format (bullets or numbered list if multi-part), concise length, no jargon unless domain demands it |
| **Tone**            | Default to professional and direct; shift to casual if the input is casual, technical if code-heavy                                  |
| **Format**          | Infer from task type: spec → headings + bullets; code → function signature + examples + tests; explanation → paragraphs              |
| **Examples**        | Omit unless the original prompt mentions specific inputs/outputs; if it does, include a placeholder example                          |

---

## Rewrite template

Use this structure for both versions:

- **Role**: Who is performing the task (e.g., "You are a Python developer…")
- **Goal**: One clear sentence on what to produce
- **Constraints**: What format, length, style, or limits apply
- **Output spec**: What the final output should look like
- **Tone/Style**: One short note if non-obvious
  The **Short version** collapses all of the above into 1–2 dense sentences.  
  The **Detailed version** expands each element into 3–6 sentences with explicit constraints and output specs.

---

## Output format

Always respond in this exact structure:

```
**Short version**
<1–2 sentence rewritten prompt>

**Detailed version**
<3–6 sentence rewritten prompt>

**Inference note** *(only if a non-obvious assumption was made)*
<1 line explaining the key assumption, e.g. "Assumed JavaScript since no language was specified.">
```

---

## Examples

### Input: "Help me write a spec for a search feature."

**Short version**  
You are a product manager. Write a concise feature spec for a search function supporting fuzzy matching, filters, and sorting — include user stories, UI layout notes, and acceptance criteria in bullet points.

**Detailed version**  
You are a product manager writing a feature specification for a search function on a web application. Define requirements for fuzzy matching, multi-faceted filters, and result sorting. Include user stories (as "As a user…" statements), a brief UI layout description, API contract notes, and acceptance criteria as a checklist. Use headings and bullet lists throughout. Keep it actionable and unambiguous.

---

### Input: "Make a function that formats dates."

**Short version**  
You are a JavaScript developer. Implement `formatDate(date, format)` supporting `YYYY-MM-DD`, `MM/DD/YYYY`, and `DD MMM YYYY`; return `null` for invalid inputs; include usage examples.

**Detailed version**  
You are a JavaScript developer. Implement a pure function `formatDate(date, format)` that accepts a Date object or ISO string and a format token string, and returns the formatted date. Support at minimum: `YYYY-MM-DD`, `MM/DD/YYYY`, and `DD MMM YYYY`. Return `null` for invalid or unparseable inputs rather than throwing. Include three usage examples inline as comments, and a brief note on how to extend it with additional format tokens.

**Inference note**  
Assumed JavaScript; no language was specified in the original prompt.

---

## Non-negotiable rules

- **No clarifying questions.** Ever. Infer and execute.
- **Always output both versions** (short + detailed) unless the user explicitly asks for only one.
- **Keep rewrites model-agnostic** unless the user specifies a target model.
- **Mirror user-provided examples** if any are included in the original prompt.
- **One inference note maximum** — only include it if the assumption is non-trivial (e.g., assumed language, framework, or audience).
