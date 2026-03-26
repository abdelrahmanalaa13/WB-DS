# Final Plan: Corrected Architecture and Execution

**Date:** 2026-03-25
**Replaces:** PATCH_PLAN.md, REVISED_PLAN.md

---

## Part 1: Why I Was Wrong (Lessons From Rejections)

### Memory System: Fundamentally Wrong Mental Model

I proposed memory as a consumer-product personalization feature. A design system is not a consumer product. It is a governance framework. The law book for product design. Laws do not "learn" from individual user preferences. Laws are amended through governance.

**What I got wrong and why:**

| My Proposal | Why It Was Rejected | Correct Principle |
|------------|-------------------|------------------|
| Silent Context Recall: adapt without announcing | Breaks human-in-the-loop. Every decision must be auditable. "Silent" = invisible = ungovernable | Every adaptation must be visible and approvable |
| Preference Learning: remember per-user choices | Different team members using different preferences = inconsistency. The DS exists to PREVENT individual variation | The DS produces ONE correct output regardless of who uses it |
| Error-Correcting Memory: rejection triggers different harmony | "Try something different next time" = randomized output. A DS must be deterministic. Same input = same output every time | Rejection triggers: find the CORRECT component from DS. If missing, report the gap with recommended alternative from existing components |
| Privacy as QI Feature: temporary mode, forget-me | Kills the DS concept. A DS is institutional knowledge. You cannot "forget" institutional rules | All decisions are permanent records. Governance manages change, not deletion |

**The corrected behavior when a result is rejected:**
1. Search the existing DS for the correct component
2. If found: use it. Log the correction for audit
3. If not found: report the missing component. Recommend the closest existing alternative. Submit a governance proposal for the new component

This is how a law book works. You do not improvise. You apply the law. If the law has a gap, you amend it through process.

### AG-UI Corrections

**State deltas must be logged, not hidden.** I wrote "Agent silently adjusts preferences/context. No announcements." This directly violates enterprise audit compliance. Every state change via RFC 6902 JSON Patch must be logged with: who changed it, when, what the previous value was, and why. Transparency is not optional in enterprise software.

**ZDR (Zero Data Retention) for reasoning:** You asked if this affects learning quality or implementation. Answer: ZDR means the server encrypts chain-of-thought and the client stores it as an opaque blob. The client forwards it back on subsequent turns. This means:
- Learning quality: NOT affected. The agent still has access to its own reasoning (via the encrypted blob). It just cannot be inspected externally
- Implementation: DOES affect us. We must decide: do we need to audit reasoning traces? If yes (enterprise compliance), we cannot use ZDR for reasoning. We should store reasoning in plain text in audit logs. ZDR is only appropriate for end-user-facing consumer products, not enterprise governance tools
- For our DS: reasoning traces should be VISIBLE and LOGGED. No encryption. Full transparency

**Interrupts for local folder access and core DS file changes:** Correct. The interrupt mechanism should ALWAYS fire when:
- An agent attempts to access local filesystem
- An agent proposes changes to any core DS file (ds-library.html, any JSON architecture file, any token definition)
- An agent proposes a new component or pattern
- An agent modifies governance records

This maps directly to the governance lifecycle. Interrupts ARE the governance gate.

---

## Part 2: Full Workflow Audit

### File Inventory: 29 files, 1.8MB total

| File | Size | Status | Verdict |
|------|------|--------|---------|
| ds-library.html | 325KB | Actual DS code | SOURCE OF TRUTH. Keep |
| tokens-optimized.json | 621KB | Token dump | REDUNDANT. Tokens are already in ds-library.html :root. This file is a copy that can drift. Delete or auto-generate from HTML |
| ai-component-registry.json | 107KB | Component schemas | ASPIRATIONAL. Claims capabilities (WCAG AAA, RTL, slots) that ds-library.html does not implement. Must be rebuilt from actual code |
| ai-knowledge-graph.json | 88KB | Entity relationships | DISCONNECTED. 207 relationships that reference entities by name but never validate they exist in the HTML. Aspirational |
| ai-interaction-contracts.json | 123KB | Behavioral specs | ASPIRATIONAL. 15 contracts describing behavior that has no corresponding JS implementation. Zero DOM events referenced |
| ai-motion-system.json | 60KB | Motion patterns | PARTIALLY VALID. Motion tokens exist in CSS. But 45 patterns reference components that may not use them. 3/37 reduced-motion coverage |
| ai-page-templates.json | 123KB | Page layouts | ASPIRATIONAL. 10 templates describing pages that do not exist. Zero implementation |
| ai-responsive-system.json | 51KB | Breakpoint rules | ASPIRATIONAL. 4 breakpoints documented. Zero @media or @container queries in ds-library.html |
| ai-mobile-touch-model.json | 29KB | Touch gestures | ASPIRATIONAL. 41 component overrides. Zero implementation |
| ai-governance-lifecycle.json | 21KB | Governance process | ASPIRATIONAL. 7 phases. No component tracks its phase. No approval records exist |
| PATCH_PLAN.md | 12KB | Feature roadmap | SUPERSEDED. Built on top of unfinished foundations. Replace with this document |
| REVISED_PLAN.md | 22KB | Corrected roadmap | SUPERSEDED. Contains memory system proposals that were correctly rejected. Replace with this document |
| RETROSPECTIVE.md | 19KB | Honest audit | VALID. Keep as historical record |
| AG-UI_KNOWLEDGE.md | 20KB | Protocol reference | VALID but needs corrections (state delta logging, ZDR, interrupt rules). Will update |
| DS_LEARNING_LOOP.md | 25KB | Learning records | PARTIALLY VALID. Contains useful historical decisions but also aspirational entries |
| DS_UNIVERSAL_CHECKLIST.md | 9KB | U1-U19 rules | VALID. Keep as enforcement reference |
| CONTAINER_ARCHITECTURE.md | 12KB | Container docs | REVIEW NEEDED |
| architecture-plan.md | 17KB | System architecture | REVIEW NEEDED. May contain aspirational claims |
| ds-competitive-analysis.html | 41KB | Competitor comparison | VALID as research. But claims about our DS capabilities are inflated |
| Various deep-dive HTMLs | ~250KB | Research reports | VALID as reference. No false claims about our DS |
| 5 skill SKILL.md files | ~15KB | Agent skills | NOT INSTALLED. Sitting in skills-to-install/. Never tested |
| VERSION | tiny | Version tracker | Shows 1.0.0. Never updated despite claimed patches |
| index.html | 9KB | Entry point | REVIEW NEEDED |
| font-test.html | 20KB | Font testing | VALID as test artifact |

### Redundancies Found

1. **tokens-optimized.json (621KB)** duplicates what is in ds-library.html :root. Two sources of truth for the same data. Must be auto-generated, never hand-maintained
2. **PATCH_PLAN.md and REVISED_PLAN.md** are both superseded. Two dead roadmaps cluttering the repo
3. **ai-component-registry.json** and **ai-knowledge-graph.json** overlap significantly. The registry describes component tokens/states. The knowledge graph also maps component-to-token relationships. Two files describing the same relationships differently
4. **5 skill files** reference the architecture JSONs as authoritative sources, but those JSONs are aspirational. Skills built on false foundations

### Missing Sources

1. **No validation script.** Nothing checks if JSON files match the HTML
2. **No contrast ratio data.** WCAG claims with zero measured values
3. **No Arabic text corpus.** RTL claimed but no Arabic content anywhere
4. **No test harness.** Zero automated testing of any kind
5. **No canary test for Material Symbols.** Learned from HugeIcons failure but have not applied the lesson yet
6. **No AG-UI event-to-component mapping.** AG-UI knowledge exists as a document but is not integrated into the registry

### Aspirational Items Masquerading as Real

1. ai-component-registry.json: `"wcagLevel": "AAA (WideBot), AA (Hulul)"` on every component. Zero implementation
2. ai-component-registry.json: `"responsiveBehavior"` on every component. Zero @media queries
3. ai-component-registry.json: `"motionBehavior"` on many components. 3/37 have reduced-motion
4. ai-governance-lifecycle.json: entire file. No component follows this process
5. ai-page-templates.json: entire file. No page exists
6. ai-responsive-system.json: entire file. No responsive code exists
7. ai-mobile-touch-model.json: entire file. No touch handling exists
8. ai-interaction-contracts.json: 15 contracts. Zero JS event handlers implement them
9. VERSION file says 1.0.0. We never shipped 1.0.0. We shipped a prototype

### Wrong Logical Order (What Actually Happened)

| Order | What I Did | What Should Have Happened |
|-------|-----------|--------------------------|
| 1 | Built 37 components in HTML | Correct start |
| 2 | Created architecture JSONs describing ideal state | WRONG. Should have created JSONs ONLY for what exists |
| 3 | Created governance lifecycle JSON | WRONG. Should have enforced governance on existing components first |
| 4 | Integrated HugeIcons without testing CDN | WRONG. Canary test first |
| 5 | Built 5 agent skills referencing aspirational JSONs | WRONG. Skills reference data that does not match reality |
| 6 | Competitive analysis claiming our DS has features it lacks | WRONG. Inflated self-assessment |
| 7 | Patch plan adding features on broken foundations | WRONG. Fix foundations first |
| 8 | Memory system proposal for a governance tool | WRONG. Misunderstood what a DS is |

### Wasteful Actions

1. **HugeIcons integration: ~3 agent operations.** 44 sizing rules, 26 SVG replacements, CSS compatibility layer. All wasted because the CDN does not serve the font
2. **tokens-optimized.json: 621KB.** A manual copy of data already in the HTML. Will drift. Maintenance cost with zero value
3. **ai-page-templates.json: 123KB.** 10 page templates that describe pages we will never build in the DS. Pages are product concerns, not DS concerns. The DS provides components and patterns, not complete pages
4. **Memory system research and documentation.** Correct for a consumer product. Wrong for a governance tool. Time spent on a concept that was rejected for good reason

### Overloaded Agents

The 5 skills in skills-to-install/ try to do too much per agent:
- ds-copilot: architecture + governance + token audit + knowledge graph + proposal validation. That is 5 separate responsibilities
- ds-ai-agent-copilot: AI behavior + streaming + confidence + error UX + memory + suggestions + feedback + accessibility. That is 8 responsibilities

A skill should have ONE clear responsibility.

---

## Part 3: Corrected Architecture

### Principle: The DS is a Deterministic Governance System

The design system produces the same output regardless of who uses it, when they use it, or what they used before. It is not personalized. It is not adaptive per user. It is a shared institutional standard.

When an AI agent uses the DS:
- It reads the component registry to find the RIGHT component
- It applies the component as specified (tokens, states, accessibility, RTL)
- If the right component does not exist, it reports the gap
- It NEVER improvises, approximates, or "learns" a different approach

### File Architecture (Corrected)

**Keep (source of truth):**
```
ds-library.html              <- THE source of truth. All CSS, HTML, JS
DS_UNIVERSAL_CHECKLIST.md     <- U1-U19 enforcement rules
DS_LEARNING_LOOP.md           <- Historical decisions log (pruned of aspirational entries)
AG-UI_KNOWLEDGE.md            <- Protocol reference (corrected)
RETROSPECTIVE.md              <- Historical record
FINAL_PLAN.md                 <- This document (active roadmap)
```

**Rebuild from scratch (must match ds-library.html exactly):**
```
ai-component-registry.json   <- Rebuilt by parsing actual HTML/CSS. Only proven capabilities
ai-knowledge-graph.json       <- Rebuilt from actual token-to-component mappings in CSS
ai-motion-system.json         <- Rebuilt from actual motion tokens and @media queries in CSS
```

**tokens-optimized.json: ARCHIVED to /archive/**
```
tokens-optimized.json         <- Old Figma token extraction (~3,730 values, 19 token sets).
                                  ds-library.html has since been enhanced independently.
                                  All 141 tokens in :root are self-contained literal values.
                                  Zero references to this file from any project file.
                                  Zero var() cross-references inside :root.
                                  The HTML is fully independent and is the source of truth.
                                  Archived (not deleted) in case historical reference is needed.
                                  Location: /archive/tokens-optimized.json
```

**Rebuild as implementation (must match ds-library.html and be backed by code):**
```
ai-mobile-touch-model.json    <- Currently aspirational. Rebuild when touch behavior is implemented in code
ai-responsive-system.json     <- Currently aspirational. Rebuild when @media/@container queries exist in CSS
ai-interaction-contracts.json <- Currently aspirational. Rebuild when JS event handlers are implemented
ai-governance-lifecycle.json  <- Will be rebuilt as enforcement mechanism, not spec
```

**Delete permanently:**
```
ai-page-templates.json        <- Pages are product concerns, not DS concerns. Removed from DS scope
PATCH_PLAN.md                 <- Superseded by FINAL_PLAN.md
REVISED_PLAN.md               <- Superseded by FINAL_PLAN.md
```

**Why delete ai-page-templates.json:** A design system provides components and composition patterns. It does NOT provide complete page layouts. Pages are product decisions. A "Dashboard" page layout belongs in the product repo, not the DS repo. The DS provides the DataTable, Chart, Card, and Layout Grid components. The product decides how to arrange them.

**Why delete ai-interaction-contracts.json:** Behavioral contracts are only meaningful when backed by code. 15 contracts with zero JS implementation is 123KB of fiction. When we implement interactive behavior in ds-library.html, THEN we document what we built.

### Agent/Skill Architecture (Corrected)

Current: 5 overloaded skills with blurred responsibilities.

Corrected: 3 focused skills, each with ONE job.

**ds-validator** (replaces ds-copilot)
- ONE JOB: Validate that ds-library.html complies with U1-U19
- Reads ds-library.html, checks each rule, produces pass/fail report
- No architecture design. No proposals. Just validation

**ds-registry-sync** (new)
- ONE JOB: Generate ai-component-registry.json from ds-library.html
- Parses actual CSS classes, tokens, states, ARIA attributes
- Only documents what exists. Never adds aspirational fields
- Output is deterministic: same HTML input = same JSON output

**ds-agent-protocol** (replaces ds-ai-agent-copilot)
- ONE JOB: Map components to AG-UI protocol (readable, actions, events)
- Reads the proven registry (from ds-registry-sync) and adds agentProtocol schemas
- Only maps capabilities that are implemented

The other 3 skills (ds-product-compliance-copilot, ds-ux-research-copilot, ds-frontend-copilot) are premature. They reference architecture that does not exist. Archive until foundations are solid.

### AG-UI Integration (Corrected)

Three corrections from your feedback:

**1. State deltas are ALWAYS logged:**
```json
{
  "agentProtocol": {
    "statePolicy": {
      "logging": "mandatory",
      "auditTrail": true,
      "deltaLog": {
        "fields": ["timestamp", "actor", "operation", "path", "previousValue", "newValue", "reason"],
        "retention": "permanent"
      }
    }
  }
}
```
No silent updates. Every RFC 6902 patch is a logged, auditable record.

**2. Reasoning traces are stored in plain text, not encrypted:**
```json
{
  "agentProtocol": {
    "reasoningPolicy": {
      "visibility": "logged",
      "storage": "plaintext",
      "zdr": false,
      "auditAccess": "all-team-members",
      "displayMode": "collapsed-by-default-expandable"
    }
  }
}
```
ZDR does NOT affect learning quality (agent still accesses its own reasoning via the blob). But it DOES prevent audit inspection. For an enterprise governance tool, audit access to reasoning is mandatory. We display reasoning collapsed by default (QI principle: progressive disclosure) but it is ALWAYS inspectable and logged.

**3. Interrupts are mandatory for:**
```json
{
  "agentProtocol": {
    "interruptPolicy": {
      "alwaysInterrupt": [
        "localFilesystemAccess",
        "coreDsFileModification",
        "newComponentProposal",
        "governanceRecordChange",
        "tokenValueChange",
        "architectureFileModification"
      ],
      "neverInterrupt": [
        "readOnlyRegistryQuery",
        "validationCheck",
        "searchExistingComponents"
      ]
    }
  }
}
```

### Rejection Handling (Corrected)

When an agent-generated result is rejected:

```
Step 1: Search ai-component-registry.json for the correct component
  |
  +--> Found? Use it. Log: "Correction applied. Used [component] instead of [rejected]"
  |
  +--> Not found?
        |
        Step 2: Search for closest alternative in existing DS
        |
        Step 3: Report to governance:
          - Missing component: [name]
          - Use case: [description]
          - Recommended alternative from existing DS: [component]
          - Proposal status: PENDING HUMAN APPROVAL
        |
        Step 4: Use the recommended alternative until governance approves the new component
```

No improvisation. No "try different harmony." The DS is the law. Follow it or amend it through process.

---

## Part 4: Final Execution Plan

### Phase 0: Clean Slate (1 session)

Remove the noise. Establish truth.

| Step | Task | Output |
|------|------|--------|
| 0.1 | Canary test Material Symbols Rounded | icon-canary-test.html with all 37 icons rendering correctly |
| 0.2 | Replace HugeIcons with Material Symbols in ds-library.html | Working icons in all components |
| 0.3 | Load IBM Plex Sans Arabic, update --font-sans token | Arabic font renders correctly |
| 0.4 | Delete: ai-page-templates.json, PATCH_PLAN.md, REVISED_PLAN.md | Pages removed from DS scope. Dead roadmaps removed |
| 0.5 | Mark aspirational JSONs: add `"implementationStatus": "aspirational"` header to ai-mobile-touch-model.json, ai-responsive-system.json, ai-interaction-contracts.json, ai-governance-lifecycle.json | Files kept for future implementation but clearly labeled as NOT yet real |
| 0.6 | Archive research HTMLs to /archive/: ds-competitive-analysis.html, apple-hig-deep-dive.html, design-aesthetics-deep-dive.html, responsive-rtl-mobile-deep-dive.html, m3-gap-analysis.html, status-color-analysis.html, ds-rethink.html, ds-auditor-eval-review.html | Research preserved but separated from active DS files |
| 0.7 | VERSION file set to 0.1.0 (honest: this is a prototype, not a release) | Honest version number |

**Exit criteria:** Icons render. Arabic font loads. Repo contains only truthful files.

### Phase 1: Accessibility (2 sessions)

Make WCAG compliance real.

| Step | Task | Verification |
|------|------|-------------|
| 1.1 | Add role attributes to all interactive components | Count matches component count |
| 1.2 | Add aria-label to all icon-only buttons/controls | Zero unlabeled interactive elements |
| 1.3 | Add aria-expanded, aria-selected, aria-disabled to stateful components | Accordion, tree, tabs, dropdown verified |
| 1.4 | Add aria-live regions for dynamic content | Toast, notification, AI chat responses |
| 1.5 | Write a contrast audit script (reads CSS tokens, calculates ratios) | WB pairs checked against 7:1 (AAA), HL pairs against 4.5:1 (AA). Results documented per pair |
| 1.6 | Add prefers-reduced-motion to ALL animated components | 37/37 covered |
| 1.7 | Keyboard navigation audit | Tab through all components. Document focus order. Fix traps |

**Exit criteria:** Every claim is backed by code. Contrast ratios are measured numbers, not assumptions.

### Phase 2: RTL/Bilingual (2 sessions)

Make Arabic real.

| Step | Task | Verification |
|------|------|-------------|
| 2.1 | Convert all physical CSS properties to logical | margin-left -> margin-inline-start, etc. Count all conversions |
| 2.2 | Add Arabic sample text to all component test data | Real Arabic ERP content (customer names, invoice labels, approval statuses) |
| 2.3 | Add dir="rtl" toggle to tester UI | One-click switch, visible in header |
| 2.4 | Mirror directional icons in RTL | Chevrons/arrows flip. Star/heart/settings do not |
| 2.5 | Test all 37 components in RTL | Visual verification of each |

**Exit criteria:** RTL toggle works. All components render correctly in both directions with real Arabic text.

### Phase 3: U6 Completion + Atomic Modifiers (1 session)

Small CSS fixes. No dependencies. Cleaning the code before adding new behavior.

| Step | Task | Verification |
|------|------|-------------|
| 3.1 | Convert remaining 2 filled text field border-bottom to box-shadow | Zero border-* in DS component CSS |
| 3.2 | Refactor top 5 complex components to variable-scope modifier pattern | Flat specificity, no 0-3-0+ selectors |

**Exit criteria:** U6 fully compliant. Modifier pattern documented and applied.

**Why moved here:** U6 and atomic modifiers change CSS in ds-library.html. Running them before the registry rebuild avoids drift. They have zero dependencies on anything except Phase 0 (working icons/fonts).

### Phase 4: Responsive + Interactions + Touch (2 sessions)

Add all remaining code behavior to ds-library.html. After this phase, the HTML is COMPLETE.

| Step | Task | Verification |
|------|------|-------------|
| 4.1 | Define @media breakpoint tokens in ds-library.html :root (mobile: 640px, tablet: 1024px, desktop: 1440px) | Breakpoints exist as CSS custom properties |
| 4.2 | Add @media breakpoint rules for key components | DataTable, Navigation, Dialog, Panel at minimum |
| 4.3 | Add @container queries for composed components | Cards, list items adapt to container width |
| 4.4 | Implement JS event handlers for core interaction contracts (form validation, search, modal lifecycle, toast) | Actual DOM event listeners, not specs |
| 4.5 | Define mobile touch alternatives for implemented components | Hover-to-tap-reveal, tooltip-to-bottom-sheet, etc. |

**Exit criteria:** Components adapt to viewport. Interactions work. Touch alternatives functional. ds-library.html is now code-complete for v0.1.0.

**Why here:** All code changes to ds-library.html must finish BEFORE the registry rebuild (Phase 5). Otherwise the registry drifts the moment we add more code.

### Phase 5: Rebuild ALL Architecture From Truth (1-2 sessions)

Single pass. Parse ds-library.html ONCE. Generate ALL JSON files. No partial rebuilds.

ds-library.html is now code-complete (accessibility, RTL, U6, responsive, interactions, touch all done in Phases 1-4). This is the ONLY time we generate architecture files.

| Step | Task | Verification |
|------|------|-------------|
| 5.1 | Parse ds-library.html: extract all .ds-* CSS classes, tokens, states, ARIA attributes, @media queries, @container queries, JS event handlers, touch handlers | Automated extraction produces a complete inventory |
| 5.2 | Generate ai-component-registry.json from extracted data | Every field is PROVEN. No aspirational entries |
| 5.3 | Generate ai-knowledge-graph.json from token-to-component mappings | Every relationship verified in code |
| 5.4 | Generate ai-motion-system.json from actual motion tokens and reduced-motion queries | Only documents what exists |
| 5.5 | Rebuild ai-responsive-system.json from actual @media/@container in CSS | Every rule in JSON exists in code. Remove aspirational header |
| 5.6 | Rebuild ai-interaction-contracts.json from implemented JS handlers | Every contract references a real function/event |
| 5.7 | Rebuild ai-mobile-touch-model.json from implemented touch handlers | Every override has code backing it |
| 5.8 | Add AG-UI agentProtocol schema to each component (readable, actions, events) | Schemas reference implemented capabilities only |
| 5.9 | Add governancePhase field to every component | All 37 marked "released" |
| 5.10 | Add interruptPolicy per component | Which agent actions require human approval |
| 5.11 | Add rejection handling protocol | How components report "not found" and recommend alternatives |

**Exit criteria:** ALL JSON files generated in ONE pass from ONE source of truth. Every field proven. Zero aspirational entries anywhere.

**Why merged into one phase:** The old plan had Phase 3 (registry), Phase 4 (rebuild responsive/interaction/touch JSONs), and Phase 5 (governance + AG-UI schemas) all writing to the same JSON files. Three phases touching the same outputs = three chances for drift. One pass = one source of truth = no drift.

### Phase 6: Governance Enforcement + Skills (1 session)

Now that architecture files are truthful, build the enforcement tools.

| Step | Task | Verification |
|------|------|-------------|
| 6.1 | Rebuild ai-governance-lifecycle.json as enforcement mechanism | Interrupt rules, rejection protocol, approval gates all reference real code paths from Phase 5 registry |
| 6.2 | Write ds-validator skill (ONE JOB: U1-U19 compliance check) | Skill runs against ds-library.html, produces pass/fail per rule |
| 6.3 | Write ds-registry-sync skill (ONE JOB: generate JSON from HTML) | Skill re-runs Phase 5 extraction. Output is deterministic: same HTML = same JSON |

**Exit criteria:** Governance is enforced by code. Skills produce deterministic output.

**Why after Phase 5:** Skills read architecture files. Architecture files must be truthful before skills reference them. Governance lifecycle must reference real code paths that exist in the registry.

### Phase 7: QI Foundations (only after Phases 0-6)

Now we can add intelligence features on a truthful foundation.

| Step | Item | Depends On |
|------|------|-----------|
| 7.1 | Spring animation via CSS linear() | Phase 1 (reduced-motion 37/37) |
| 7.2 | Material Symbols expansion (200+ icons) | Phase 0 (icon system working) |
| 7.3 | AI Confidence Indicator component | Phase 1 (ARIA), Phase 5 (truthful registry) |
| 7.4 | Reasoning Trace Block component | Phase 1 (ARIA), AG-UI ReasoningMessage events |
| 7.5 | Streaming text animation tokens | Phase 1 (reduced-motion) |
| 7.6 | ds-agent-protocol skill (AG-UI mapping) | Phase 5 (truthful registry), Phase 6 (interrupt rules) |
| 7.7 | Fluid typography with clamp() | Phase 2 (Arabic font working) |
| 7.8 | --surface-elevated token | Phase 3 (token discipline) |
| 7.9 | Generative UI tool registry (AG-UI pattern) | Phase 5 (truthful registry) |
| 7.10 | Activity event UI components (AG-UI pattern) | Phase 5 (truthful registry) |

**Note:** Phase 7 adds new code to ds-library.html. After Phase 7, re-run ds-registry-sync skill (Phase 6.3) to update architecture files. This is not a loop. It is the designed maintenance cycle: change code, then sync JSON

---

## Part 5: Process Rules (Final)

1. **Code first, document second.** JSON files are generated from HTML. Never hand-written independently
2. **Canary test external resources.** One standalone HTML file before any integration
3. **Proof or delete.** Every capability claim is backed by code. Aspirational items are removed, not labeled
4. **The DS is deterministic.** Same input = same output. No personalization. No "learning" per user
5. **Rejections follow governance.** Find correct component, or report gap. Never improvise
6. **State changes are logged.** Every AG-UI delta has an audit trail
7. **Reasoning is transparent.** No ZDR. Plain text. Collapsed by default, inspectable always
8. **Interrupts guard the gates.** Any modification to DS core files requires human approval
9. **One skill, one job.** No overloaded agents with 8 responsibilities
10. **Delete what is broken.** Archive aspirational files. Do not maintain fiction alongside truth
