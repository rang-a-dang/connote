# 📝 Connote

Connote helps you collaboratively edit a Markdown document using AI.

## A quick look

To use Connote, you mark up your document with specially-formatted editing requests.
For example:

```markdown
co/d Keep the tone plain and factual.

# Chickens
co/p Make this paragraph more engaging.
Chickens are domesticated birds descended from junglefowl.

co/s/expand
A mother hen begins communicating with her chicks before they hatch.
```

Every line starting with `co/` is an editing request that applies to the text after it.
`co/d` covers the whole document, `co/p` the next paragraph, and `co/s` the next sentence.
`expand` is a built-in command that requests elaboration.

Once you've marked up your document, use the `/connote` skill to have AI make your requested edits:

```
/connote
```

## Connote Directive Language (CDL)

A **directive** is a single editing request written in Connote Directive Language (CDL).
This section is the full reference for writing them.

### General

Directives start with `co/` and look like this:

```
# Command directive
co/scope/command

# Command directive with instructions
co/scope/command instructions

# Command directive with multiline instructions
co/scope/command {
<instructions>
}

# Directive with instructions
co/scope instructions

# Directive with multiline instructions
co/scope {
<instructions>
}
```

### Scope

The scope defines the range of the document covered by the directive.
A directive's scope begins immediately after the directive.

| Scope | Syntax | Ends at... |
|---|---|---|
| Sentence | `s` | ...the end of the sentence, as judged by AI. |
| Paragraph | `p` | ...the next blank line. |
| Section | `h1`–`h6` | ...the next heading of equal or higher level, or the end of the document. |
| Document | `d` | ...the end of the document. |

Place a heading-scoped directive *after* the heading it governs, not before:

```markdown
## Breeds
co/h2 Keep this section concise.

Hundreds of chicken breeds exist, from tiny bantams to birds the size of a small dog.
```

### Commands

Commands are predefined types of edits.
A directive invokes one by name after its scope:

```markdown
co/s/expand
Chickens can fly short distances.
```

might result in

```markdown
Chickens can fly, but only in clumsy, short-lived bursts that carry them just far enough to clear a fence or reach a low roost.
```

Each command is documented in [Commands](docs/Commands.md).

### Instructions

Instructions are free-form guidance.
You can use them *with* commands or *instead of* commands.

```markdown
co/p start every word with c
Chickens are great at driving.
```

might result in

```markdown
Chickens constantly cruise cars competently.
```

Connote will remove directives that have neither a command nor instructions.

### Persistence

Whether a directive stays in your document depends on where you put it.

An inline directive is consumed: the AI removes it once it has acted on it.

A directive inside an HTML comment persists, and re-applies every time you run Connote.
This is useful for standing rules, such as a tone or style guideline:

```markdown
<!-- co/d Avoid superlatives and exclamation marks. -->
```

| Location | Lifetime |
|---|---|
| Inline | Consumed once it's applied |
| HTML comment | Persists and re-applies on every run |

### Conflicts

When two directives cover the same text, such as a document-wide rule and a paragraph-level request, the narrower scope takes precedence.
