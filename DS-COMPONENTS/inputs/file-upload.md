# File Upload

Drag-and-drop zone with file list management.

- Layer: L3 (input)
- Dependencies: Button (L1), Icon Button (L1), Badge (L1)
- Status: done
- Sprint: C

## Variants

- States: idle, dragging-over (dashed border highlight), uploading (progress), complete, error
- File list: name, size, progress bar, remove button
- With/without: image preview thumbnails

## Valid Tokens

`--border-default` (dashed idle), `--primary-outline-border` (drag-over), `--surface-sunken` (zone bg), `--primary-filled-bg` (progress bar), `--danger-outline-fg` (error), `--radius-lg`

## Forbidden

- Always provide a "Browse" button fallback alongside drag-and-drop.
- File size/type validation must show inline error per file.
