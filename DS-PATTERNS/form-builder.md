# Form Builder

Drag-and-drop form creation and configuration interface.

- Type: Product Feature Pattern
- Status: pending

## Components Used

Text Field (L3), Selection Controls (L3), Button (L1), Icon Button (L1), Card (L2), Menu (L2), Panel (L2), Dialog (L2), Chip (L1), Divider (L1), Empty States (L6)

## Layout Structure

- Field palette: left panel with draggable field types (text, number, email, dropdown, checkbox, radio, date, file upload, section divider)
- Form canvas: center area showing form preview with drop zones between fields
- Properties panel: right panel for selected field configuration (label, placeholder, validation rules, required flag, conditional logic)
- Toolbar: top bar with save, preview, publish, undo/redo, form settings
- Form preview: toggleable live preview in dialog (desktop and mobile viewport simulation)

## Interaction Flow

1. Drag field type from palette to form canvas
2. Click field to select, opens properties panel
3. Drag to reorder fields within form
4. Configure validation rules per field (required, min/max, regex, custom)
5. Set conditional visibility (show field B if field A = value)
6. Preview form in dialog with simulated data entry
7. Publish generates form schema (JSON) consumable by frontend

## Viewport

Desktop: three-panel layout (palette + canvas + properties).
Tablet: palette collapses to icon strip, properties in bottom sheet.
Mobile: read-only preview of built forms. Building requires desktop.

## Layout Tokens

`--form-builder-palette-width: 240px`, `--form-builder-props-width: 320px`, `--form-field-gap: var(--space-3)`
