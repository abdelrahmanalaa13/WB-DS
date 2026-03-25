# Inbox

Conversation list + detail split-view for messaging/support.

- Type: Product Feature Pattern
- Status: pending

## Components Used

List (L5), Panel (L2), AI Chat (L6), Badge (L1), Avatar (L1), Tabs (L4), Search (L6), Chip (L1), Icon Button (L1), Menu (L2), Loading (L5), Empty States (L6)

## Layout Structure

- Left panel (conversation list): search bar + filter tabs (All, Unread, Assigned to me, Starred) + scrollable conversation list
- Conversation item: avatar + contact name + last message preview + timestamp + unread badge + channel icon (WhatsApp, web, email)
- Right panel (conversation detail): header (contact info + actions) + AI Chat component + customer context sidebar (collapsible)
- Customer context: contact details card, conversation history, tags, notes, CRM link

## Interaction Flow

1. Click conversation in list to load in detail panel
2. Search/filter narrows conversation list
3. Type response in chat input, send
4. Assign conversation via header menu
5. Star/pin important conversations
6. Bulk select conversations for batch actions (assign, close, tag)
7. Real-time: new messages push to top of list, update preview

## Viewport

Desktop: two-panel split (list 360px + detail fills remaining).
Tablet: list panel collapsible, detail takes full width when conversation selected.
Mobile: list and detail are separate screens with back navigation.

## Layout Tokens

`--inbox-list-width: 360px`, `--inbox-list-min-width: 300px`, `--inbox-detail-max-message-width: 600px`

## Real-time Behavior

New messages: conversation moves to top of list with highlight flash. Typing indicators from other agents visible. Presence dots (online/offline/away) on conversation items. Sound notification for new messages (user preference).
