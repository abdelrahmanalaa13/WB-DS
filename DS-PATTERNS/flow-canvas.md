# Flow Canvas

Node-based visual workflow/logic builder with connections.

- Type: Product Feature Pattern
- Status: pending

## Components Used

Panel (L2), Button (L1), Icon Button (L1), Menu (L2), Tooltip (L2), Badge (L1), Dialog (L2), Search (L6), Loading (L5)

## Layout Structure

- Canvas: infinite pan/zoom surface with grid background
- Toolbar: top bar with zoom controls, undo/redo, run/test, save
- Node palette: left panel with draggable node types (collapsible)
- Node: custom flow-node component (NOT a card) with input/output ports, title, icon, status indicator
- Edge/wire: SVG path connecting output port to input port with directional arrow
- Properties panel: right slide-in panel for selected node configuration
- Minimap: bottom-right thumbnail overview of full canvas

## Flow Node Anatomy

- Header: icon + node type label + status dot
- Body: condensed config preview (1-2 lines)
- Ports: input circles (left/top) + output circles (right/bottom), typed by color
- States: idle, selected (primary border), running (pulse animation), success (green dot), error (red dot + error badge)

## Interaction Flow

1. Drag node from palette to canvas (creates instance)
2. Click-drag from output port to input port (creates edge)
3. Click node to select, opens properties panel
4. Double-click node to edit inline
5. Ctrl+click for multi-select, drag to move group
6. Delete key removes selected nodes/edges
7. Canvas pan: middle-click drag or space+drag
8. Canvas zoom: scroll wheel or pinch

## Viewport

Desktop only. Minimum 1024px viewport width. Tablet: simplified view with limited palette. Mobile: read-only preview, no editing.

## Layout Tokens

`--flow-grid-size: 20px`, `--flow-node-width: 200px`, `--flow-node-min-height: 80px`, `--flow-edge-color: var(--border-default)`, `--flow-port-size: 12px`

## Important Notes

Flow nodes are NOT cards. They are a separate component with port system, canvas-relative positioning, and edge/wire rendering. They do not use ds-surface or ds-card classes.
