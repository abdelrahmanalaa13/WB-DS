# Container Architecture

Defines how every bounded surface in the design system is structured, styled, and behaves across viewports.

## Three-Layer Model

Every container is composed from three independent layers. Each layer can be mixed and matched without affecting the others.

### Layer 1: Surface (Visual Shell)

The `ds-surface` base class. Owns the visual treatment of any bounded region.

**Responsibilities:**
- Border radius (scaled by density: compact=radius-md, default=radius-lg, comfortable=radius-xl)
- Overflow clipping
- Flex column layout
- Transition timing
- Interactive state layer (::before pseudo with currentColor + opacity)

**Density model (replaces "size" for containers):**

Containers are content-driven, not fixed-height like atoms. They use density instead of size. Density controls internal spacing, typographic scale, and radius. Width/height are never the card's responsibility -- the parent layout context controls dimensions.

| Density | Radius | Header padding | Content font | Use case |
|---------|--------|---------------|-------------|----------|
| compact | radius-md | space-2 / space-3 | font-size-xs | Dashboards, grids, sidebar cards |
| default | radius-lg | space-4 / space-5 | font-size-sm | General use |
| comfortable | radius-xl | space-5 / space-6 | font-size-base | Detail views, landing pages |

**Surface variants:**

| Variant | Background | Boundary | Use when |
|---------|-----------|----------|----------|
| elevated | `--surface-raised` | `--shadow-sm`, hover escalates to `--shadow-md` | Content needs visual lift (cards, popovers) |
| outlined | `--surface-default` | `box-shadow: inset 0 0 0 1px --border-subtle` | Content needs boundary without depth (forms, panels) |
| filled | `--surface-sunken` | none | Content sits recessed into the page (secondary regions) |

**Status surface variants (color-role containers):**

Status containers (Alert, Notification, Snackbar, Toast) reuse the existing `{role}-{emphasis}` token pattern from atoms.

| Emphasis | Background | Foreground | Border | Best for |
|----------|-----------|------------|--------|----------|
| tonal | `--{role}-tonal-bg` | `--{role}-tonal-fg` | none | Default alerts, inline status banners |
| filled | `--{role}-filled-bg` | `--{role}-filled-fg` | none | Critical/high-emphasis alerts |
| outlined | transparent | `--{role}-outline-fg` | `box-shadow: inset 0 0 0 1px --{role}-outline-border` | Subtle, minimal status indicators |

Supported roles: danger, warning, success, neutral (doubles as info), ai.

No dedicated `info` role. `neutral` serves this purpose. Rationale: adding a 7th role would add ~16 tokens per brand/theme combo. The visual distinction between "primary blue" and "info blue" confuses users. Neutral-tonal provides a clean non-colored informational treatment that contrasts well against the colored statuses.

### Layer 2: Layout (Slot Structure)

Defines how content is arranged inside the surface. Each layout type provides named content slots.

**Slot-based layout (Card, Alert, Dialog):**

```
+-------------------------------+
| header: title, subtitle, action |
+-------------------------------+
| media: flush-edge image/video  |  (optional, card only)
+-------------------------------+
| content: any child content     |
+-------------------------------+
| footer: action buttons         |  (optional)
+-------------------------------+
```

Slots are optional. A card can be header+content, content-only, or header+content+footer. The layout adapts.

**Bar layout (Snackbar, Bottom Nav, Toolbar):**

```
+---+------------------------+---+
| L |    content / label     | R |
+---+------------------------+---+
```

Horizontal, single-row. Leading slot (icon), content center, trailing slot (action/dismiss).

**Rail layout (Nav Rail):**

```
+--header--+
| nav-item |
| nav-item |
| nav-item |
| ...      |
+--footer--+
```

Vertical stacked items with optional header/footer.

**Port layout (Flow Node):**

```
   [in-port]
+------------+
| node body  |
+------------+
   [out-port]
```

Connection anchors on edges. Coordinate-based positioning. Completely separate from slot layout. Part of Wave 5 (AI Patterns), built from scratch as `ds-flow-node` within a `ds-flow-canvas` system.

### Layer 3: Behavior (Interaction + Viewport)

Composable traits that can be applied to any surface+layout combination.

**Interaction traits:**

| Trait | What it adds | Used by |
|-------|-------------|---------|
| interactive | State layer (hover/focus/pressed), cursor pointer | Clickable cards, tiles |
| draggable | Drag handle, ghost preview, drop zone detection | Kanban items, sortable lists, reorderable tabs |
| selectable | Selected state ring (`box-shadow: inset 0 0 0 2px --primary-filled-bg`), checkbox integration, multi-select keyboard | Plan pickers, grid selectors |
| dismissible | Close button or swipe-to-dismiss gesture, exit animation | Alerts, notifications, toasts, sheets |
| collapsible | Expand/collapse toggle, content height animation | Panels, accordions, card sections |
| resizable | Drag-to-resize handle, min/max constraints | Desktop panels, split views |

Traits are additive. A card in a kanban board is: `surface(elevated) + layout(slots) + interactive + draggable + selectable`.

**Viewport behavior:**

| Component | Desktop | Mobile | Transition method |
|-----------|---------|--------|-------------------|
| Card | Grid cell, constrained width | Full-width, stacked | Parent grid changes, card unchanged |
| Panel | Side region, collapsible/resizable | Bottom sheet, drag-to-dismiss | Same component, viewport-driven CSS |
| Dialog (compact) | Centered modal + backdrop | Centered modal + backdrop | No change |
| Dialog (large) | Centered modal + backdrop | Bottom sheet or full-screen | Viewport-driven layout switch |
| Snackbar/Toast | Fixed bottom-corner, max-width 400px | Full-width bottom, safe-area inset | Position + width CSS only |
| Alert | Inline, in content flow | Inline, in content flow | No change |
| Notification | Corner toast stack | Top banner, swipe-to-dismiss | Position + gesture change |
| Nav Rail | Side rail, collapsible | Hidden (replaced by Bottom Nav) | Component swap |
| Bottom Nav | Hidden | Sticky bottom bar | Component swap |
| Tabs | Inline, all visible | Scrollable, overflow indicator | Overflow behavior CSS |

Key insight: Panel and Sheet are the same component at different breakpoints. Do not build them separately. Build `ds-panel` that renders as a side panel on desktop and a bottom sheet on mobile.

## Component Registry (Concrete Containers)

### Neutral Containers

| Component | Surface | Layout | Default behaviors | Wave |
|-----------|---------|--------|-------------------|------|
| Card | elevated/outlined/filled | slot (header/media/content/footer) | interactive (optional) | 2 |
| Tile | elevated/outlined/filled | slot (minimal: icon + label + value) | interactive, selectable | 3 |
| Panel | elevated/outlined | slot (header/content/footer) | collapsible, resizable, sheet-on-mobile | 3 |
| Dialog | elevated | slot (header/content/footer) + backdrop | dismissible, sheet-on-mobile (large) | 3 |

### Status Containers

| Component | Surface | Layout | Default behaviors | Wave |
|-----------|---------|--------|-------------------|------|
| Alert | tonal/filled/outlined (role-colored) | bar (icon + content + action + dismiss) | dismissible (optional) | 3 |
| Snackbar | filled (role-colored) | bar (icon + content + action) | auto-dismiss, viewport-positioned | 3 |
| Toast | elevated + accent strip | bar (icon + content + action) | auto-dismiss, stacking, viewport-positioned | 3 |
| Notification | elevated | slot (header/content/actions) | dismissible, stacking, viewport-positioned | 3 |

### Specialized Containers

| Component | Surface | Layout | Default behaviors | Wave |
|-----------|---------|--------|-------------------|------|
| Flow Node | elevated | port (connection anchors) | draggable (canvas-relative), selectable | 5 |
| Popover | elevated | slot (content) | dismissible, anchor-positioned | 3 |
| Tooltip | filled-inverse | inline (text only) | hover/focus triggered, auto-positioned | 3 |
| Menu | elevated | list (menu items) | dismissible, keyboard navigable | 3 |

## Card Content Types

The card slot system must demonstrate flexibility through multiple content compositions in the tester.

| Content type | Slots used | Real-world use |
|-------------|-----------|----------------|
| text | header + content + footer | Settings panels, detail views, info cards |
| media | media + header + content | Knowledge base articles, product catalog |
| metric | header + content (large number + trend) | Dashboard KPIs, analytics widgets |
| profile | header (avatar + name) + content + footer | Contact cards, team member cards, entity summaries |
| list-item | content (compact horizontal: icon + text + meta) | Inbox items, notification cards, activity feed |
| action | content (icon/illustration + CTA) | Onboarding steps, empty states, quick actions |

## Token Requirements

No new token categories needed. The existing token set covers all containers:

- Surface tokens: `--surface-ground`, `--surface-default`, `--surface-raised`, `--surface-sunken`, `--surface-overlay`
- Border tokens: `--border-subtle`, `--border-default`, `--border-strong`
- Shadow tokens: `--shadow-xs` through `--shadow-xl`
- Role-emphasis tokens: `--{role}-{emphasis}-{bg|fg|border}` (already exist for all 6 roles x 4 emphasis levels)
- State tokens: `--state-hover` (0.08), `--state-focus` (0.12), `--state-pressed` (0.12)
- Radius tokens: `--radius-xs` through `--radius-full`
- Motion tokens: `--motion-fast`, `--motion-normal`, `--ease-standard`, `--ease-decelerate`

## Drag/Drop Architecture

Drag/drop is an interaction trait, not a container type.

`ds-draggable` is a wrapper/directive that adds:
- Drag handle (optional, defaults to entire surface)
- Ghost preview during drag (semi-transparent clone)
- Drop zone detection and visual feedback
- Reorder index tracking

It composes around any content. A kanban card is `ds-card` + `ds-draggable`. A sortable list item is `ds-list-item` + `ds-draggable`. The card/list-item knows nothing about being draggable.

Flow Node blocks are NOT draggable cards. They share `ds-surface` for their visual shell but have their own:
- Port system (input/output connection anchors)
- Canvas-relative coordinate positioning (not document flow)
- Edge/wire rendering between nodes
- Rubber-band multi-selection
- Zoom/pan on the canvas

`ds-flow-node` and `ds-flow-canvas` are purpose-built components in Wave 5, not card variants with drag behavior.

## Rules

1. Never duplicate surface treatment CSS across containers. All containers extend `ds-surface`.
2. Never bake viewport behavior into the surface layer. Surface is viewport-agnostic.
3. Never create a separate Sheet component. Panel handles both desktop-panel and mobile-sheet.
4. Never add an `info` color role. Use `neutral` for informational status.
5. Never put drag/drop logic inside a container component. Use the `draggable` trait.
6. Status containers use the same `{role}-{emphasis}` token pattern as atoms. No container-specific color tokens.
7. All outlined containers use `box-shadow: inset 0 0 0 1px` for borders. Never CSS `border`.
8. Interactive state layers use `::before` with `currentColor` + opacity. Never separate hover color tokens.
9. Never set width, min-width, max-width, height, min-height, or max-height on a container component. Dimensions are the parent layout's responsibility. Containers fill whatever space they are given.
10. Containers use density (compact/default/comfortable) not size (sm/md/lg). Size is for fixed-height atoms. Density is for content-driven containers.
