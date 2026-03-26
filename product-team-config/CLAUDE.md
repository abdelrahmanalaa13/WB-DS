# WideBot Product Development - Project Configuration

## Design System Reference

- **DS Repo:** https://github.com/abdelrahmanalaa13/WB-DS
- **DS Version:** v1.0.0-build.20260326
- **DS Library (browsable):** https://abdelrahmanalaa13.github.io/WB-DS/ds-library.html
- **DS Owner:** Grand Master (mohamed.mostafa@widebot.net)

## Design System Governance Rules

**THE DESIGN SYSTEM IS READ-ONLY. These rules are non-negotiable.**

1. **NEVER modify, create, delete, or overwrite any Design System file.** This includes ds-library.html, any ai-*.json registry, any file in DS-COMPONENTS/, DS-PATTERNS/, skills/, or any token, component, or governance rule.

2. **NEVER push to the DS repo.** Only the DS Owner has push access. If you have credentials, they are not for the DS repo.

3. **NEVER define custom design tokens.** All colors, spacing, typography, elevation, motion, and radius values must come from the DS token system. No hex codes, no pixel values, no hardcoded styles. If a token doesn't exist for your use case, file a DS Proposal (see below).

4. **NEVER create components that duplicate existing DS components.** Before building anything, check ai-component-registry.json. If a component exists there, use it. If you need a variant that doesn't exist, file a DS Proposal.

5. **NEVER override governance rules U1-U19.** These rules define interaction states, text behavior, positioning, animation, and component structure. They apply to every screen, every feature, every product. No exceptions.

6. **ALWAYS validate against the DS before finalizing any feature spec.** Run the ds-validator skill on every UI specification before it's considered complete.

7. **ALWAYS use DS components as-is.** Don't wrap them with custom styles that change their appearance. Don't override their responsive behavior. Don't skip their accessibility requirements.

## How to Reference the DS

When building features, use the DS registries as your component catalog:

```
# Check available components
Read ai-component-registry.json from the DS repo

# Check interaction contracts
Read ai-interaction-contracts.json from the DS repo

# Check motion/animation rules
Read ai-motion-system.json from the DS repo

# Check responsive behavior
Read ai-responsive-system.json from the DS repo

# Check mobile touch alternatives
Read ai-mobile-touch-model.json from the DS repo

# Check governance lifecycle
Read ai-governance-lifecycle.json from the DS repo
```

## DS Proposal Process

When a feature needs something the DS doesn't provide (new component, new token, new pattern), do NOT build it yourself. Instead:

1. Create a DS Proposal document with:
   - What is needed (component, token, pattern)
   - Why it's needed (which feature, which user story)
   - Suggested behavior and variants
   - Which existing DS components are closest

2. Save the proposal as `DS-PROPOSAL-{feature-name}.md` in your product repo

3. The DS Owner will review, approve, and implement it in the DS repo. Only after the DS is updated can you use the new component.

4. Until the proposal is approved, use the closest existing DS component as a placeholder and document the gap.

## Brand Configuration

All products must specify brand context. Set these on the root HTML element:

```html
<!-- WideBot (Enterprise/Government) - WCAG AAA -->
<html dir="ltr" data-brand="widebot" data-theme="light">

<!-- Hulul (SMB) - WCAG AA -->
<html dir="ltr" data-brand="hulul" data-theme="light">
```

Supported combinations:
- data-brand: "widebot" | "hulul"
- data-theme: "light" | "dark"
- dir: "ltr" | "rtl"

## Product Development Workflow

1. **Plan**: Define the feature using erp-pm-agent or pm-toolkit
2. **Specify**: Generate UI specs using ui-feature-builder (reads DS automatically)
3. **Preview**: Visualize with ui-feature-previewer to verify DS compliance
4. **Validate**: Run ds-validator to check all 19 governance rules
5. **Build**: Developers implement using the validated specs
6. **Review**: ptr-agent reviews PRD completeness and platform compatibility

## Available Skills for Product Work

| Skill | Purpose | DS Access |
|-------|---------|-----------|
| erp-pm-agent | ERP module planning and feature definition | Reads DS for component availability |
| pm-toolkit | PRD writing with user stories and Gherkin scenarios | References DS governance rules |
| ui-feature-builder | Generates UI feature specs from DS components | Reads DS registries (read-only) |
| ui-feature-previewer | Visual preview of feature specs | Renders using DS tokens (read-only) |
| ptr-agent | PRD review and platform compatibility check | Validates against DS standards |
| task-builder | Converts stories to developer tasks | Maps to DS component implementations |

## What You CAN Do

- Read any DS file for reference
- Use DS components in your feature specs
- Generate UI specs that reference DS components
- Preview features using DS tokens
- Validate your work against DS rules
- File DS Proposals for missing components
- Build product features within DS constraints

## What You CANNOT Do

- Modify any DS file
- Push to the DS repo
- Create custom tokens or components outside the DS
- Override governance rules
- Skip validation
- Use raw design values (hex colors, pixel sizes) instead of tokens
