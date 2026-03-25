# Dashboard

Analytics and metrics overview with configurable widget grid.

- Type: Product Feature Pattern
- Status: pending

## Components Used

Card (L2), Data Grid (L5), Tabs (L4), Loading (L5), Empty States (L6), Button (L1), Icon Button (L1), Badge (L1), Chip (L1), Menu (L2), Date Picker (L3)

## Layout Structure

- Header: page title + date range picker + refresh button + layout toggle (grid/list)
- KPI row: 3-5 metric cards in horizontal scroll (value, delta, sparkline)
- Widget grid: CSS grid of cards, each containing a chart/table/metric
- Widget card: header (title + menu with expand/remove/configure) + chart body + optional footer
- Configurable: drag-to-reorder widgets, resize handles, add widget from catalog dialog

## Widget Types

- Metric card: single KPI with trend arrow and comparison period
- Line/bar/area chart: time-series data with legend
- Pie/donut chart: distribution breakdown
- Data table: compact data grid within card
- Activity feed: recent events list
- Map: geographic data (placeholder, third-party integration)

## Viewport

Desktop: multi-column grid (12-column, widgets span 3/4/6/12 columns).
Mobile: single column, widgets stack vertically, charts resize to fit.

## Layout Tokens

`--dashboard-grid-gap: var(--space-4)`, `--dashboard-widget-min-height: 240px`, `--dashboard-kpi-card-min-width: 200px`

## Data Behavior

All widgets must show loading skeletons on initial fetch. Stale data (>5min) shows subtle "Last updated" timestamp. Error state per widget (not full page). Refresh updates all widgets with staggered loading.
