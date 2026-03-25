# DS-PATTERNS: Product Feature Patterns

Page-level and feature-level patterns that CONSUME the design system.
These are not reusable components. They are complete product features built from DS components.

## Distinction from DS-COMPONENTS

DS-COMPONENTS = reusable building blocks (Button, Card, AI Chat, etc.)
DS-PATTERNS = product features that compose those blocks into full experiences.

An AI agent building a product picks patterns from here, which reference components from DS-COMPONENTS.

---

## Pattern Registry

| Pattern | Description | Key Components Used | Status |
|---------|-------------|-------------------|--------|
| [Kanban Board](kanban.md) | Drag-and-drop column-based task management | Card, Badge, Avatar, Chip, Menu, Button | pending |
| [Flow Canvas](flow-canvas.md) | Node-based visual workflow/logic builder | Panel, Menu, Button, Icon Button, Tooltip | pending |
| [Form Builder](form-builder.md) | Drag-and-drop form creation interface | Text Field, Selection Controls, Card, Button, Menu | pending |
| [Web Chat Widget](web-chat-widget.md) | Embeddable customer-facing chat interface | AI Chat, Button, Avatar, Badge | pending |
| [Dashboard](dashboard.md) | Analytics/metrics overview with configurable widgets | Card, Data Grid, Tabs, Loading, Empty States | pending |
| [Inbox](inbox.md) | Conversation list + detail split-view | List, Panel, AI Chat, Badge, Avatar, Tabs, Search | pending |

---

## AI Agent Rules for Patterns

1. Always read the pattern file BEFORE the component files it references
2. Patterns define LAYOUT and FLOW. Components define APPEARANCE and BEHAVIOR.
3. Never create a new component to satisfy a pattern need. Compose from existing DS-COMPONENTS.
4. If a pattern requires a component that does not exist, flag it as a dependency gap.
5. Patterns may define pattern-specific layout tokens (grid columns, panel widths) but never override component tokens.
