# AI Chat

Conversational AI interface with message history, streaming responses, and rich content.

- Layer: L6 (composed)
- Dependencies: Button (L1), Icon Button (L1), Avatar (L1), Badge (L1), Chip (L1), Text Field (L3), Menu (L2), Loading (L5), Panel (L2)
- Status: pending
- Sprint: E

## Anatomy

- Message list: scrollable container with auto-scroll to bottom
- Message bubble: user (right-aligned, primary tonal) vs assistant (left-aligned, surface)
- Message content: supports markdown, code blocks, tables, images, file attachments
- Input area: multi-line text field + send button + attachment button
- Typing indicator: dots animation in assistant bubble position
- Suggested actions: chip row above input for quick replies
- Header: assistant name/avatar, status indicator, overflow menu

## Variants

- Modes: full-page (inbox style), embedded panel (side panel), floating widget (bottom-right)
- Message types: text, code (syntax highlighted), image, file card, action card (buttons), system message (centered, muted)
- Features: message reactions, copy button, regenerate, edit user message, branch conversations

## Viewport

Desktop: panel or full-page with 600px max message width centered.
Mobile: full-screen, input pinned to bottom with keyboard-aware repositioning.

## Motion

Message appear: fade-up `--motion-fast`. Streaming text: character-by-character with cursor blink. Typing dots: sequential bounce `--motion-normal`. Suggested actions: staggered fade-in `--motion-fast`.

## Valid Tokens

`--surface-default` (chat bg), `--surface-raised` (assistant bubble), `--primary-tonal-bg` (user bubble), `--primary-tonal-fg` (user text), `--content-primary` (assistant text), `--content-tertiary` (timestamp), `--border-subtle` (input border), `--space-3` (message gap), `--space-4` (bubble padding), `--radius-lg` (bubbles), `--radius-xl` (input area), `--shadow-md` (floating widget)

## Forbidden

- Never auto-send on Enter without Shift+Enter for newline support.
- Streaming responses must not cause layout shift in the message list.
- Message list must preserve scroll position when loading older messages (prepend, not shift).
- Floating widget must not obscure critical page content.
