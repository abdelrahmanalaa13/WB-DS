# Web Chat Widget

Embeddable customer-facing chat interface for websites.

- Type: Product Feature Pattern
- Status: pending

## Components Used

AI Chat (L6), Button (L1), Icon Button (L1), Avatar (L1), Badge (L1), Loading (L5)

## Layout Structure

- Launcher: floating action button (bottom-right, RTL: bottom-left) with unread badge
- Widget: expanded chat panel (400px wide, 600px tall max)
- Header: brand logo/name + status + minimize/close buttons
- Body: AI Chat component in embedded panel mode
- Pre-chat form: name/email collection before first message (optional)
- Footer: powered-by branding (optional)

## Interaction Flow

1. Click launcher to expand widget (scale + fade animation)
2. Pre-chat form collects user info (if configured)
3. Chat begins with greeting message from assistant
4. User types and receives AI responses
5. Minimize returns to launcher with unread count
6. Close ends session (with confirmation if conversation active)

## Viewport

Desktop: floating widget, fixed position bottom-right.
Mobile: full-screen takeover when opened, launcher remains floating.

## Layout Tokens

`--widget-width: 400px`, `--widget-height: 600px`, `--widget-launcher-size: 56px`, `--widget-offset: var(--space-5)` (distance from screen edge)

## Embedding Rules

Widget must be iframe-isolated for CSS safety. Communication via postMessage API. Widget loads its own token set (brand-specific). Host page styles must never leak in.
