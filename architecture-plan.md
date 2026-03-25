# AI-Native Design System Architecture v2

## What Changed and Why

The v1 plan had six structural problems:

1. The knowledge graph was a static JSON file, not a queryable system that enables reasoning
2. Markdown files were treated as the primary AI interface instead of the Component Registry
3. The pipeline was a single linear flow that could not handle evolve/fix/audit/add-brand workflows
4. Six separate schemas with no unified contract or relationship validation
5. Agent orchestration was a table listing with no coordination protocol
6. No practical execution path showing how to build incrementally

This v2 plan redesigns around three principles:
- The Knowledge Graph is the brain (every agent queries it before acting)
- The Component Registry is the primary interface for generative agents
- Multiple entry points converge on shared validation gates

---

## Architecture: 7 Layers

### Layer 1: Knowledge Graph (The Brain)

Not a static file. A structured data store that agents traverse to reason about the design system.

**Entities (nodes):** tokens, components, patterns, templates, interactions, motions, compliance-rules, accessibility-rules. Each has a unique ID, version, and type.

**Relationships (edges):** uses-token, participates-in-pattern, composes-into, requires-rule, conflicts-with, replaces, depends-on. Each edge carries metadata: why it exists, when it was created, confidence level.

**Query examples an agent can ask:**
- "What components use token `color-primary`?" (impact analysis before token change)
- "What patterns does `data-table` participate in?" (understand blast radius)
- "Does a component with this purpose already exist?" (prevent duplication)
- "What WCAG level does WideBot require for interactive elements?" (accessibility check)
- "If I deprecate `chip`, what patterns break?" (safe deprecation)

**Implementation:** Three JSON files in `graph/`:
- `nodes.json` -- all entity references with IDs, types, versions
- `edges.json` -- all relationships with metadata
- `changelog.json` -- every mutation with timestamp, reason, and agent attribution

The graph is rebuilt from the underlying registry/token/pattern files at validation time. The JSON files are the index, not the only storage. The actual data lives in the foundation files (tokens/*.json, registry/*.json, etc.).

### Layer 2: Contract Layer (JSON Schemas)

Nothing enters the system without schema validation.

**Unified Entity Schema:** Instead of 6 separate schemas, one polymorphic schema with a discriminator field (`entityType`). All entities share a base contract (id, version, status, name, description). Type-specific extensions handle component-specific fields, token-specific fields, etc.

**Relationship Schema:** Defines which edge types are valid between which entity types. Prevents nonsensical connections (a token cannot "compose-into" another token). Enforces referential integrity (a pattern cannot reference a component that does not exist).

**Governance Schema:** Lifecycle states (proposal -> specification -> approved -> implemented -> documented -> released -> deprecated). Transition rules: cannot skip from proposal to released. Cannot reach "implemented" without "approved" first. Requires human approval at the "approved" gate.

**Brand Config Schema:** Per-brand overrides are data, not code. Defines WCAG level, theme modes, font stack, icon mirroring rules, RTL exceptions. Adding a brand means creating a new config file, not changing code.

### Layer 3: Foundation (Source of Truth)

All data is Git-versioned JSON. Every file validates against the contract layer.

**Token Store:**
- `global.tokens.json` -- spacing, elevation, z-index, breakpoints (shared across brands)
- `semantic.tokens.json` -- intent aliases: success, error, warning, info (resolving to brand tokens)
- `motion.tokens.json` -- duration, easing, distance, delay, opacity, scale
- `brands/widebot.tokens.json` -- WideBot light + dark color values with WCAG AAA contrast data
- `brands/hulul.tokens.json` -- Hulul light + dark with WCAG AA contrast data
- `brands/widebot.config.json` -- Brand rules: a11y level, RTL, font, icon set, locale
- `brands/hulul.config.json` -- Brand rules for Hulul

**Component Registry (PRIMARY AI INTERFACE):**
One JSON file per component. Each entry contains:
- name, selector, category, purpose, status, version
- anatomy (HTML structure + named slots with accepted types)
- properties (Angular signal inputs/outputs/models with types and defaults)
- states (default, hover, focus, active, disabled, loading, error, success, selected)
- variants (with controlled naming, no explosion)
- sizes (xs through xl)
- tokenMappings (maps every component part + state to a design token)
- responsive (mobile behavior, touch target, desktop behavior)
- accessibility (role, ARIA, keyboard interaction, per-brand WCAG level, screen reader notes, reduced motion)
- motion (enter, exit, hover animations referencing motion tokens)
- rtl (mirror layout, icon flips, exceptions)
- interactionContract (reference to interaction file)
- cdkDependencies (Angular CDK modules)
- aiHints (when to use, when not to use, common combinations, contextual rules)
- relatedComponents + usedInPatterns (graph relationships)

This registry is what generative agents read to understand how to compose UI.

**Rules Engine:**
Rules are machine-evaluatable JSON, not prose documentation.
- `accessibility.rules.json` -- per-brand WCAG requirements, contrast ratios, keyboard navigation rules, ARIA requirements
- `responsive.rules.json` -- breakpoints, density limits, container constraints
- `rtl.rules.json` -- mirroring rules, icon flip list, CSS logical property requirements, exceptions
- `governance.rules.json` -- naming conventions, duplication detection rules, lifecycle enforcement
- `touch.rules.json` -- mobile touch model: hover becomes tap-reveal, tooltip becomes bottom-sheet, dropdown becomes full-screen selector, context-menu becomes long-press

### Layer 4: Agent Orchestration

Agents coordinate via a shared context bus with explicit permissions.

**ds-copilot (Orchestrator):**
- Routes incoming requests to the right agents
- Queries knowledge graph to decide which agents are needed
- Resolves conflicts when agents disagree
- Only agent that can approve graph changes
- Maintains the audit log

**frontend-copilot:**
- Reads registry + patterns
- Generates Angular 21 code (standalone, signals, OnPush)
- Validates generated code against token mappings
- Can PROPOSE new component specs; cannot approve them
- Cannot modify tokens directly

**product-compliance-copilot:**
- Validates enterprise/government compliance requirements
- Can BLOCK a component from advancing past "specification" in lifecycle
- Reads brand config to determine compliance level

**ux-research-copilot:**
- Validates usability of patterns and templates
- Proposes improvements based on research insights
- Cannot modify components directly

**ai-agent-copilot:**
- Defines AI interaction UX patterns (streaming, tool results, conversation flow)
- Manages AI-specific component registry entries
- Defines behavior contracts for AI interfaces

**Conflict Resolution Protocol:**
When agents disagree (e.g., compliance says "add disclosure" but UX says "keep it clean"):
1. ds-copilot collects both positions with reasoning
2. Knowledge graph is queried for precedent
3. If unresolvable by precedent, human decides
4. Decision is recorded in governance/approval-history.json

### Layer 5: Generation (Multiple Entry Points)

Not one pipeline. Six entry points that all converge on the same validation gate.

**Entry A: New Component**
Proposal -> graph.checkOverlap() -> schema.validate() -> registry entry created -> ui-feature-builder generates MDs -> preview -> human approval -> Figma sync + code gen

**Entry B: Lovable Prototype (Optional)**
DS tokens exported as CSS vars + guidelines -> Lovable generates React prototype -> Claude extracts layout intent and discards raw values -> maps to registry components -> creates/updates registry entries -> ui-feature-builder -> preview -> approval -> Figma sync + code gen

**Entry C: Evolve Existing**
Change request -> graph.impactAnalysis() -> blast radius report -> modify registry/tokens -> re-generate affected MDs -> gap analysis against Figma current state -> preview -> approval

**Entry D: Fix / Audit**
Accessibility audit finding or compliance gap -> rules engine identifies violations -> auto-propose fixes (token changes, ARIA additions, contrast adjustments) -> update registry/tokens -> re-validate all affected -> preview -> approval

**Entry E: Add Brand**
New brand config created -> inherits from base token structure -> overrides specific values -> knowledge graph extends with new brand node -> all components gain new token variants -> bulk preview for QA -> approval

**Entry F: Learning Signal**
Usage data, UX research, or engineering feedback -> evaluate whether it requires a new component, a token change, or a pattern update -> knowledge graph traversal determines blast radius -> consistency safeguards applied -> propose changes -> human approval required

### Layer 6: Output (Multiple Targets, Single Source)

Same registry data produces all outputs. Change once, propagate everywhere.

**Feature MDs (ui-feature-builder):** Generated from registry + tokens. Per-brand token tables, component anatomy, design principles. These are the input for preview and Figma sync skills.

**JSX Preview (ui-feature-previewer):** Interactive artifact with brand/theme/direction switching. Token inspector. Gap analysis overlay. This is the human QA gate.

**Figma Sync (figma-md-sync):** Variables + component frames. LTR+RTL, Light+Dark. Arabic text in RTL variants. Auto-layout. Figma is the visual record and designer handoff, not the source of truth.

**Angular 21 Code:** Standalone components. Signal-based inputs/outputs. OnPush change detection. CDK integration. SCSS with CSS custom properties. Brand-switchable via `[data-brand]` attribute.

**4-Audience Documentation:** Auto-generated from registry:
- Designers: usage guidelines, token values, visual examples, do/don't
- Engineers: API reference, Angular signals, theming setup, RTL implementation
- Product: component catalog, pattern library, feature mapping
- AI Agents: registry query API, knowledge graph traversal guide, generation rules

### Layer 7: Continuous Learning

Structured learning with consistency safeguards. Learning never breaks the system.

**Signal Collection:** Product usage analytics, UX research insights, engineering friction reports, accessibility audit results, compliance updates, new feature requirements.

**Impact Evaluation:** Each signal is classified: Does it require a new component (Entry A)? A modification (Entry C)? A fix (Entry D)? The knowledge graph determines what would be affected.

**Consistency Safeguards (applied before every learning-driven change):**
- Preserve token architecture (no raw values introduced)
- Avoid component duplication (graph overlap check)
- Maintain interaction consistency (no contradictory contracts)
- Validate accessibility compliance (rules engine)
- Validate enterprise compliance (compliance copilot)
- Consistency always overrides novelty

**Graph Evolution:** Approved changes update the knowledge graph. New edges added, weights updated, deprecated nodes marked. Changelog records every evolution with full reasoning.

---

## Execution Phases

### Phase 1: Foundation + First Component (Week 1-2)

Goal: Validate the entire architecture with one component end-to-end.

Build:
- All 4 schemas (entity, relationship, governance, brand-config)
- Knowledge graph structure with initial nodes
- Brand configs for WideBot and Hulul
- Token files (global, semantic, motion, both brands)
- Rules files (accessibility, responsive, RTL, governance, touch)
- ONE complete component: Button

The Button goes through the full pipeline:
1. Create button.registry.json with all fields
2. Add to knowledge graph
3. Run through ui-feature-builder to generate MDs
4. Preview via ui-feature-previewer
5. Sync to Figma via figma-md-sync
6. Generate Angular code

If this works end-to-end with full validation, the architecture is sound.

Output: Working pipeline. All schemas validated. Graph with ~20 nodes.

### Phase 2: Primitive Layer (Week 3-4)

Goal: Validate scale, cross-component consistency, and token reuse.

Build all primitives: input, icon, badge, avatar, chip, toggle, tooltip, checkbox, radio, select, textarea, switch, divider, skeleton, spinner.

Each component goes through the full pipeline. By the end:
- Token reuse patterns are clear (same tokens appearing across components)
- Knowledge graph shows meaningful relationships
- Governance rules are tested (naming, duplication prevention)
- First interaction contracts defined (form validation, focus management)
- Motion system tested across multiple components

Output: ~15-20 primitives. Graph with ~200 nodes. Interaction contracts. Motion system.

### Phase 3: Composites + AI + Patterns (Week 5-8)

Goal: Validate composition, patterns, and AI-specific UX.

Build composites from primitives: data-table, dialog, dropdown-menu, navigation, tabs, accordion, form, card, sidebar, command-palette.

Build AI-native components: chat-bubble, streaming-text, assistant-panel, suggestion-card, tool-result, typing-indicator.

Define patterns: AI assistant panel, conversation timeline, analytics dashboard, enterprise data table, audit activity log, workflow approval, notification center.

Test Entry C (evolve existing): modify Button, verify blast radius propagation.
Test Entry B (Lovable prototype): generate one feature via Lovable, translate through pipeline.

Output: ~30 composites + AI components. ~10 patterns. Graph with ~500 nodes.

### Phase 4: Templates + Learning + Full System (Week 9-12)

Goal: Validate page generation, learning loop, and complete system.

Define page templates: dashboard, conversation inbox, analytics workspace, admin console, approval workflow, settings page.

Test Entry E: add a hypothetical third brand to verify the system scales.

Implement learning loop: simulate usage data, UX feedback, accessibility audit findings. Verify consistency safeguards hold under pressure.

Generate complete documentation for all 4 audiences.

Test the ultimate goal: give an AI agent a feature requirement and have it generate a complete page from the template, composed of patterns, built from components, styled with tokens, accessible, RTL-ready, brand-aware.

Output: Full system operational. ~6 templates. Learning loop active. AI agents can generate full pages.

---

## Instruction Coverage Checklist

| Instruction Requirement | Where It Lives |
|------------------------|----------------|
| AI readability | Registry JSON as primary AI interface |
| Token-driven design | Token store with semantic aliases + brand overrides |
| Component composability | Patterns compose components via registry references |
| Accessibility (WCAG AAA/AA) | Per-brand rules in accessibility.rules.json + brand config |
| Enterprise compliance | product-compliance-copilot agent with BLOCK authority |
| Governance lifecycle | governance.schema.json with 7-state lifecycle |
| Continuous learning | Layer 7 with signal collection + consistency safeguards |
| Knowledge graph | Layer 1: queryable nodes + edges with traversal |
| AI Component Registry | Layer 3 registry/ as PRIMARY interface |
| Component schema | Unified entity.schema.json with component extensions |
| Motion system | motion.tokens.json + motion definitions in motions/ |
| Interaction contracts | interactions/ directory with full contract schema |
| Mobile touch model | touch.rules.json with hover->tap, tooltip->sheet mappings |
| Responsive system | responsive.rules.json + per-component responsive specs |
| Pattern library | patterns/ directory with composition + layout + interaction |
| Page templates | templates/ directory with grid + regions + responsive |
| RTL + LTR | rtl.rules.json + per-component RTL specs in registry |
| Arabic RTL | figma-md-sync generates Arabic text in RTL variants |
| Light + Dark themes | Per-brand token files with light/dark values |
| HugeIcons | Specified in brand config, RTL mirroring in rtl.rules |
| Documentation for 4 audiences | docs/ with designers, engineers, product, ai-agents |
| No raw design values | Governance rules + schema validation prevent raw values |
| No variant explosion | Governance rules limit variant naming |
| No undocumented interactions | Interaction contract required for complex behaviors |
| No inconsistent motion | Motion tokens + reduced motion alternatives required |
| No uncontrolled growth | Knowledge graph overlap check before new components |
| Human approval required | Governance lifecycle requires approval gate |
| Agent orchestration | Layer 4 with permissions, routing, conflict resolution |
| Figma integration | figma-md-sync + figma-console-mcp for sync |
| Lovable integration | Optional Entry B with constraint injection |
| Reference: shadcn/ui richness | Component registry depth matches/exceeds shadcn |
| Reference: Quo AI interfaces | AI-native component category in registry |
| Reference: Gemini micro-interactions | Motion system + interaction contracts |
