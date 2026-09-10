# Frontend Accessibility Review

## Purpose

Review frontend experiences for operability, understandability, and robustness against WCAG 2.1 AA expectations and product-specific accessibility requirements.

## When to Use

- Before releasing a new page, component, or workflow
- When a product must meet accessibility or procurement requirements
- After changing navigation, forms, dialogs, tables, or interactive widgets
- When defects are reported by keyboard or assistive-technology users

## When NOT to Use

- As a substitute for inclusive discovery or user research
- As a one-time automated scan with no manual verification
- For backend accessibility or document compliance outside the UI

## Inputs

- User journeys, designs, and component implementation
- Supported browsers, assistive technologies, and input methods
- Content, error, loading, and localization states
- Existing automated scan results and known issues

## Expected Outputs

- Findings mapped to user impact and success criteria
- Keyboard, semantics, focus, contrast, motion, and announcement checks
- Remediation recommendations and regression tests
- Release risk and verification status

## Workflow

1. Identify critical tasks and users with access needs.
2. Inspect semantic structure, headings, landmarks, names, roles, and values.
3. Execute every task with keyboard only, including focus order and recovery.
4. Test zoom, contrast, reduced motion, text resizing, and responsive states.
5. Use representative screen-reader and browser combinations for dynamic content.
6. Review forms, errors, dialogs, tables, and asynchronous announcements.
7. Record findings with reproduction steps, impact, criterion, owner, and verification method.

## Decision Framework

Block release for failures that prevent a critical task, expose sensitive content, trap focus, or remove a required interaction. Prioritize barriers by user impact and task criticality, not by scanner count.

## Quality Checklist

- [ ] All critical flows work with keyboard only
- [ ] Focus is visible, logical, and restored after overlays
- [ ] Controls have accessible names and state announcements
- [ ] Contrast, zoom, text resizing, and reduced motion are supported
- [ ] Form errors are associated, specific, and recoverable
- [ ] Automated checks are supplemented by manual verification

## Common Mistakes

- Using ARIA to compensate for incorrect native semantics
- Testing only the happy path and default color theme
- Removing focus outlines without an equivalent indicator
- Treating an automated scan as proof of accessibility

## Related Skills

- **Requires:** component-design, responsive-design
- **Works with:** frontend-testing-strategy, frontend-security-review, frontend-feature workflow
- **Commonly followed by:** production-readiness

## Evaluation Criteria

A review is successful when users can complete critical tasks across supported input methods and assistive technologies, with findings reproducible and regression coverage identified.
