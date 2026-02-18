---
title: Markdown Syntax Guide
created: 2026-01-30
tags: [resource, markdown, reference]
type: resource
---

# Markdown Syntax Guide

A quick reference for Markdown syntax used in Obsidian.

## Headers

```markdown
# H1 Header
## H2 Header
### H3 Header
#### H4 Header
##### H5 Header
###### H6 Header
```

## Emphasis

```markdown
*italic* or _italic_
**bold** or __bold__
***bold italic*** or ___bold italic___
~~strikethrough~~
==highlight==
```

*italic* or _italic_
**bold** or __bold__
***bold italic*** or ___bold italic___
~~strikethrough~~
==highlight==

## Lists

### Unordered Lists
```markdown
- Item 1
- Item 2
  - Nested item
  - Another nested
```

- Item 1
- Item 2
  - Nested item
  - Another nested

### Ordered Lists
```markdown
1. First item
2. Second item
3. Third item
```

1. First item
2. Second item
3. Third item

### Task Lists
```markdown
- [ ] Unchecked task
- [x] Checked task
```

- [ ] Unchecked task
- [x] Checked task

## Links

```markdown
[External Link](https://example.com)
[[Internal Link]]
[[Internal Link|Custom Text]]
```

## Images

```markdown
![Alt text](image.png)
![[local-image.png]]
```

## Code

### Inline Code
```markdown
Use `code` for inline code
```

Use `code` for inline code

### Code Blocks
````markdown
```python
def hello_world():
    print("Hello, World!")
```
````

```python
def hello_world():
    print("Hello, World!")
```

## Blockquotes

```markdown
> This is a blockquote
> It can span multiple lines
```

> This is a blockquote
> It can span multiple lines

## Tables

```markdown
| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |
```

| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |

## Horizontal Rules

```markdown
---
or
***
```

---

## Footnotes

```markdown
Here's a sentence with a footnote[^1].

[^1]: This is the footnote content.
```

## Callouts (Obsidian-specific)

```markdown
> [!NOTE]
> This is a note callout

> [!TIP]
> This is a tip callout

> [!WARNING]
> This is a warning callout

> [!IMPORTANT]
> This is an important callout
```

> [!NOTE]
> This is a note callout

## Embedding

```markdown
![[Other Note]]  # Embeds entire note
![[Other Note#Section]]  # Embeds specific section
```

## Tags

```markdown
#tag
#nested/tag
#tag-with-dash
```

## Frontmatter (YAML)

```markdown
---
title: Note Title
date: 2026-01-30
tags: [tag1, tag2]
---
```

## Related Notes

- [[Getting Started with Obsidian]]
- [[Effective Note-Taking Strategies]]

---
*Keep this as a quick reference guide*
