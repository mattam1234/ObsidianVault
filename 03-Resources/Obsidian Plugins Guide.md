---
title: Obsidian Plugins Guide
created: 2026-01-30
tags: [resource, obsidian, plugins]
type: guide
---

# 🔌 Obsidian Plugins Guide

## Overview

Obsidian has a rich ecosystem of community plugins that extend its functionality. This guide covers essential and popular plugins.

## Core Plugins

Core plugins are built into Obsidian and can be enabled in Settings.

### Essential Core Plugins

#### Templates
- Insert note templates
- Support date/time variables
- Location: Settings → Core plugins → Templates

#### Daily Notes
- Automatically create daily notes
- Customizable format
- Quick access via hotkey

#### Backlinks
- See which notes link to current note
- Discover connections
- Always enabled

#### Graph View
- Visualize note connections
- Filter by tags
- Interactive exploration

#### Quick Switcher
- Fast file navigation
- Fuzzy search
- Keyboard-driven

#### File Explorer
- Browse vault structure
- Drag and drop files
- Context menus

#### Search
- Full-text search
- Regular expressions
- Search operators

#### Tag Pane
- Browse all tags
- Click to filter
- See tag usage

#### Page Preview
- Hover to preview notes
- Quick reference
- Reduce context switching

#### Outline
- Current file structure
- Navigate headings
- Overview of document

## Popular Community Plugins

> **Note**: Community plugins need to be enabled in Settings → Community plugins

### Productivity

#### Dataview
**Purpose**: Query and display data from notes
- Create dynamic lists
- Build dashboards
- Advanced filtering

**Example**:
```dataview
TABLE file.cdate as "Created", tags
FROM #project
WHERE status = "active"
```

#### Calendar
**Purpose**: Visual calendar interface
- Navigate daily notes
- See note creation dates
- Quick date selection

#### Kanban
**Purpose**: Kanban board view
- Visual task management
- Drag and drop
- Multiple boards

#### Tasks
**Purpose**: Enhanced task management
- Query tasks across vault
- Due dates and priorities
- Task organization

### Writing & Editing

#### Advanced Tables
**Purpose**: Better table editing
- Spreadsheet-like controls
- Auto-formatting
- Formulas

#### Templater
**Purpose**: Advanced templating
- JavaScript support
- Dynamic templates
- Custom functions

**Better than core Templates for**:
- Complex date formatting
- Conditional content
- User prompts

#### Editor Syntax Highlight
**Purpose**: Code syntax highlighting
- Multiple languages
- Readable code blocks
- Developer-friendly

### Organization

#### Tag Wrangler
**Purpose**: Tag management
- Rename tags globally
- Merge tags
- Tag hierarchy

#### Folder Note
**Purpose**: Notes for folders
- Folder-level documentation
- Index generation
- Hierarchy visualization

#### Note Refactor
**Purpose**: Extract and split notes
- Create new notes from selections
- Maintain links
- Organize content

### Linking & Discovery

#### Link Autocomplete
**Purpose**: Suggest links as you type
- Faster linking
- Discover related notes
- Reduce errors

#### Juggl
**Purpose**: 3D graph visualization
- Interactive knowledge graph
- Filter and style
- Alternative to built-in graph

#### Breadcrumbs
**Purpose**: Navigate note hierarchies
- Parent-child relationships
- Trail views
- Custom hierarchies

### Import & Export

#### Obsidian Git
**Purpose**: Version control integration
- Auto-commit
- Sync across devices
- Backup to GitHub

#### Markdown Importer
**Purpose**: Import from other apps
- Notion import
- Evernote import
- Format conversion

#### Pandoc
**Purpose**: Export to various formats
- PDF, DOCX, etc.
- Professional output
- Citations support

### Appearance

#### Style Settings
**Purpose**: Customize theme settings
- No CSS knowledge needed
- Per-theme options
- Easy customization

#### Icon Folder
**Purpose**: Custom folder icons
- Visual organization
- Emoji support
- Color coding

## Plugin Installation

### Installing Community Plugins

1. Go to Settings → Community plugins
2. Turn off Safe mode (first time only)
3. Click "Browse"
4. Search for plugin
5. Click "Install"
6. Click "Enable"

### Plugin Settings

- Each plugin has its own settings
- Access via Settings → Plugin name
- Configure hotkeys separately
- Read plugin documentation

## Essential Plugin Combinations

### For Students
- Dataview
- Calendar
- Advanced Tables
- Citation plugin

### For Writers
- Templater
- Word Count
- Natural Language Dates
- Editor Syntax Highlight

### For Developers
- Obsidian Git
- Code Block Enhancer
- Editor Syntax Highlight
- Advanced Tables

### For Project Management
- Kanban
- Tasks
- Calendar
- Dataview

## Plugin Best Practices

### Start Minimal
- Don't install too many at once
- Learn one before adding another
- Remove unused plugins
- Keep it simple

### Regular Updates
- Update plugins regularly
- Check for breaking changes
- Read update notes
- Backup before major updates

### Performance
- Too many plugins can slow Obsidian
- Disable unused plugins
- Monitor performance
- Use mobile plugins selectively

## Advanced Plugin Usage

### Dataview Examples

**Active Projects**:
```dataview
LIST
FROM #project
WHERE status = "active"
SORT file.mtime DESC
```

**Recent Notes**:
```dataview
TABLE file.mtime as "Modified"
SORT file.mtime DESC
LIMIT 10
```

### Templater Examples

**Smart Date**:
```
Today is <% tp.date.now("dddd, MMMM DD, YYYY") %>
```

**User Input**:
```
Title: <% tp.system.prompt("Enter title") %>
```

## Troubleshooting

### Plugin Not Working
1. Check if it's enabled
2. Restart Obsidian
3. Check for updates
4. Read plugin documentation
5. Search community forum

### Conflicts
- Some plugins may conflict
- Disable one to test
- Check plugin settings
- Report issues to developers

### Performance Issues
- Disable resource-heavy plugins
- Use fewer graph/canvas plugins
- Clear cache
- Restart Obsidian

## Community Resources

### Finding Plugins
- [Obsidian Plugin Stats](https://obsidian-plugin-stats.vercel.app/)
- Community forum
- Discord channels
- YouTube tutorials

### Plugin Development
- [Plugin Developer Docs](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin)
- GitHub templates
- Community support
- TypeScript knowledge helpful

## Recommended Plugin Sets

### Minimal Setup
- Templates (core)
- Daily Notes (core)
- Backlinks (core)
- Quick Switcher (core)

### Intermediate Setup
Add:
- Calendar
- Dataview
- Templater
- Advanced Tables

### Advanced Setup
Add:
- Tasks
- Kanban
- Obsidian Git
- Breadcrumbs

## Related Notes

- [[Getting Started with Obsidian]]
- [[Vault Guide]]

---
*Plugin ecosystem constantly evolving - check for new additions regularly*
