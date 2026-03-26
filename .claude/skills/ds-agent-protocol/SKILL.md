# DS Agent Protocol Mapper

**Trigger:** Map DS components to AG-UI protocol patterns. Use when the user says "map AG-UI", "update agent protocol", "add protocol schemas", "AG-UI mapping", or after adding new components that need agent protocol definitions.

**Purpose:** Systematically map components in ai-component-registry.json to AG-UI protocol capabilities. Read actual implementation details from ds-library.html and JS event delegation, then add/update agentProtocol schemas that describe what each component can do in an agent context.

---

## Core Responsibilities

### 1. Read Ground Truth

Read three files in order:

1. **ai-component-registry.json** - The source of truth for all components
2. **AG-UI_KNOWLEDGE.md** - Protocol reference (event types, patterns, message model)
3. **ai-interaction-contracts.json** - JS event delegation, handlers, state changes

### 2. Analyze Each Component

For each component in the registry, determine what AG-UI capabilities apply by examining:

**State Readability** (What can agents read?)
- From HTML/CSS classes in ds-library.html: `aria-expanded`, `aria-disabled`, `aria-selected`, `data-state`, `data-variant`, `data-size`
- From component properties: `disabled`, `checked`, `isOpen`, `expanded`, `variant`, `color`, `size`
- Example: `ds-accordion` emits `aria-expanded="true|false"` → readable state `expanded: boolean`

**Actions** (What can agents do?)
- From interaction contracts: click handlers, JS method calls, property setters
- Standard actions: `click`, `toggle`, `open`, `close`, `select`, `clear`, `submit`, `setDisabled`, `setValue`, `setVariant`
- Example: `ds-button` accepts `.click()` → action `click` exists
- Example: `ds-dialog` has `.close()` method → action `close` exists

**Events Emitted** (What does the component announce?)
- From JS event delegation: `addEventListener`, custom events, mutation observer callbacks
- Example: `ds-button` fires custom `button-clicked` → emits `clicked`
- Example: `ds-dialog` fires `dialog-closed` → emits `closed`

**Generative UI Capability** (Can agents propose this component?)
- Nearly all DS components can be proposed via GenerativeUI
- Exception: Pure read-only display components (badge, divider, avatar without interaction)

### 3. Map to AG-UI Event Types

For each component, determine which AG-UI event categories apply:

**TextMessage Events** - Components that produce text output
- Chat: `TextMessageStart`, `TextMessageContent`, `TextMessageEnd`
- Toast, Snackbar, Alert: Text message + auto-dismiss
- Search results: TextMessage for results text
- Implementation rule: Only if component's primary purpose is displaying/streaming text

**ToolCall Events** - Components for triggering agent tools
- Data-grid with action buttons: `ToolCallStart`, `ToolCallArgs`, `ToolCallEnd`
- Command-palette selections: `ToolCallStart` (command selection as tool invocation)
- Button with data payload: `ToolCallStart` with args from component state
- Implementation rule: Only if component action carries structured data (JSON args)

**State Events** - Components with discrete, readable state changes
- Dialog open/close: `StateSnapshot` { isOpen: true/false }
- Accordion expand/collapse: `StateDelta` for aria-expanded
- Tabs switching: `StateSnapshot` { activeTab: "tab-id" }
- Select/dropdown value change: `StateSnapshot` { selectedValue: "..." }
- Implementation rule: Map state changes that require agent awareness to participate

**Activity Events** - Progress/status components
- Spinner/Loader: `ActivitySnapshot` { activityType: "LOADING", content: {} }
- Progress bar: `ActivitySnapshot` with progress percentage
- Timeline steps: `ActivitySnapshot` { activityType: "STEP_PROGRESS" }
- Skeleton: `ActivitySnapshot` { activityType: "SKELETON" }
- Implementation rule: Only if component indicates ongoing work, not final state

**Reasoning Events** - Components exposing agent thinking
- Reasoning trace: `ReasoningStart` → `ReasoningMessageContent` → `ReasoningEnd`
- Implementation rule: Only for dedicated reasoning/trace components

### 4. Define Interrupt Policies

Interrupt policies determine if a component action pauses agent execution.

**Always Interrupt** - Data-modifying actions
- Form submission
- Data-grid row deletion
- Dialog confirmation (user choice required)
- File upload completion
- Implementation rule: Actions that change backend state always interrupt

**Never Interrupt** - Read-only or display actions
- Button clicks that only read data (navigation, filtering)
- Accordion expansion (visual only)
- Tab switching (view selection)
- Checkbox read without form submission
- Implementation rule: Pure reads and view changes never interrupt

**Conditional** - GenerativeUI component creation
- If proposing new component instance: interrupt (user sees new UI, may want to cancel)
- If updating existing component: never interrupt (user already authorized this component)
- Implementation rule: Check if component exists in DOM before setting interrupt

### 5. Define Rejection Handling

When an agent action fails on a component, define fallback behavior.

**onRejection** - What to do if component action fails
- Options: `searchRegistry`, `fallbackComponent`, `reportToGovernance`, `silent`
- Rule: Always report, then fall back to closest alternative

**fallbackComponent** - Closest alternative for this component
- `ds-dialog` → `ds-popover` (same containment, smaller)
- `ds-data-grid` → `ds-list` (same data display, less features)
- `ds-command-palette` → `ds-menu` (same interaction, less powered)
- `ds-toast` → `ds-snackbar` → `ds-alert` (notification fallback chain)

**reportToGovernance** - Always true. Rejection events inform product team of agent capability gaps.

### 6. Write Back Registry

Update ai-component-registry.json with complete agentProtocol for each component:

```json
{
  "name": "component-name",
  "agentProtocol": {
    "readable": {
      "propertyName": "javascript type",
      "anotherProperty": "another type"
    },
    "actions": [
      "action1",
      "action2"
    ],
    "events": {
      "emits": [
        "eventName"
      ],
      "listens": [
        "externalEventName"
      ]
    },
    "eventMapping": {
      "eventName": {
        "category": "StateEvent | TextMessageEvent | ToolCallEvent | ActivityEvent | ReasoningEvent",
        "agUiType": "StateSnapshot | TextMessageChunk | ToolCallStart | ActivitySnapshot | ReasoningStart",
        "payload": { }
      }
    },
    "generativeUI": {
      "canBeProposed": true,
      "requiredData": ["label", "variant"],
      "outputSchema": {
        "clicked": "boolean"
      }
    },
    "interruptPolicy": {
      "alwaysInterrupt": [
        "actionThatRequiresApproval"
      ],
      "neverInterrupt": [
        "readOnlyAction"
      ],
      "conditional": [
        {
          "action": "create",
          "condition": "isNewInstance",
          "interrupt": true
        }
      ]
    },
    "rejectionHandling": {
      "onRejection": "searchRegistry | fallbackComponent | reportToGovernance",
      "fallbackComponent": "component-name-or-null",
      "fallbackChain": [
        "firstAlternative",
        "secondAlternative",
        "thirdAlternative"
      ],
      "reportToGovernance": true
    }
  }
}
```

---

## Execution Steps

1. **Load files**
   - Read ai-component-registry.json completely
   - Read AG-UI_KNOWLEDGE.md completely
   - Read ai-interaction-contracts.json completely

2. **Iterate components**
   - For each component in registry:
     - Determine readable state from implementation
     - List all possible actions
     - Map emitted/listened events
     - Assign AG-UI event categories
     - Set interrupt policies
     - Define rejection fallbacks

3. **Validate**
   - Every `agentProtocol.readable` property must be observable in ds-library.html
   - Every `agentProtocol.actions` item must be callable on the component
   - Every `agentProtocol.events.emits` event must be fired by JS handlers
   - No aspirational features: only what's truly implemented
   - All `eventMapping` entries must reference real AG-UI event types from AG-UI_KNOWLEDGE.md

4. **Write**
   - Preserve all existing fields in registry (don't strip properties)
   - Update/create agentProtocol fields only
   - Keep JSON valid and pretty-printed
   - Write back to ai-component-registry.json

5. **Report**
   - List all components processed
   - Count how many were updated vs. already complete
   - Highlight any components missing critical AG-UI mappings
   - Note any components that cannot map to AG-UI (read-only display with no state)

---

## Key Rules (Non-Negotiable)

- **Determinism:** Same input registry produces same output every run
- **No Aspiration:** Only map capabilities present in ds-library.html JS and CSS
- **Event Truth:** Events must actually fire; don't invent events
- **Type Accuracy:** readable state types must match JS types (boolean, string, number, object, array)
- **Interrupt Safety:** Data mutations always interrupt; reads never do
- **Fallback Validity:** Fallback components must exist in registry and be more general than the primary
- **Governance Transparency:** Every rejection is reported for product review

---

## Component-Specific Patterns (Reference)

Use these patterns for common component types:

### Text Output Components
- **Purpose:** Render text from agent
- **Events:** TextMessageStart/Content/End
- **Readable:** None (output only)
- **Actions:** clear, dismiss
- **Examples:** toast, snackbar, alert, chat-message
- **Interrupt:** Never (display only)

### Interactive Selection Components
- **Purpose:** User/agent selects from options
- **Events:** StateSnapshot (selectedValue changes)
- **Readable:** selectedValue, isOpen, disabled
- **Actions:** select, open, close, clear
- **Examples:** dropdown, select, tabs, radio, checkbox
- **Interrupt:** Never (selection is read)

### Data Mutation Components
- **Purpose:** User/agent modifies data
- **Events:** StateSnapshot (form state), then ToolCallStart (submit)
- **Readable:** formState (all field values), isValid, isDirty
- **Actions:** setValue, submit, reset, clear
- **Examples:** form-field, file-upload, data-grid with edit
- **Interrupt:** Always (mutation requires approval)

### Container/Layout Components
- **Purpose:** Organize content
- **Events:** StateSnapshot (open/closed, expanded/collapsed)
- **Readable:** isOpen, isExpanded
- **Actions:** open, close, toggle
- **Examples:** dialog, accordion, drawer, popover, menu
- **Interrupt:** Never (containment only, agent controls content inside)

### Progress/Activity Components
- **Purpose:** Show ongoing work
- **Events:** ActivitySnapshot, ActivityDelta (progress updates)
- **Readable:** progress, status, activityType
- **Actions:** start, cancel, reset
- **Examples:** spinner, progress-bar, skeleton, timeline
- **Interrupt:** Never (progress is informational)

### Navigation Components
- **Purpose:** Move through pages/sections
- **Events:** StateSnapshot (currentStep, currentTab)
- **Readable:** currentStep, currentTab, totalSteps
- **Actions:** goToStep, selectTab, next, previous
- **Examples:** breadcrumb, pagination, stepper, tabs
- **Interrupt:** Never (navigation is view-only)

---

## Example Output

For `ds-button`:
```json
{
  "name": "ds-button",
  "agentProtocol": {
    "readable": {
      "disabled": "boolean",
      "variant": "string",
      "role": "string"
    },
    "actions": [
      "click",
      "setDisabled"
    ],
    "events": {
      "emits": [
        "clicked"
      ],
      "listens": []
    },
    "eventMapping": {
      "clicked": {
        "category": "ToolCallEvent",
        "agUiType": "ToolCallStart",
        "payload": {
          "toolCallId": "uuid",
          "toolCallName": "buttonAction"
        }
      }
    },
    "generativeUI": {
      "canBeProposed": true,
      "requiredData": [
        "label"
      ],
      "outputSchema": {
        "clicked": "boolean"
      }
    },
    "interruptPolicy": {
      "alwaysInterrupt": [
        "click"
      ],
      "neverInterrupt": [],
      "conditional": []
    },
    "rejectionHandling": {
      "onRejection": "fallbackComponent",
      "fallbackComponent": "ds-icon-button",
      "fallbackChain": [
        "ds-icon-button",
        "ds-text-button",
        "searchRegistry"
      ],
      "reportToGovernance": true
    }
  }
}
```

For `ds-accordion`:
```json
{
  "name": "ds-accordion",
  "agentProtocol": {
    "readable": {
      "expanded": "boolean",
      "multiExpand": "boolean",
      "items": "array"
    },
    "actions": [
      "toggle",
      "open",
      "close"
    ],
    "events": {
      "emits": [
        "itemToggled",
        "itemOpened",
        "itemClosed"
      ],
      "listens": []
    },
    "eventMapping": {
      "itemToggled": {
        "category": "StateEvent",
        "agUiType": "StateDelta",
        "payload": {
          "patch": [
            {
              "op": "replace",
              "path": "/items/0/expanded",
              "value": true
            }
          ]
        }
      }
    },
    "generativeUI": {
      "canBeProposed": true,
      "requiredData": [
        "items"
      ],
      "outputSchema": {
        "expandedItems": "array"
      }
    },
    "interruptPolicy": {
      "alwaysInterrupt": [],
      "neverInterrupt": [
        "toggle",
        "open",
        "close"
      ],
      "conditional": []
    },
    "rejectionHandling": {
      "onRejection": "fallbackComponent",
      "fallbackComponent": null,
      "fallbackChain": [
        "searchRegistry"
      ],
      "reportToGovernance": true
    }
  }
}
```

---

## Notes

- **Determinism is critical:** This skill must produce identical output for identical input. Version the agentProtocol schema if you ever change the rules.
- **Implement first, then map:** Never add AG-UI patterns for unimplemented features. If a component can't do something, it doesn't belong in agentProtocol.
- **Event types come from AG-UI_KNOWLEDGE.md:** Don't invent event types. Stick to the 25 defined types.
- **Update, don't replace:** Preserve all other registry fields. Only add/update agentProtocol.
- **Report completeness:** After execution, tell the user which components got updated and which were skipped.
