# WideBot AI Design System

**v1.0.0-build.20260326**

AI-native, token-driven design system for enterprise SaaS, AI platforms, CX systems, and ERP products. Built for both human designers and generative AI agents.

## What's Inside

- **42 components** across 6 categories (general, inputs, data-display, containers, navigation, composed)
- **8 AI registries** (component, generative-ui, governance, interaction, knowledge-graph, mobile-touch, motion, responsive)
- **6 design patterns** (dashboard, inbox, kanban, flow-canvas, form-builder, web-chat-widget)
- **Dual-brand support**: WideBot (WCAG AAA) + Hulul (WCAG AA)
- **Bidirectional**: RTL (Arabic) + LTR (English) via CSS logical properties
- **Themes**: Light + Dark
- **19 governance rules** (U1-U19) for consistent, accessible UI generation
- **AG-UI protocol** for generative UI (agent proposes WHAT, frontend decides HOW)

## Quick Start

### For Designers

Open `ds-library.html` in a browser to browse all components with live theme/brand/direction switching. The file is the single source of truth for every token, variant, and interaction.

### For Developers

1. Clone the repo:

```bash
git clone https://github.com/abdelrahmanalaa13/WB-DS.git
cd WB-DS
```

2. Set the brand on your root HTML element:

```html
<html dir="ltr" data-brand="widebot" data-theme="light">
```

Supported values: `data-brand="widebot" | "hulul"`, `data-theme="light" | "dark"`, `dir="ltr" | "rtl"`

3. Reference the AI registries in your build pipeline. Each JSON file is machine-readable and designed for consumption by generative agents or component libraries.

### For AI Agents (Claude Skills)

Install the skills from `skills-to-install/` into your Claude project:

| Skill | Purpose |
|-------|---------|
| `ds-copilot` | Design system architecture and component governance |
| `ds-frontend-copilot` | Ensures generated code follows DS rules |
| `ds-ai-agent-copilot` | AI behavior patterns and interaction UX |
| `ds-product-compliance-copilot` | Enterprise compliance and AI governance |
| `ds-ux-research-copilot` | UX validation and usability insights |

Example prompts after installing skills:

```
"Build a dashboard page using the DS for WideBot brand, dark theme, RTL"
"Validate this component against the 19 governance rules"
"Generate the Button component in all variants for Hulul light theme"
```

## Repo Structure

```
WB-DS/
  ds-library.html            # Source of truth (42 components, all tokens)
  index.html                 # GitHub Pages portal
  VERSION                    # Current version (1.0.0-build.20260326)
  DS-v1.0.0-Release-Notes.docx  # Full release notes with guides
  ai-*.json                  # 8 AI registries
  AG-UI_KNOWLEDGE.md         # AG-UI protocol documentation
  CONTAINER_ARCHITECTURE.md  # Container component architecture
  DS_LEARNING_LOOP.md        # Continuous learning log
  DS_UNIVERSAL_CHECKLIST.md  # QA validation checklist
  DS-COMPONENTS/             # Component specifications (markdown)
    general/                 # button, avatar, badge, chip, divider, icon-button, toggle
    inputs/                  # text-field, selection-controls, slider, date-picker, tag-input, file-upload
    data-display/            # accordion, list, data-grid, timeline, tree-view, loading
    containers/              # alert, card, dialog, menu, panel, popover, snackbar, toast, tooltip
    navigation/              # tabs, breadcrumb, pagination, stepper, nav-rail, bottom-nav
    composed/                # search, command-palette, notification-center, ai-chat, empty-states
    QC/                      # Pre/post-build audit reports
  DS-PATTERNS/               # Page-level design patterns
  skills-to-install/         # Claude skills for AI-native workflow
  archive/                   # Historical research and planning docs
  .claude/                   # Project config and core skills
```

## Versioning

Format: `MAJOR.MINOR.PATCH-build.YYYYMMDD`

- **MAJOR**: breaking changes to tokens, component schemas, or knowledge graph
- **MINOR**: new components, patterns, templates, or features
- **PATCH**: fixes, doc updates, token adjustments

## Brands

| Brand | Accessibility | Target |
|-------|--------------|--------|
| WideBot | WCAG AAA | Enterprise and government customers |
| Hulul | WCAG AA | Small-to-medium business customers |

## Icons

Uses [HugeIcons](https://hugeicons.com/) library with RTL mirroring support.

## License

Proprietary. Internal use only.
