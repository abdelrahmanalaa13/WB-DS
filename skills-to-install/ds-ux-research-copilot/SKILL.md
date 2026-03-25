---
name: ds-ux-research-copilot
description: >
  User experience validation and usability insights agent for the design system. Provides UX
  research frameworks, usability heuristics, and validation methodologies for design system
  components and patterns. Use this skill when conducting usability reviews, heuristic evaluations,
  user flow analysis, interaction audits, or gathering UX insights for component improvements.
  Trigger when the user says "UX review", "usability check", "heuristic evaluation", "user flow
  analysis", "interaction audit", "UX validation", "usability testing plan", "cognitive walkthrough",
  "UX metrics", "task analysis", "user journey review", or any phrase involving UX research,
  usability validation, or user experience improvement for design system components.
---

# ds-ux-research-copilot: User Experience Validation and Usability Insights

## Role and Scope

This agent validates the usability and learnability of every component, pattern, and template in the design system. Works with all 37 components across L1-L6 and both brands (WideBot enterprise, Hulul SMB).

Primary activities:
- Conduct heuristic evaluations against Nielsen's 10 usability principles
- Execute cognitive walkthroughs for multi-step interactions
- Design usability test plans and research protocols
- Analyze user flows and task completion paths
- Provide UX metrics and measurement frameworks
- Document findings in research reports with actionable improvement recommendations

## Heuristic Evaluation Framework

Apply Nielsen's 10 usability heuristics to every component. Rate each heuristic on 1-5 scale (1=major violation, 5=exemplary).

### 1. Visibility of System Status
- Does the component provide real-time feedback for user actions?
- Is loading state clearly indicated (spinner, skeleton)?
- Are disabled states visually distinct from enabled?
- Is form submission progress shown (steps, percentage)?
- Checklist: Visual feedback within 100ms, clear loading indicators, state changes obvious

### 2. Match Between System and Real World
- Does the component use language users understand (not jargon)?
- Are icons immediately recognizable (test against diverse users)?
- Do error messages explain what went wrong in plain English?
- Checklist: No technical jargon, consistent terminology with user mental models, metaphors align with real-world behavior

### 3. User Control and Freedom
- Can users undo recent actions or easily exit dialogs?
- Is there a clear way to cancel operations?
- Do wizards/multistep flows allow jumping to previous steps?
- Checklist: Escape key closes overlays, undo available for destructive actions, back navigation always present

### 4. Consistency and Standards
- Does this component follow the same interaction pattern as similar components?
- Are button placements, colors, and states consistent across the design system?
- Do success/error states use the same token colors everywhere?
- Checklist: Consistent button emphasis usage, matching state visual hierarchy, predictable interaction patterns

### 5. Error Prevention
- Does the component validate input before submission?
- Are confirmation dialogs shown before destructive actions?
- Is there a clear indication of required vs optional fields?
- Checklist: Real-time input validation, confirmation for deletion/data loss, required field indicators, helpful hints

### 6. Recognition Over Recall
- Is the component self-explanatory without tooltips?
- Are options visible in dropdowns rather than requiring memory?
- Do icons have text labels or tooltips?
- Checklist: Labels visible, no hover-only content, dropdown options readable without expansion

### 7. Flexibility and Efficiency of Use
- Can power users work faster than novices (keyboard shortcuts, bulk actions)?
- Are there multiple ways to complete a task?
- Do form fields support autocomplete or prefill?
- Checklist: Keyboard shortcuts documented, bulk operations available, autocomplete for common inputs

### 8. Aesthetic and Minimalist Design
- Is visual hierarchy clear (important actions prominent, secondary actions de-emphasized)?
- Is there excessive decoration or unnecessary information?
- Are animations purposeful or distracting?
- Checklist: Clear visual hierarchy, purposeful use of white space, animations under 300ms

### 9. Error Recovery
- When errors occur, do messages suggest how to fix the problem?
- Are error messages specific (not "Invalid input")?
- Can users recover without starting over?
- Checklist: Error suggestions provided, context preserved on error, recovery path clear

### 10. Help and Documentation
- Are help resources discoverable (tooltips, help icons, documentation)?
- Is help task-focused rather than system-focused?
- Are there example values or use case guidance?
- Checklist: Help accessible from context, documentation current and tested, examples clear

## Cognitive Walkthrough Protocol

Execute this protocol for any multi-step interaction or complex component:

1. **Define User Goal**: State the specific goal (e.g., "Approve a purchase order")
2. **Identify Action Sequence**: Map exact steps user must take in the UI
3. **For Each Step**:
   - Will the user know this action is possible?
   - Will the user find the right control for this action?
   - Will the user recognize that this control does what they need?
   - Will the user understand the feedback after action?
4. **Identify Failure Points**: Where might users get stuck?
5. **Document Recovery Paths**: How can users recover from mistakes?
6. **Rate Learnability**: First-time user vs experienced user effort comparison

Example: Approval workflow for invoice
- Step 1: Navigate to "Pending Approvals" (icon visible in navigation?)
- Step 2: Find invoice in list (sortable columns? search?)
- Step 3: Review details in preview (all fields visible? no scrolling surprises?)
- Step 4: Enter comment (optional? required? placeholder helpful?)
- Step 5: Select Approve button (clearly distinguished from Reject?)
- Step 6: Confirm or submit (confirmation needed for high-value invoices?)
- Step 7: Receive success feedback (toast, redirect, or modal?)

## Component Usability Metrics

Measure each component category with these metrics:

- **Task Completion Rate**: % of users completing task without assistance (target: >95%)
- **Time on Task**: Average seconds to complete standard task (establish baseline first)
- **Error Rate**: % of user actions that result in errors (target: <5%)
- **Learnability**: Comparison of first task vs third task time (target: <15% time difference)
- **SUS Score**: System Usability Scale survey (target: >70 for enterprise, >65 for SMB)
- **Accessibility Score**: % of WCAG AA criteria met (WideBot target: 100% AAA, Hulul target: 100% AA)
- **User Satisfaction**: 5-point Likert scale ("easy to use", "clear feedback", "error messages helpful")

## User Flow Validation

For every multi-component workflow, validate these aspects:

- **Entry Points**: Where can users enter the flow? Are entry points discoverable?
- **Exit Points**: How can users exit gracefully without losing data?
- **Branching Logic**: Are conditional paths (if A then B, else C) clearly indicated?
- **Error Recovery**: If a step fails, what happens? Can users retry or backtrack?
- **Progress Indicators**: Do multi-step flows show progress (step counter, progress bar)?
- **Data Persistence**: Is form data preserved if user navigates away?
- **Performance**: Are transitions between steps responsive (<100ms)?

Standard flow validation questions:
1. Can a user enter this flow from multiple entry points?
2. Is the current step clearly indicated?
3. Can users navigate backward to review/change previous steps?
4. Are destructive actions (submit, delete) confirmed with a dialog?
5. Is success clearly communicated (message, redirect, or confirmation)?
6. Can users pause and resume (save draft)?

## Interaction Pattern Audit

Every interaction pattern in the design system must follow these standards:

- **Response Time**: User action receives visual feedback within 100ms
- **Feedback Loops**: State changes visible immediately (button presses show :active state)
- **Undo/Redo**: All non-destructive actions support undo (minimum last 5 actions)
- **Progressive Disclosure**: Show essential content first, details on demand (expandable sections)
- **Information Hierarchy**: Most important information prominent, supporting details secondary
- **Feedback Clarity**: Success/error states use consistent visual language (green for success, red for error)

Audit checklist for any interaction:
- [ ] Visual feedback appears within 100ms
- [ ] Button press shows active state
- [ ] Hover state present and distinguishable
- [ ] Focus indicator visible (3px WideBot, 2px Hulul)
- [ ] Keyboard alternative available
- [ ] Undo available for destructive actions
- [ ] Error message actionable (suggests fix, not just problem)

## RTL/Bilingual UX

Design system must support right-to-left (RTL) interfaces for Arabic. Special considerations:

- **Reading Direction**: Icons, chevrons, arrows must mirror in RTL (scaleX transform)
- **Number Handling**: Numbers stay LTR in Arabic text (e.g., "الرقم 123" not "123 الرقم")
- **Date Formatting**: Respect locale (Arabic uses Hijri calendar option)
- **Form Alignment**: Labels and inputs swap positions (label on right in RTL)
- **Icon Mirroring**: Directional icons (back/forward, left/right) flip horizontally
- **Text Truncation**: Ellipsis behavior consistent in RTL (right side, not left)
- **Number Input**: Plus/minus buttons position swapped in RTL (minus on right)

RTL Testing Checklist:
- [ ] Text direction flows correctly
- [ ] Icons mirror appropriately
- [ ] Layout doesn't shift with text length changes
- [ ] Input fields align correctly with labels
- [ ] Buttons and controls position swapped as needed
- [ ] Numbers display in correct format
- [ ] Tooltips position correctly

## Mobile Usability

For components used on mobile devices:

- **Touch Target Size**: Minimum 44px x 44px (iOS 44x44, Android 48x48)
- **Thumb Zone Mapping**: Place most-used controls in lower third of screen
- **Gesture Discoverability**: Swipe/pinch gestures must be discoverable (affordance indicators)
- **Scroll Behavior**: Page shouldn't scroll unexpectedly, inputs focus without scroll jumps
- **Input Method**: Text input shows appropriate keyboard (email for @email, number for quantities)
- **Spacing**: Sufficient gap between touch targets (minimum 8px)
- **Orientation**: Design works in portrait and landscape

Mobile checklist:
- [ ] Touch targets minimum 44px
- [ ] Important actions in lower third
- [ ] Gestures have visual affordances
- [ ] Text readable at arm's length
- [ ] Responsive layout without horizontal scroll
- [ ] Form inputs fast (proper keyboard type, autocomplete)

## AI Interaction UX Research

For components with AI-powered features, conduct research on:

- **Trust Calibration**: Do users trust AI recommendations? How confident should system appear?
- **Explanation Effectiveness**: Does explaining AI reasoning increase user confidence? What level of detail?
- **Error Recovery**: How do users react when AI makes mistakes? Is human override clear?
- **User Mental Models**: What do users think the AI can/cannot do? Are expectations aligned?
- **Transparency Trade-offs**: Detailed explanations vs simplicity. Where's the right balance?
- **Confidence Indicators**: Do users understand confidence scores? Are they actionable?

Research protocols:
1. **Qualitative interviews** (5-8 users): Ask why they trust/distrust AI recommendations
2. **A/B testing** (>100 users): High confidence vs low confidence UI
3. **Mental model elicitation**: Ask users to predict AI behavior in scenarios
4. **Error scenarios**: How do users recover when AI fails?
5. **Think-aloud protocol**: Watch users interact with AI features, note confusion points

## Research Output Templates

### Usability Report
Structure:
1. Executive Summary (1 paragraph with key findings and recommendations)
2. Methodology (number of participants, testing method, component tested)
3. Heuristic Evaluation Results (1-5 scores per heuristic with justification)
4. Task Completion Analysis (which tasks succeeded/failed, why)
5. User Quotes (3-5 verbatim quotes supporting findings)
6. Top 3 Recommendations (prioritized by impact, specific and actionable)
7. Appendix (testing script, participant demographics, raw data)

### Heuristic Evaluation Summary
For each heuristic:
- Score (1-5)
- Specific findings (what works, what doesn't)
- Example from component
- Severity (minor/moderate/severe)
- Recommended fix

### Cognitive Walkthrough Findings
For each step:
- Action required
- Predictability (will user know to do this?)
- Visibility (can user find the control?)
- Clarity (will user understand feedback?)
- Failure risk (low/medium/high)
- Recovery suggestion if failure occurs

### Component Improvement Recommendations
For each recommendation:
- Issue (specific, not vague)
- Impact (how many users affected? what task impacted?)
- Effort (low/medium/high to implement)
- Proposed solution (specific fix, not just "improve this")
- Success metric (how will we know it's fixed?)
