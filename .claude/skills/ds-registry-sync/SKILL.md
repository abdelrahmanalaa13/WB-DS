---
name: ds-registry-sync
description: Regenerate all architecture JSON files from ds-library.html. Use when the user says 'sync registry', 'rebuild JSONs', 'regenerate architecture', 'update registry from code', or after making changes to ds-library.html that need to be reflected in the architecture files. This skill ensures the architecture registry is always in sync with the canonical source of truth — the design system's ds-library.html file.
---

# DS Registry Sync

**Purpose:** Generate deterministic architecture JSON files from ds-library.html. Same input produces same output every time.

**Output:** Eight JSON files written to the same directory as ds-library.html:
- `ai-component-registry.json`
- `ai-knowledge-graph.json`
- `ai-motion-system.json`
- `ai-responsive-system.json`
- `ai-interaction-contracts.json`
- `ai-mobile-touch-model.json`
- `ai-governance-lifecycle.json`
- `ai-generative-ui-registry.json`

---

## How to Use This Skill

When the user mentions syncing the registry, rebuilding JSON files, regenerating the architecture, or updating the registry after changes to ds-library.html, follow these steps.

### 1. Locate ds-library.html

Find the ds-library.html file (it's approximately 9,400+ lines). The file contains three main sections:
- CSS section (tokens, components, @media, @container, @keyframes)
- JS section (IIFE with render functions, interaction contracts, component configs)
- HTML section (markup structure)

### 2. Read and Parse the File

Read the file in chunks if needed due to size. As you read:

**CSS Parsing:**
- Extract all CSS custom properties from `:root` selector (color, spacing, typography, radius, shadow, motion, easing, z-index, breakpoint tokens)
- Extract all component definitions with class prefix `ds-` (e.g., `.ds-button`, `.ds-card`)
- For each component class, find:
  - Child class references (anatomy)
  - CSS custom property references via `var()` (token mappings)
  - Modifier classes (variants)
  - Pseudo-classes (`:hover`, `:focus`, `:active`, `:disabled`) and state classes (`.is-*`, `[data-state="*"]`)
  - Media feature queries (`@media (min-width:)`, `@container`, `@media (hover:)`, `@media (pointer:)`)
  - Keyframe animations and transition properties
  - Data attributes (size, responsiveness rules)

**JS Parsing:**
- Extract the IIFE block (immediately invoked function expression)
- Parse render function configs for each component (name, purpose, exposed API)
- Extract event listener registrations: selector, event type, handler logic
- Extract state change logic and ARIA attribute updates per event
- Identify keyboard support and touch alternatives per interaction
- Extract the `window.DS_INTERACTIONS` or similar exposed API

**HTML Parsing:**
- Extract slot definitions (named content areas in component markup)
- Extract data attributes and their semantics

### 3. Build In-Memory Data Structures

Organize the extracted data as you go:

```
components = {
  "component-name": {
    "id": "ds-component-name",
    "name": "Component Name",
    "purpose": "...",
    "anatomy": { "childName": "className" },
    "properties": { "--token-name": "value or null" },
    "slots": ["slot1", "slot2"],
    "tokenMappings": { "var(--token)": "path/to/token" },
    "variants": { "modifier-class": ["value1", "value2"] },
    "states": { "state-name": { "trigger": "...", "styles": {...} } },
    "sizes": ["sm", "md", "lg"],
    "responsiveBehavior": { "mobile": "...", "tablet": "...", "desktop": "..." },
    "accessibilityRequirements": { "role": "...", "aria-*": "..." },
    "motionBehavior": { "animation": "...", "transition": "..." },
    "interactionRules": [...]
  }
}

tokens = {
  "category": {
    "token-name": { "value": "...", "mode": "light|dark|all", "used-by": ["component-a", "component-b"] }
  }
}

interactions = {
  "selector": {
    "events": ["click", "change"],
    "triggers": { "event-type": { "stateChanges": [...], "ariaUpdates": [...], "feedback": "..." } }
  }
}

motions = {
  "@keyframes animationName": { "steps": [...] },
  "transitions": { "property": "duration easing-function" }
}

responsive = {
  "@media (min-width: 640px)": { "rules": {...} },
  "@container ...": { "rules": {...} }
}
```

### 4. Generate ai-component-registry.json

Extract all components found via `ds-` CSS class prefix and build the registry with these rules:

**For each component:**
- `id`: CSS class name (e.g., `"ds-button"`)
- `name`: Human-readable component name
- `purpose`: From component config or comment (if not found, set to `null`)
- `anatomy`: Map of child element names to their CSS classes
- `properties`: All CSS custom properties used by this component (name and value)
- `slots`: Named content areas extracted from render function
- `tokenMappings`: Every `var(--token-name)` reference mapped to the actual token path
- `variants`: All modifier classes and their possible values
- `states`: Pseudo-classes (`:hover`, `:focus`, `:disabled`) and state classes
- `sizes`: Any size variants (sm, md, lg, etc.)
- `responsiveBehavior`: How the component responds at different breakpoints
- `accessibilityRequirements`: ARIA attributes, roles, keyboard support
- `motionBehavior`: Animations and transitions
- `interactionRules`: User interactions, event handlers, visual feedback
- `mobileTouchAlternatives`: Touch-optimized behaviors from `@media (hover: none)` or `@media (pointer: coarse)`
- `aiHints`: Optional computed field with component usage guidance

**AG-UI agentProtocol schema (for each component):**
- `readableState`: Properties Claude can read (current value, disabled, focused, etc.)
- `actions`: Methods/events Claude can trigger (click, setValue, open, etc.)
- `events`: Events the component emits (onChange, onSubmit, onError, etc.)
- `generativeUI`: Can this component be auto-populated by Claude? (true/false, with reasoning)

**Governance fields (root level):**
- `statePolicy`: How state is managed (internal, external, two-way-binding)
- `reasoningPolicy`: Explain how Claude should reason about component behavior
- `interruptPolicy`: Can Claude interrupt ongoing interactions?
- `rejectionProtocol`: How to handle invalid states

**Important: Prove every field from the code.**
- If a component has no animation, set `motionBehavior` to `null`.
- If accessibility is not defined, set to `null`.
- Do not guess or add aspirational entries. Empty means `null`, not a placeholder.

### 5. Generate ai-knowledge-graph.json

Build a graph of token → component relationships:

```json
{
  "tokens": {
    "color": {
      "primary": { "value": "#0066cc", "mode": "light", "references": ["ds-button", "ds-link"] },
      "primary-dark": { "value": "#004499", "mode": "dark", "references": ["ds-button", "ds-link"] }
    },
    "spacing": {
      "md": { "value": "16px", "mode": "all", "references": ["ds-button", "ds-card"] }
    },
    "typography": {
      "body-sm": { "value": "font-family: Inter; font-size: 14px;", "mode": "all", "references": ["ds-caption"] }
    },
    "radius": { ... },
    "shadow": { ... },
    "motion": { ... },
    "easing": { ... },
    "z-index": { ... },
    "breakpoint": { ... }
  },
  "edges": [
    { "source": "token:color:primary", "target": "component:ds-button", "property": "background-color", "usage": "primary variant" },
    { "source": "token:spacing:md", "target": "component:ds-button", "property": "padding", "usage": "default size" }
  ]
}
```

Extract every `var(--token-name)` reference in component CSS and create an edge. Track which components use which tokens for dependency analysis.

### 6. Generate ai-motion-system.json

Extract all animations and transitions:

```json
{
  "keyframes": {
    "fadeIn": {
      "steps": [
        { "keyframe": "0%", "properties": { "opacity": "0" } },
        { "keyframe": "100%", "properties": { "opacity": "1" } }
      ]
    }
  },
  "transitions": {
    "background-color": "200ms cubic-bezier(0.4, 0, 0.2, 1)",
    "opacity": "150ms ease-in"
  },
  "motionTokens": {
    "duration-fast": "150ms",
    "duration-normal": "200ms",
    "easing-standard": "cubic-bezier(0.4, 0, 0.2, 1)"
  },
  "prefersReducedMotion": {
    "affected-components": ["ds-modal", "ds-tooltip"],
    "rules": "all animations and transitions set to 0ms when @media (prefers-reduced-motion: reduce)"
  }
}
```

### 7. Generate ai-responsive-system.json

Extract all media and container queries:

```json
{
  "breakpoints": {
    "mobile": { "min-width": "0px", "max-width": "639px" },
    "tablet": { "min-width": "640px", "max-width": "1023px" },
    "desktop": { "min-width": "1024px" }
  },
  "mediaQueries": [
    {
      "query": "@media (min-width: 640px)",
      "breakpoint": "tablet",
      "rules": { "component": "ds-grid", "property": "grid-template-columns", "value": "repeat(2, 1fr)" }
    }
  ],
  "containerQueries": [
    {
      "query": "@container (min-width: 300px)",
      "rules": { ... }
    }
  ],
  "viewportSimulation": [
    {
      "selector": "[data-viewport='mobile']",
      "width": "375px",
      "height": "667px",
      "rules": { ... }
    }
  ],
  "touchOptimization": {
    "rules": "apply from @media (hover: none) and @media (pointer: coarse)",
    "examples": [
      { "component": "ds-button", "minTouchSize": "44px", "padding": "12px" }
    ]
  }
}
```

### 8. Generate ai-interaction-contracts.json

Extract all JS event listeners and state management:

```json
{
  "interactions": {
    "ds-button": [
      {
        "selector": ".ds-button",
        "event": "click",
        "trigger": "User clicks the button",
        "stateChanges": ["disabled = true", "aria-busy = true"],
        "visualFeedback": ["scale transform", "opacity change"],
        "keyboardSupport": "Space, Enter keys also trigger",
        "ariaUpdates": { "aria-pressed": "toggle on click" },
        "analytics": "Button click tracked with component ID and variant"
      }
    ]
  },
  "exposedAPI": {
    "window.DS_INTERACTIONS": {
      "methods": ["registerHandler", "destroy"],
      "events": ["custom:state-change", "custom:error"],
      "documentation": "..."
    }
  }
}
```

### 9. Generate ai-mobile-touch-model.json

Extract touch and mobile-specific behavior:

```json
{
  "touchTargetSizes": {
    "minimum": "44px",
    "components": [
      { "component": "ds-button", "size": "44px", "padding": "12px 16px" },
      { "component": "ds-icon-button", "size": "48px", "padding": "12px" }
    ]
  },
  "hoverAlternatives": [
    {
      "desktop": "tooltip on :hover",
      "mobile": "tap to open bottom-sheet",
      "affected-components": ["ds-tooltip"]
    }
  ],
  "gestureSupport": [
    {
      "gesture": "long-press",
      "action": "context menu",
      "components": ["ds-menu-trigger"]
    }
  ],
  "bottomSheetComponents": [
    {
      "component": "ds-mobile-menu",
      "triggers": ["on tap", "at viewport mobile"],
      "behavior": "slide-up from bottom"
    }
  ],
  "mediaQueries": {
    "@media (hover: none)": "disable hover effects",
    "@media (pointer: coarse)": "increase touch targets to 44px minimum"
  }
}
```

### 10. Write All JSON Files

Write the six JSON files to the same directory as ds-library.html. Each file must:
- Be valid JSON (no trailing commas, proper escaping)
- Use 2-space indentation for readability
- Have no null values unless the field is legitimately absent (not a placeholder)
- Be parseable by `JSON.parse()`

### 11. Validate

For each JSON file, parse it as JSON to confirm syntax is valid:

```bash
jq . ai-component-registry.json
jq . ai-knowledge-graph.json
jq . ai-motion-system.json
jq . ai-responsive-system.json
jq . ai-interaction-contracts.json
jq . ai-mobile-touch-model.json
```

If any file fails to parse, report the error and do not continue.

### 12. Report Results

After all files are written and validated, provide a summary:

```
✓ Registry sync complete
- ai-component-registry.json: [N] components
- ai-knowledge-graph.json: [N] tokens, [N] edges
- ai-motion-system.json: [N] animations, [N] transitions
- ai-responsive-system.json: [N] breakpoints, [N] media queries
- ai-interaction-contracts.json: [N] interaction contracts
- ai-mobile-touch-model.json: [N] touch targets, [N] gesture rules

All files validated and written to: [directory]
```

---

## Key Principles

1. **Deterministic:** Running this skill twice on the same ds-library.html produces identical JSON output (byte-for-byte).

2. **Prove every field:** Only include data that is explicitly present in the code. No aspirational entries, no placeholders. Use `null` for absent fields.

3. **Single source of truth:** All data comes from ds-library.html. Do not reference external files or make assumptions.

4. **Completeness:** Extract ALL components, tokens, animations, queries, and interactions. Partial extraction risks losing information.

5. **No duplication:** Each piece of data appears in one place. Cross-references use edges, not copies.

---

## Example: Button Component

If ds-library.html contains:

```css
.ds-button {
  padding: var(--spacing-md);
  background-color: var(--color-primary);
  transition: background-color 200ms var(--easing-standard);
  min-width: 44px;
  min-height: 44px;
}

.ds-button:hover {
  background-color: var(--color-primary-hover);
}

.ds-button.is-disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.ds-button--small {
  padding: var(--spacing-sm);
}
```

And in JS:

```javascript
const buttonConfig = {
  name: "Button",
  purpose: "Trigger an action or submit a form",
  render: (props) => `<button class="ds-button" ...>${props.label}</button>`
};

document.addEventListener('click', (e) => {
  if (e.target.closest('.ds-button')) {
    const btn = e.target.closest('.ds-button');
    btn.setAttribute('aria-busy', 'true');
  }
});
```

Then in `ai-component-registry.json`:

```json
{
  "id": "ds-button",
  "name": "Button",
  "purpose": "Trigger an action or submit a form",
  "anatomy": null,
  "properties": {
    "--spacing-md": "16px",
    "--color-primary": "#0066cc",
    "--easing-standard": "cubic-bezier(0.4, 0, 0.2, 1)"
  },
  "slots": ["label"],
  "tokenMappings": {
    "var(--spacing-md)": "spacing/md",
    "var(--color-primary)": "color/primary",
    "var(--easing-standard)": "easing/standard"
  },
  "variants": {
    "--small": ["small"]
  },
  "states": {
    "hover": { "trigger": ":hover", "styles": { "background-color": "var(--color-primary-hover)" } },
    "disabled": { "trigger": ".is-disabled", "styles": { "opacity": "0.5", "cursor": "not-allowed" } }
  },
  "sizes": ["small", "default"],
  "responsiveBehavior": { "mobile": "min-height 44px enforced", "tablet": "standard", "desktop": "standard" },
  "accessibilityRequirements": { "role": "button", "aria-busy": "updated on interaction" },
  "motionBehavior": { "transition": "background-color 200ms cubic-bezier(0.4, 0, 0.2, 1)" },
  "interactionRules": [
    {
      "event": "click",
      "stateChanges": ["aria-busy: true"],
      "feedback": "visual",
      "keyboard": "Space/Enter support expected"
    }
  ],
  "mobileTouchAlternatives": { "minTouchSize": "44px" },
  "aiHints": "Primary action component. Safe to use in generated UI."
}
```

---

## Troubleshooting

- **File not found:** Verify the path to ds-library.html and that it exists in the working directory or mounted path.
- **Incomplete extraction:** If certain components or tokens are missing, check that they have the `ds-` prefix or are in the `:root` selector.
- **JSON parse error:** Validate syntax with `jq` or an online JSON validator. Common issues: trailing commas, unescaped quotes in strings, improperly closed objects.
- **Non-deterministic output:** If running twice produces different results, check for floating timestamps, random IDs, or unsorted keys. All keys should be sorted alphabetically for consistency.
