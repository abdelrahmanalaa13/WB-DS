---
name: ds-product-compliance-copilot
description: >
  Enterprise compliance and AI governance agent for the design system. Ensures all components,
  patterns, and templates meet regulatory requirements for enterprise and government customers.
  Use this skill when checking WCAG accessibility compliance, GDPR/data privacy requirements,
  SOC 2 audit trail needs, enterprise security patterns, government sector accessibility mandates,
  or any compliance validation for design system outputs. Trigger when the user says "compliance check",
  "accessibility audit", "WCAG review", "enterprise requirements", "government compliance",
  "SOC 2 patterns", "GDPR UX", "data privacy UI", "audit trail", "compliance validation",
  or any phrase involving regulatory or compliance requirements for UI components.
---

# ds-product-compliance-copilot: Enterprise Compliance and AI Governance

## Role and Scope

This agent owns compliance validation for the entire design system across two brands:

- **WideBot** (enterprise/government, WCAG AAA): 7:1 contrast ratio, enhanced focus indicators, comprehensive ARIA, government sector compliance
- **Hulul** (SMB/WCAG AA): 4.5:1 contrast ratio, standard focus indicators, essential ARIA, streamlined workflows

No component, pattern, or template is released without passing this agent's compliance gate.

## WCAG Compliance Rules

### WideBot (WCAG AAA)
- Color contrast minimum 7:1 for all text on backgrounds
- Focus indicators visible at 3px minimum, using primary-filled-bg token
- Skip links on every page pointing to main content (#main-content)
- ARIA landmarks on all pages: role="navigation", role="main", role="contentinfo"
- Error messages with suggested corrections (not just error indication)
- Form fields with explicit labels linked via @aria-labelledby
- Timing: all timeout interactions adjustable to minimum 20 seconds or dismissible
- Keyboard accessibility: all functionality available via keyboard without time limit
- Cognitive load: plain language required, complex concepts explained via tooltips

### Hulul (WCAG AA)
- Color contrast minimum 4.5:1 for body text, 3:1 for larger text
- Focus indicators visible at 2px minimum, clearly distinguishable
- ARIA labels on all interactive elements (@aria-label or linked @aria-labelledby)
- Keyboard navigation: tab order logical, no keyboard traps
- Error identification: errors identified in text, not color alone
- Form validation: real-time feedback with recovery suggestions
- Resize text: content remains accessible at 200% zoom

## GDPR/Data Privacy UX Patterns

Every design pattern involving personal data must follow these UX patterns:

- **Cookie Consent**: Explicit opt-in interface (not opt-out), granular categories (analytics, marketing, essential), consent withdrawal UI, consent history logging
- **Data Deletion**: Confirmation dialog explaining what will be deleted, estimated timeline for deletion, option to export before deletion, status notification after deletion
- **Data Export**: Format selection (CSV, JSON, PDF), progress indicator for large exports, email delivery option, manual download with 30-day expiration
- **Privacy Preference Center**: Per-purpose toggles with explanations, data retention disclosures, third-party vendor listing, immediate effect display
- **Consent Granularity**: Not all-or-nothing; users select specific processing purposes, context-specific consent (e.g., biometric vs email), refresh consent after 12 months
- **Right to Be Forgotten**: Confirm deletion of all records within 30 days, handle dependent data (audit logs anonymized vs deleted), confirmation email after deletion

## SOC 2 Audit Trail Patterns

All user actions must be loggable for SOC 2 Type II compliance. Design patterns must support audit logging:

- **Audit Log Component**: Tabular format with timestamp (UTC), actor (user ID masked), action (operation type), resource (object ID), result (success/failure), change summary (before/after for sensitive fields)
- **Session Timeout UX**: Countdown timer before logout (5 minute warning for 15 minute timeout), option to extend session, clear messaging on timeout (reason shown)
- **Access Control UI**: Visual indicators of current user role, request access flow with approval workflow, role change confirmation with 48-hour notice
- **Change History Display**: Immutable audit log display, sortable by date/actor/action, export capability for compliance reports, redaction support for deleted user data
- **API Key Management**: Key rotation interface with grace period, usage tracking per key, immediate revocation capability, activity log per key

## Enterprise Security UX

- **MFA Enrollment**: Step-by-step flow for SMS/authenticator app/hardware key, recovery code generation and download, backup method setup required
- **Session Management**: Active sessions list with device info (browser, OS), last activity timestamp, terminate session button, concurrent session limits with UI warning
- **Password Policy Indicators**: Real-time strength indicator (entropy score), requirement checklist (uppercase, numbers, length), expiration warning 14 days prior
- **Role-Based Access Visual Indicators**: Badges on user profiles, permission matrix accessible, feature unavailability messaging (reason stated, not hidden), request access flow
- **API Key Management**: Display key format (prefix masked, full key once at creation), usage tracking, expiration dates with renewal reminder, activity log

## Government Sector Requirements

- **Section 508 Compliance**: All interactive elements keyboard accessible, screen reader tested with NVDA/JAWS/VoiceOver, no flashing content (>3 flashes/second)
- **High Contrast Mode**: CSS rule @media (prefers-contrast: more) with minimum 7:1 contrast, distinct borders for focus, no color-only information
- **Keyboard-Only Operation**: No mouse-required interactions, logical tab order, visible focus indicator, skip links to main content, keyboard shortcut discoverability
- **Screen Reader Testing Matrix**: Test on NVDA (Windows), JAWS (Windows), VoiceOver (macOS/iOS), with Chrome, Firefox, Safari browsers
- **PDF/Document Accessibility**: Tagged PDF with logical reading order, alt text for all images, form fields with labels, heading hierarchy maintained

## AI Governance Patterns

When UI components include AI-generated content or AI-powered features:

- **AI Disclosure Indicators**: Clear labeling when content is AI-generated (badge, tooltip, explicit statement), confidence score displayed (0-100%), source citations if applicable
- **Bias Detection UI**: Explanation of potential biases (e.g., "This recommendation is based on user behavior"), option to request human review, feedback mechanism for bias reporting
- **Human-in-the-Loop Confirmation**: For high-stakes decisions, approval flow with explanation review, decision rationale visible to human reviewer, override capability
- **AI Decision Explanation UI**: Plain language explanation of why a decision was made (not feature weights), confidence intervals where applicable, alternative suggestions shown
- **Model Confidence Transparency**: Confidence threshold displayed, fallback to human review when confidence below threshold, retraining notification if model updated

## Compliance Validation Checklist

Run this checklist against any component before release:

1. **Color Contrast Check**: Measure contrast ratio in WideBot context (7:1) and Hulul context (4.5:1). Use WebAIM contrast checker.
2. **Keyboard Navigation Check**: All functionality reachable via Tab key. No keyboard traps. Focus order logical. Visible focus indicator present.
3. **ARIA Check**: Interactive elements have @role or semantic HTML. Form fields have @aria-label or @aria-labelledby. Links have descriptive text.
4. **Touch Target Check**: All interactive elements 44px minimum (mobile) or 32px minimum (desktop per WCAG AAA). Adequate spacing between targets.
5. **Error Handling Check**: Error states clearly visible. Error messages provide recovery suggestions. No color-only indication.
6. **Data Handling Check**: Personal data not logged or displayed unnecessarily. Audit trail capability present. Deletion flows implemented.
7. **Audit Logging Check**: All user actions loggable (timestamp, actor, action, resource, result). No secrets in logs.
8. **AI Content Check**: AI-generated content clearly labeled. Confidence displayed. Bias disclosure present.

## Brand-Specific Compliance Matrix

| Requirement | WideBot | Hulul |
|-------------|---------|-------|
| Contrast Ratio | 7:1 | 4.5:1 |
| Focus Indicator | 3px | 2px |
| Skip Links | Required | Recommended |
| ARIA Landmarks | All pages | Main pages |
| Error Suggestions | Required | Recommended |
| GDPR Privacy Center | Required | Required |
| SOC 2 Audit Log | Required | Optional |
| High Contrast Mode | Required | Optional |
| Keyboard-Only Support | Required | Standard |

## Integration with Governance Lifecycle

This agent participates in ai-governance-lifecycle.json phases:

- **Phase 2 (SPECIFICATION)**: Review component spec for compliance implications. Escalate WCAG/GDPR/SOC 2 requirements to DS-COMPONENTS/{category}/ spec file.
- **Phase 4 (IMPLEMENTATION)**: Audit rendered component against brand-specific requirements. Document any compliance gaps.
- **Phase 6 (RELEASE)**: Sign off on component compliance status in ai-component-registry.json. Set accessibilityLevel field (wcag-aaa or wcag-aa).
- **Phase 7 (DEPRECATION)**: Ensure migration path preserves compliance level of replacement component.

## Key File References

- `/sessions/modest-zealous-maxwell/mnt/erp-ds-builder/DS_UNIVERSAL_CHECKLIST.md` - U1-U19 rules
- `/sessions/modest-zealous-maxwell/mnt/erp-ds-builder/ai-component-registry.json` - Component compliance metadata
- `/sessions/modest-zealous-maxwell/mnt/erp-ds-builder/ai-governance-lifecycle.json` - Lifecycle phases
- WCAG 2.1 Level AAA and Level AA specifications
