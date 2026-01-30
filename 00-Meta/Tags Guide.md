---
title: Tags Guide
tags: [meta, guide, reference]
type: guide
created: 2026-01-30
---

# 🏷️ Tags Guide

## Purpose
Tags help you categorize and find notes across your vault. Unlike folders, a note can have multiple tags, allowing for flexible organization.

## Tag Strategy

### Core Tags

#### By Type
- `#index` - Map of Contents and navigation pages
- `#guide` - How-to guides and documentation
- `#resource` - Reference materials
- `#project` - Project-related notes
- `#meeting` - Meeting notes
- `#note` - Standard notes and ideas

#### By Status
- `#todo` - Action items and tasks
- `#in-progress` - Currently working on
- `#done` - Completed items
- `#archived` - Archived content

#### By Topic
Create topic-specific tags as needed:
- `#programming`
- `#learning`
- `#productivity`
- `#books`
- `#articles`

### Nested Tags
Use `/` for hierarchy:
- `#programming/python`
- `#programming/javascript`
- `#books/fiction`
- `#books/non-fiction`

## Best Practices

### 1. **Be Consistent**
- Use lowercase
- Use hyphens for multi-word tags: `#project-management`
- Avoid spaces in tags

### 2. **Don't Over-Tag**
- 3-5 tags per note is usually sufficient
- Tags should add value, not just categorize

### 3. **Review and Refine**
- Periodically review your tags
- Merge similar tags
- Remove unused tags
- Standardize naming

### 4. **Combine with Folders**
- Use folders for broad categories
- Use tags for cross-cutting themes
- Tags work across folder boundaries

## Common Tag Patterns

### Status Tags
```markdown
#active #paused #completed #archived
```

### Priority Tags
```markdown
#high-priority #medium-priority #low-priority
```

### Context Tags
```markdown
#work #personal #learning
```

### Time Tags
```markdown
#daily #weekly #monthly
```

## Using Tags Effectively

### Search by Tag
- Click on a tag to see all notes with that tag
- Use the tag pane to browse all tags
- Combine tags in search: `tag:#project tag:#active`

### Tag Combinations
```markdown
tags: [project, programming, active]
```

Or inline:
```markdown
#project #programming #active
```

### In Frontmatter
```yaml
---
tags: [guide, meta, tags]
---
```

## Tag Maintenance

### Regular Review
- Monthly: Review and clean up tags
- Merge duplicate or similar tags
- Remove tags from archived notes
- Update tag usage as vault evolves

### Tag Index
Keep a list of your most-used tags and their purposes:

| Tag | Purpose | Example Use |
|-----|---------|-------------|
| #index | Navigation pages | Home, Project Index |
| #project | Active projects | Project notes |
| #resource | Reference material | Articles, guides |
| #meeting | Meeting notes | Team meetings |
| #todo | Action items | Tasks and reminders |

## Advanced Techniques

### Dataview Queries
If using Dataview plugin:
```dataview
LIST
WHERE contains(tags, "project")
AND contains(tags, "active")
```

### Tag Hierarchies
Create meaningful hierarchies:
```markdown
#work/projects/client-a
#work/projects/client-b
#work/meetings
```

## Related Notes

- [[Vault Guide]]
- [[Getting Started with Obsidian]]

---
*Update this guide as your tagging system evolves*
