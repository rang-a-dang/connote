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
| Sentence | `s` | ...the end of the sentence. |
| Paragraph | `p` | ...the next blank line. |
| Section | `h1`–`h6` | ...the next heading of equal or higher level. |
| Document | `d` | ...the end of the document. |

### Commands

Commands are predefined types of edits.
Here's an example of the `expand` command applied to the next sentence.

```markdown
co/s/expand
Explain how great chickens are.
```

might result in

```markdown
Chickens are the unsung comedians of the animal kingdom, blessed with a strut that radiates completely unearned confidence.
```

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

Whether a directive remains in your Markdown document depends on where it's located.
A directive in an HTML comment remains in the document.
An inline directive will be removed once it has been processed.

| Location | Removed when processed? |
|---|---|
| HTML comment | No |
| Inline | Yes |
