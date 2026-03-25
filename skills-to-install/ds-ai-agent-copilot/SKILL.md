---
name: ds-ai-agent-copilot
description: >
  AI behavior patterns and AI interaction UX agent for the design system. Defines how AI-powered
  features should behave inside products built with this design system, including streaming responses,
  confidence indicators, error/fallback UX, human handoff flows, and conversation memory indicators.
  Use this skill whenever designing AI-facing UI, building AI chat interfaces, creating copilot panels,
  implementing suggestion engines, or defining AI behavior in any product feature. Also trigger when
  the user says "AI UX", "streaming UI", "AI response pattern", "copilot behavior", "AI error state",
  "confidence indicator", "AI loading state", "human handoff UX", "AI suggestion UX", "conversation
  memory UI", "AI feedback loop", or any phrase involving AI behavior within a product interface.
  Trigger proactively when working on AI Chat, Command Palette, or any AI-powered composed component.
---

## Role and Scope

This agent owns all AI-specific interaction patterns in the design system. It works alongside ds-copilot (which governs component governance) but focuses exclusively on how AI features should behave within products.

Your rules apply to any component or pattern involving AI-generated content, AI suggestions, or AI-driven interactions. You are the authority on streaming UX, loading states, confidence signaling, error fallbacks, human handoffs, and conversation memory.

## AI Response Rendering Patterns

### Streaming Text
- Render character-by-character as tokens arrive
- Show animated cursor (blinking) at the end of text using --motion-fast (150ms) blink cycle
- Text appears in --content-primary color within --surface-raised message bubbles
- Do not cause layout shifts in the message list; pre-allocate bubble space if needed

### Code Blocks
- Render block container immediately with language badge
- Code syntax highlighting appears only after the block completes streaming (not during)
- Use pre-formatted font with monospace stack; copy button appears on block completion
- If code streaming takes longer than 3 seconds, show skeleton with "Code loading..." text

### Markdown Rendering
- Progressive rendering as tokens arrive: headings render when complete, lists build item-by-item
- Inline formatting (bold, italic, links) applies as they close
- Tables render row-by-row as complete rows are received
- Do not delay markdown rendering waiting for the full response

### Media and Images
- Show skeleton placeholder (using Loading component skeleton variant) during generation
- Fade-in (using --motion-normal 250ms ease) when image/media arrives
- Display with aspect ratio preserved; use object-fit: cover for thumbnails
- File attachments show as card with icon, name, and size; expand on click

### Error During Streaming
- Show the partial response that was successfully generated
- Place inline error indicator below the partial response (Alert component, warning variant)
- Provide a "Retry" button that resubmits the same prompt
- If error persists on retry, escalate to human handoff option

### Long Responses
- Enable auto-scroll to bottom as content streams (unless user has scrolled up)
- Detect user scroll-up; pause auto-scroll and show "Scroll to bottom" FAB button (--motion-normal fade-in)
- FAB appears only if response is longer than 2 viewport heights
- Auto-scroll resumes when user manually scrolls to the very bottom

## Confidence and Uncertainty UX

### High Confidence (Default)
- Present response normally without additional indicators
- No disclaimer or visual marker needed

### Medium Confidence
- Add subtle caveat text in --content-tertiary color below the response: "This may not be completely accurate"
- Include a small info icon with tooltip: "AI confidence: medium. Please verify important details."
- Do not hide the response; present it with the caveat visible

### Low Confidence
- Use Alert component (warning variant) as a banner above the response
- Text: "I'm not very confident about this answer. Please verify before using."
- Suggest explicit human verification: "Would you like to escalate to a human agent?"
- Display response alongside the warning, not hidden

### No Data
- Never attempt to generate a response if the AI has no reliable data
- Show empty state: "I don't have information about this topic"
- Provide 2-3 actionable alternatives: "Try searching the knowledge base", "Ask a human agent", "Rephrase your question"
- Include a search box so user can refine the query

### Sources and Citation
- When citing specific data, show inline source chips that display the source name/URL
- Chips use Chip component with leadingIcon (link icon) and trailing action (expand icon)
- Clicking or expanding shows: document name, URL, confidence of citation, date accessed
- Format: "[Source: Document Name]" as a clickable chip within the response text

## AI Loading States

### Initial Thinking (0-3 seconds)
- Show typing indicator: three dots in sequential bounce animation (--motion-normal 250ms)
- Dots appear in assistant message bubble position, left-aligned
- Text: "AI is thinking..." in --content-secondary color
- Stop showing dots once response begins streaming

### Extended Thinking (3-10 seconds)
- Transition from dots to "Analyzing..." text
- Add subtle vertical pulse animation (--motion-slow 400ms easing) to the text
- Color: --content-secondary
- Optional: show a second line "This may take a moment..."

### Very Long Operations (>10 seconds)
- Display progress indicator (Loading component) with step descriptions
- Examples: "Searching knowledge base..." then "Generating response..." then "Formatting response..."
- Update descriptions every 3-4 seconds to keep user informed
- Use --motion-normal for transition between steps

### Background Processing
- When AI completes a background task (e.g., indexing, analysis), post a toast notification
- Toast text: "AI analysis complete: [task name]"
- Duration: 4 seconds, auto-dismiss
- Include dismiss button and link to view results

### Skeleton States
- For content areas that will be populated by AI (e.g., dashboard insights), use Loading component (skeleton variant)
- Show 3-5 placeholder lines matching the expected content height
- Fade-out skeleton and fade-in content using --motion-normal when ready

## Error and Fallback UX

### Transient Errors (Network, Timeout)
- Silently auto-retry once with exponential backoff (500ms initial)
- If retry succeeds, show no error to user
- If retry fails, show inline error message with manual "Retry" button
- Error text: "Couldn't reach the AI service. Please try again." (--content-error color)

### Model Errors (Rate Limit, Overload)
- Show friendly user-facing message: "I'm a bit busy right now. Trying again..."
- Display estimated wait time if available: "Try again in 45 seconds"
- Use countdown timer that auto-resets send button when time expires
- Never expose internal error codes or technical jargon

### Content Policy Blocks
- Message: "I can't help with that. Let me suggest an alternative approach:"
- Follow with 2-3 alternative questions or tasks the user can try
- Do not show raw policy violation text or codes
- Offer human escalation: "Would you like to talk to a human agent?"

### Hallucination Prevention
- When AI cannot verify a claim or source, show dashed underline (border-bottom: 1px dashed --content-warning)
- Hover tooltip: "Unverified claim. I'm not certain about this."
- Color: --content-warning
- Do not mark-up facts as unverified unless genuinely uncertain
- Always prefer transparency over false confidence

### Graceful Degradation
- If AI features become unavailable, product must remain usable with manual fallbacks
- Example: if AI suggestions fail, show manual search/filter controls
- Show a system banner: "AI features are temporarily unavailable. Using standard mode."
- Enable fallback CTA: "Try the manual [feature] instead"

## Human Handoff Patterns

### Trigger Conditions
1. User explicitly requests human: "Talk to a human", "I want to speak to support"
2. AI confidence is too low for the use case
3. Sensitive topic detected (PII, legal, medical, financial)
4. 3+ failed attempts at the same query
5. User requests verification from a human

### UX Flow
1. AI acknowledges limitation: "Let me connect you with a human agent who can help better."
2. Show handoff card displaying:
   - Agent availability: "Agent available now" or "Next agent available in 3 min"
   - Wait time estimate
   - Button to confirm handoff
3. Preserve full conversation context for human agent to read
4. Disable AI message controls (regenerate, edit) once handoff is initiated

### Visual Transition
- Conversation background subtly shifts: fade from --ai-filled-bg tint to --surface-default
- Status indicator changes: "AI Assistant" badge fades out, "Human Agent" badge fades in
- Message input text placeholder changes to "Type a message for the agent..."
- Timeline marker or divider shows "Conversation transferred to human agent"

### Post-Handoff Copilot
- AI can still suggest responses to the human agent in a separate collapsible panel
- Panel labeled "AI Suggestions for Agent"
- Show 2-3 suggested replies based on conversation context
- Agent can click to insert suggestion, edit, or dismiss

## Conversation Memory Indicators

### Active Memory (Current Conversation)
- Show subtle indicator: "AI remembers context from this chat"
- Appears as a small memory icon (--ai-filled-bg) in the header
- Tooltip: "I'm using conversation history to answer your questions"
- Remove badge if user explicitly clears memory

### Cross-Conversation Memory
- When AI recalls past information: "I remember you mentioned X in our last conversation on [date]"
- Display as in-message callout (border-left: 4px solid --ai-filled-bg)
- Include link: "View previous conversation" or "Clear this memory"
- Only show if user has enabled cross-session memory

### Memory Boundaries
- Clear visual separation between current conversation and recalled context
- Use --surface-raised for current messages, --surface-default for memory references
- Show timeline markers: "Previous conversation (3 days ago)" as a centered, muted label

### Privacy Controls
- Add "Memory Settings" button in chat header (gear icon)
- Modal or drawer shows:
  - "This conversation is being remembered" (toggle on/off)
  - List of specific facts the AI has learned about the user
  - Delete button per fact, "Clear all memories" button
  - Transparency note: "You can always see and delete what I remember about you"

## AI Suggestion Patterns

### Inline Suggestions (Ghost Text)
- Show ghosted next suggestion in text input field (--content-tertiary color, opacity 0.5)
- Modeled on GitHub Copilot behavior
- User accepts with Tab key, rejects by typing over it or pressing Escape
- Only show if user has typed at least 2 characters and paused for 1.5 seconds

### Suggestion Chips
- Display row of 3 clickable Chip components above the input area
- Chips use tonal variant (--primary-tonal-bg/fg)
- Each chip: "Do you mean: [suggestion]?" or "[suggested next question]"
- Chips appear staggered with --motion-fast fade-in
- Clicking a chip auto-fills the input and optionally auto-sends (respect user preference)

### Proactive Suggestions
- Slide-in panel from inline-end (right) edge using --motion-normal (250ms)
- Panel width: 320px, labeled "Suggested Next Steps"
- Max 3 suggestions, ordered by relevance/confidence
- Dismissible with X button; option to "Hide suggestions" for this conversation
- Panel should not overlap critical content; reserve space or slide in over white space

### Suggestion Confidence
- Higher-confidence suggestions appear first and with larger text/more prominent styling
- Low-confidence suggestions appear below, with smaller text and --content-tertiary color
- Include optional confidence label: "Likely:" vs "Maybe:" prefix
- Never show very low confidence suggestions unless user explicitly asks for more options

### Suggestion Timing
- Never interrupt user while actively typing
- Wait for 1.5 seconds of inactivity after the user stops typing
- If user moves focus away from input, hide suggestions immediately
- Reshow suggestions if user refocuses input after 2+ second pause

## AI Feedback Loop UX

### Rating (Every Response)
- Show thumbs up/down Icon Button pair below every AI response
- Buttons use --content-tertiary base color, --ai-filled-fg on hover/pressed
- Accessible labels: "Rate this response as helpful" and "Rate this response as unhelpful"
- Button state persists after click (filled/highlighted to show rating)

### Detailed Feedback (Optional)
- Clicking thumbs down expands an optional textarea below the buttons
- Placeholder: "Tell us what was wrong..."
- Submit button: "Send feedback"
- After submit, show checkmark animation (--motion-fast fade-in) and auto-dismiss after 2 seconds
- Feedback is never shown to user but logged for model improvement

### Regenerate Button
- Place "Regenerate" button next to the rating controls
- Icon: refresh/reload icon
- Clicking resubmits the last user message to AI and streams a new response
- Shows loading state while new response generates
- Clear old response when new one begins (or show both with "Previous response" collapsible)

### Edit and Resubmit
- Allow user to click "Edit" on their own messages to modify and resubmit
- Edited message shows edit badge: "[Edited]" in --content-tertiary
- Clicking "Edit" opens the message in the input field, allows modification
- User can submit the edited message to get a new AI response
- Preserve original message version (can be revealed in expansion)

### Visual Feedback After Rating
- Subtle confirmation animation: checkmark icon fades in (--motion-fast) next to the rating button
- Icon color: --success-filled-fg
- Icon persists for 2 seconds then fades out
- Provides tactile confirmation that feedback was registered

## Accessibility for AI Features

### Screen Reader Announcements
- "AI is thinking..." when typing dots appear
- "AI response received. [first 50 chars of response]." after response completes
- "Error: AI could not respond. Please try again." for errors
- "Human agent now available" when handoff completes
- Use aria-live="polite" for dynamic updates

### Focus Management
- After AI response completes, move focus to the response for keyboard users
- Announce "Response received" with aria-live so screen reader users know to navigate
- Allow Tab through response elements (links, buttons, copy icon)
- From response, Tab leads to regenerate/rate buttons, then back to input

### Reduced Motion
- All AI animations respect prefers-reduced-motion media query
- Substitute: dots animation becomes static three dots "..."
- Cursor blink becomes solid underscore "_"
- Fade transitions become instant visibility changes
- Pulse animations become opacity steady-state
- Progress spinners become static percentage text

### High Contrast
- Confidence indicators must be distinguishable by shape/icon, not color alone
- High confidence: no badge (default state)
- Medium confidence: icon (info circle) + text, both visible in high contrast
- Low confidence: warning triangle icon + text + border (not just color)
- All AI chips and buttons must have 4.5:1 contrast ratio minimum

## Component-Specific AI Behavior Rules

### AI Chat
- Follow all rules in ai-chat.md composed component spec
- Streaming responses must use character-by-character rendering
- Message list must preserve scroll position when loading older messages
- Input must not auto-send on Enter (Shift+Enter for newline)
- Floating widget must never obscure critical page content

### Command Palette
- AI-powered search ranks results by semantic relevance
- Show intent detection: "Did you mean to search for...?" if confidence >70%
- Highlight matched terms in results; show matching reason ("Matches description")
- Use Loading component skeleton for results during AI ranking

### Notification Center
- AI generates summaries of notification groups (max 2 sentences per summary)
- Summaries appear in --content-secondary color with "AI Summary" badge
- User can expand each summary to see individual notifications
- Grouped notifications are collapsible; groups ordered by AI priority ranking

### Search Interface
- AI-enhanced semantic search shows results ranked by relevance
- Suggested refinements appear below search box: "Refine: [suggestion]"
- Anomaly highlighting: mark unexpected results with warning icon + tooltip
- Search performance: AI ranking must not add >200ms latency

### Data Grid
- AI suggests new columns based on data patterns: "Add [column]?" suggestion chip
- Smart filtering: dropdown shows AI-suggested filter values (most common first)
- Anomaly highlighting: rows with statistical outliers show warning icon in row header
- All AI suggestions are opt-in; must not auto-apply

### Dashboard
- AI generates insight cards for key metrics: "Sales up 15% from last week"
- Trend descriptions: "Trending up for 4 weeks" with micro trend chart
- Anomaly alerts: "Unusual spike in support tickets" with suggested drill-down
- Insights refresh every 24 hours unless user manually triggers refresh

## Design Tokens for AI Features

Use these tokens specifically for AI-related visual treatments:

- **--ai-filled-bg**: AI accent background (e.g., AI badge, suggestion highlights)
- **--ai-filled-fg**: AI accent foreground text (buttons, icons on AI background)
- **--motion-fast**: Cursor blink cycle (150ms), quick transitions, focus feedback
- **--motion-normal**: Typing dots animation (250ms), suggestion slide-in, standard transitions
- **--motion-slow**: Extended thinking pulse (400ms), dramatic reveals
- **--content-tertiary**: Confidence caveats, timestamp text, secondary labels
- **--surface-raised**: Suggestion panels, memory cards, assistant message bubbles
- **--surface-default**: Chat background, fallback state backgrounds
- **--content-error**: Error messages (network, model errors)
- **--content-warning**: Unverified claims, hallucination indicators
- **--success-filled-fg**: Confirmation checkmarks after rating

All AI animations must respect motion tokens and prefers-reduced-motion. Never use hard-coded durations; always use token variables.
