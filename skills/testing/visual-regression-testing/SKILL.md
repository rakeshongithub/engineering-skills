# Visual Regression Testing

## Purpose

Detect unintended visual changes while preserving intentional design, responsive, accessibility, and content behavior.

## When to Use

- Protecting shared components or design systems
- Reviewing layout, typography, theme, and responsive changes
- Supporting visual contracts across browsers and viewports
- Preventing styling regressions in critical journeys

## When NOT to Use

- As a replacement for functional or accessibility testing
- For pixel comparison without controlling fonts, data, and rendering conditions
- For snapshotting every page without risk or ownership

## Inputs

- Visual baselines, component states, and critical journeys
- Browser, viewport, font, theme, locale, and device policy
- Dynamic-content and animation behavior
- Review ownership, tolerance, and baseline-update rules

## Expected Outputs

- Prioritized visual coverage map
- Stable capture configuration and masking policy
- Review and baseline-approval process
- Visual regression findings and remediation plan

## Workflow

1. Identify high-value visual contracts and states.
2. Control fonts, data, time, animations, viewport, theme, and browser conditions.
3. Capture representative component, page, and journey states.
4. Compare with appropriate thresholds and inspect differences semantically.
5. Classify changes as intended, regression, environment noise, or baseline defect.
6. Approve, update, or reject baselines with ownership and rationale.
7. Track recurring visual failures and improve component or environment controls.

## Decision Framework

Use component-level captures for broad state coverage and journey-level captures for layout and composition risk. Mask only truly dynamic regions; excessive masking hides regressions. Baselines are reviewed artifacts, not automatically accepted output.

## Quality Checklist

- [ ] Rendering conditions are deterministic
- [ ] Important states, themes, and viewports are represented
- [ ] Dynamic areas are controlled or narrowly masked
- [ ] Baseline changes require human review and rationale
- [ ] Visual checks complement functional and accessibility checks
- [ ] Failure artifacts identify the changed region and commit

## Common Mistakes

- Accepting all visual diffs to keep CI green
- Masking timestamps, prices, or user content too broadly
- Comparing screenshots from inconsistent fonts or browsers
- Treating a visual match as proof of usability

## Related Skills

- **Requires:** e2e-test-design, frontend-testing-strategy
- **Works with:** component-design, responsive-design, frontend-accessibility-review
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

Visual regression testing is successful when meaningful visual contracts are protected with low noise and intentional changes remain easy to review.
