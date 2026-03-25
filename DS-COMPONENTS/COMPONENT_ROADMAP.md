# Component Roadmap

Routing table for the design system. Read this file first. Then read only the spec file you need.

## How to Use This File

1. Find the component by category below
2. Read its spec file (one file, under 40 lines)
3. The spec file lists valid tokens, forbidden patterns, and dependencies
4. Never load all spec files at once

## Categories

### General (L1 primitives, no dependencies)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| Button | [general/button.md](general/button.md) | done | ds-library.html |
| Icon Button | [general/icon-button.md](general/icon-button.md) | done | ds-library.html |
| Badge | [general/badge.md](general/badge.md) | done | ds-library.html |
| Chip | [general/chip.md](general/chip.md) | done | ds-library.html |
| Toggle | [general/toggle.md](general/toggle.md) | done | ds-library.html |
| Avatar | [general/avatar.md](general/avatar.md) | done | ds-library.html |
| Divider | [general/divider.md](general/divider.md) | done | ds-library.html |

### Containers (L2, depend on General for slot content)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| Card | [containers/card.md](containers/card.md) | done | ds-library.html |
| Panel | [containers/panel.md](containers/panel.md) | done | ds-library.html |
| Dialog | [containers/dialog.md](containers/dialog.md) | done | ds-library.html |
| Alert | [containers/alert.md](containers/alert.md) | done | ds-library.html |
| Snackbar | [containers/snackbar.md](containers/snackbar.md) | done | ds-library.html |
| Toast | [containers/toast.md](containers/toast.md) | done | ds-library.html |
| Tooltip | [containers/tooltip.md](containers/tooltip.md) | done | ds-library.html |
| Popover | [containers/popover.md](containers/popover.md) | done | ds-library.html |
| Menu | [containers/menu.md](containers/menu.md) | done | ds-library.html |

### Inputs (L3, depend on General for icons/badges)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| Text Field | [inputs/text-field.md](inputs/text-field.md) | done | ds-library.html |
| Selection Controls | [inputs/selection-controls.md](inputs/selection-controls.md) | done | ds-library.html |
| Slider | [inputs/slider.md](inputs/slider.md) | done | ds-library.html |
| Date Picker | [inputs/date-picker.md](inputs/date-picker.md) | done | ds-library.html |
| Tag Input | [inputs/tag-input.md](inputs/tag-input.md) | done | ds-library.html |
| File Upload | [inputs/file-upload.md](inputs/file-upload.md) | done | ds-library.html |

### Navigation (L4, depend on General + Containers)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| Tabs | [navigation/tabs.md](navigation/tabs.md) | done | ds-library.html |
| Breadcrumb | [navigation/breadcrumb.md](navigation/breadcrumb.md) | done | ds-library.html |
| Nav Rail | [navigation/nav-rail.md](navigation/nav-rail.md) | done | ds-library.html |
| Bottom Nav | [navigation/bottom-nav.md](navigation/bottom-nav.md) | done | ds-library.html |
| Pagination | [navigation/pagination.md](navigation/pagination.md) | done | ds-library.html |
| Stepper | [navigation/stepper.md](navigation/stepper.md) | done | ds-library.html |

### Data Display (L5, depend on L1-L4)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| List | [data-display/list.md](data-display/list.md) | done | ds-library.html |
| Accordion | [data-display/accordion.md](data-display/accordion.md) | done | ds-library.html |
| Data Grid | [data-display/data-grid.md](data-display/data-grid.md) | done | ds-library.html |
| Tree View | [data-display/tree-view.md](data-display/tree-view.md) | done | ds-library.html |
| Timeline | [data-display/timeline.md](data-display/timeline.md) | done | ds-library.html |
| Loading | [data-display/loading.md](data-display/loading.md) | done | ds-library.html |

### Composed (L6, depend on L1-L5)

| Component | File | Status | Baseline |
|-----------|------|--------|----------|
| AI Chat | [composed/ai-chat.md](composed/ai-chat.md) | done | ds-library.html |
| Command Palette | [composed/command-palette.md](composed/command-palette.md) | done | ds-library.html |
| Notification Center | [composed/notification-center.md](composed/notification-center.md) | done | ds-library.html |
| Search | [composed/search.md](composed/search.md) | done | ds-library.html |
| Empty States | [composed/empty-states.md](composed/empty-states.md) | done | ds-library.html |

## Product Feature Patterns (separate folder)

These are NOT components. They are page-level or product-level features that consume DS components. Composition guides live in [../DS-PATTERNS/](../DS-PATTERNS/).

| Pattern | File | Components Used |
|---------|------|----------------|
| Kanban Board | [DS-PATTERNS/kanban.md](../DS-PATTERNS/kanban.md) | Card, Draggable trait, Badge, Avatar, List |
| Flow Canvas | [DS-PATTERNS/flow-canvas.md](../DS-PATTERNS/flow-canvas.md) | ds-surface (node shell), custom port/edge system |
| Form Builder | [DS-PATTERNS/form-builder.md](../DS-PATTERNS/form-builder.md) | All Inputs, Accordion, Stepper, Card |
| Web Chat Widget | [DS-PATTERNS/web-chat-widget.md](../DS-PATTERNS/web-chat-widget.md) | AI Chat, Button (FAB), Panel |
| Dashboard Layout | [DS-PATTERNS/dashboard.md](../DS-PATTERNS/dashboard.md) | Card (metric/profile/list-item), Data Grid, Tabs |
| Inbox Layout | [DS-PATTERNS/inbox.md](../DS-PATTERNS/inbox.md) | List, Panel, AI Chat, Badge |

## Build Sprints

Dependency-ordered. Each sprint only uses components from previous sprints.

| Sprint | Components | Depends on |
|--------|-----------|------------|
| A (done) | Panel, Dialog, Alert, Snackbar, Toast | General (done) |
| B (done) | Tooltip, Popover, Menu, Text Field, Selection Controls | General + Sprint A |
| C (done) | Slider, Date Picker, Tag Input, File Upload, Pagination, Stepper | General + Containers + Sprint B inputs |
| D (done) | List, Accordion, Data Grid, Tree View, Timeline, Loading | All previous |
| E (done) | AI Chat, Command Palette, Notification Center, Search, Empty States | All previous |

## AI Agent Rules

1. Read this file first. It is the only entry point.
2. Read `DS_UNIVERSAL_CHECKLIST.md` before writing any CSS or JS. This contains 19 mandatory rules (U1-U19) that apply to every component. This is the build-time gate.
3. Read only the spec file for the component you are building or using.
4. Never invent tokens. The spec file lists every valid token. If a token is not listed, it does not exist.
5. Never invent components. If it's not in this roadmap, it's not in the system.
6. Check the "forbidden" section in each spec before generating code.
7. Check dependencies. If a dependency is "pending," you cannot use it yet.
8. When composing, trace the dependency chain: Pattern -> Composed -> Data/Nav -> Inputs -> Containers -> General.
9. For each CSS rule you write, verify: interactive element? (U1-U6), text element? (U7-U9), positioned? (U10-U13), animated? (U14). Component done? (U15-U19).

## Architecture References

| Doc | Purpose | When to read |
|-----|---------|-------------|
| [DS_UNIVERSAL_CHECKLIST.md](../DS_UNIVERSAL_CHECKLIST.md) | 19 mandatory build-time rules (U1-U19) | BEFORE writing any CSS/JS (mandatory) |
| [CONTAINER_ARCHITECTURE.md](../CONTAINER_ARCHITECTURE.md) | Surface/layout/behavior model | Building any container |
| [DS_LEARNING_LOOP.md](../DS_LEARNING_LOOP.md) | Learned patterns and propagation | After finishing any component |
| [ds-library.html](../ds-library.html) | Token definitions (CSS custom properties are source of truth) | Resolving token values |
| [ds-rethink.html](../ds-rethink.html) | Quiet Intelligence philosophy | Understanding design intent |
