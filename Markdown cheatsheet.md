# Markdown

Markdown is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML).

Documentation: [Markdown Docs](https://daringfireball.net/projects/markdown/) RFC: [RFC 7763](https://www.rfc-editor.org/rfc/rfc7763) GitHub Documentation: [Writing Markdown on GitHub](https://docs.github.com/en/get-started/writing-on-github)

---

## [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#cheat-sheet)Cheat-Sheet

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#headings)Headings

# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6

Here is a heading: `# Heading`, **don't do this:** `#Heading`

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#emphasis)Emphasis

Emphasis, aka italics, with *asterisks* or _underscores_.

Strong emphasis, aka bold, with **asterisks** or __underscores__.

Combined emphasis with **asterisks and _underscores_**.

Strikethrough uses two tildes. ~~Scratch this.~~

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#line-breaks)Line Breaks

First line with two spaces after.  
And the next line.

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#lists)Lists

#### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#ordered-lists)Ordered Lists

1. First item
2. Second item
3. Third item

#### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#unordered-lists)Unordered Lists

- First item
- Second item
- Third item

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#links)Links

Link with text: [link-text](https://www.google.com)

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#images)Images

Image with alt text: ![alt-text](https://camo.githubusercontent.com/4d89cd791580bfb19080f8b0844ba7e1235aa4becc3f43dfd708a769e257d8de/68747470733a2f2f636e642d70726f642d312e73332e75732d776573742d3030342e6261636b626c617a6562322e636f6d2f6e65772d62616e6e6572342d7363616c65642d666f722d6769746875622e6a7067)

Image without alt text: ![](https://camo.githubusercontent.com/4d89cd791580bfb19080f8b0844ba7e1235aa4becc3f43dfd708a769e257d8de/68747470733a2f2f636e642d70726f642d312e73332e75732d776573742d3030342e6261636b626c617a6562322e636f6d2f6e65772d62616e6e6572342d7363616c65642d666f722d6769746875622e6a7067)

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#code-blocks)Code Blocks

#### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#inline-code-block)Inline Code Block

Inline `code` has `back-ticks around` it.

#### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#blocks-of-code)Blocks of Code

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
 
```python
s = "Python syntax highlighting"
print s
```
 
```
No language indicated, so no syntax highlighting. 
But let's throw in a **tag**.
```

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#tables)Tables

There must be at least 3 dashes separating each header cell. The outer pipes (|) are optional, and you don't need to make the raw Markdown line up prettily.

| Heading 1 | Heading 2 | Heading 3 |
|---|---|---|
| col1 | col2 | col3 |
| col1 | col2 | col3 |

### [](https://github.com/christianlempa/cheat-sheets/blob/main/misc/markdown.md#task-list)Task list

To create a task list start line with square brackets with an empty space. Ex: [ ] and add text for task. To check the task replace the space between the bracket with "x".

[x] Write the post
[ ] Update the website
[ ] Contact the user