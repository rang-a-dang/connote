---
name: connote
description: Apply Connote (CDL) editing directives to a Markdown document.
disable-model-invocation: true
---

# Connote

Connote rewrites parts of a Markdown document according to **directives** the user has embedded in it.
A directive is a single editing request: it starts with `co/` and governs the text immediately after it.

```markdown
# Chickens
co/p Make this paragraph more engaging.
Chickens are domesticated birds descended from junglefowl.
```

Here `co/p` requests a rewrite of the paragraph that follows it.

A directive has the shape `co/<scope>[/<command>] [instructions]`:
- **scope** — how much text it governs: `s` sentence, `p` paragraph, `h1`–`h6` section, `d` document (exact ranges in step 3).
- **command** *(optional)* — a named, predefined kind of edit, defined in `references/<command>.md` and to be loaded only when a directive uses it.
- **instructions** *(optional)* — free-form guidance, written on the same line or in a trailing `{ ... }` multiline block.

A directive embedded in Markdown content is called a **content directive**.
Content directives are to be followed and removed once applied.
A directive embedded in an HTML comment (`<!-- co/d ... -->`) is called a **comment directive**.
Comment directives apply on every run. Because of this, you should ignore any command they name, i.e., treat comment directives as if they had instructions only.

## Procedure

### 1. Find the target document(s)

- In the IDE, use the active Markdown file the editor provides.
- From the CLI, use a file-path argument if given; otherwise the IDE's active file; otherwise ask the user which file to edit.
- If several files are named, run the whole procedure on each independently.

### 2. Find the directives

Scan the document for content and comment directives starting with `co/`.
Ignore any `co/` found inside a fenced code block.

### 3. Resolve each directive's range

A directive's range begins immediately after it and ends per its scope:

- `s` — the end of the sentence (you judge the boundary).
- `p` — the next blank line.
- `h1`–`h6` — the next heading of equal or higher level, or the end of the document.
- `d` — the end of the document.

### 4. Resolve conflicts

When two different directives are in scope for the same text, they should generally both be applied.
In cases where directives conflict, the **narrower scope wins**.

### 5. Edit the governed text

For each content directive, rewrite its governed text using its command and instructions.
If the directive names a command, read that command's definition from `references/<command>.md`.
If there are multiple directives in scope, apply them using your best judgment.

- Directives lacking both a command and instructions should be removed.
- If a named command has no `references/<command>.md` file, warn the user and skip that directive rather than guessing.

### 6. Apply edits and clean up

- Apply each content directive as one localized edit: replace its governed text and remove the directive line together.
- Leave comment directives in place so they re-apply on the next run.

### 7. Report

Summarize each edit you made: its scope, its command or instructions, and what changed. If there were no actionable directives, say so.

## Edge cases

Warn on:
- Heading-scoped directives placed immediately before their heading.
- Named commands with no `references/` file.
