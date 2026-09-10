# E2E Cross-Browser Testing

## Purpose

Define and execute a risk-based browser, device, viewport, locale, timezone, and network matrix for E2E coverage.

## When to Use

- Supporting multiple browsers or device classes
- Releasing responsive or browser-sensitive changes
- Investigating browser-specific defects
- Defining the minimum compatibility contract

## When NOT to Use

- For testing one browser-specific implementation detail only
- For replacing responsive or accessibility review
- For exhaustive combinations without user or risk justification

## Inputs

- Supported browser and device policy
- User traffic, analytics, and critical journeys
- Responsive, accessibility, locale, timezone, and network requirements
- CI capacity and release risk tolerance

## Expected Outputs

- Prioritized compatibility matrix
- Journey-to-browser coverage map
- Device, viewport, locale, and network cases
- Exception and unsupported-platform policy

## Workflow

1. Establish supported platforms from product policy and real user distribution.
2. Rank browsers and devices by reach, criticality, and defect history.
3. Select representative journeys and states for each matrix tier.
4. Include viewport, touch, keyboard, locale, timezone, reduced-motion, and network variations where relevant.
5. Run a fast smoke tier and a broader scheduled regression tier.
6. Review failures for product defects, browser defects, environment issues, and unsupported cases.
7. Update the matrix when traffic, risk, or support policy changes.

## Decision Framework

Keep pull-request coverage small and high-signal; run the broader matrix on scheduled or release pipelines. Every unsupported platform should be explicit, communicated, and tested for graceful behavior where possible.

## Quality Checklist

- [ ] Matrix reflects supported users and business risk
- [ ] Critical journeys run on primary browser families
- [ ] Mobile, touch, keyboard, and viewport behavior are represented
- [ ] Locale, timezone, and reduced-motion risks are considered
- [ ] Smoke and regression tiers have clear ownership
- [ ] Unsupported platforms have documented behavior

## Common Mistakes

- Testing browser logos without testing meaningful journeys
- Running every combination on every pull request
- Ignoring mobile input differences
- Treating a browser failure as automatically unsupported

## Related Skills

- **Requires:** e2e-test-design, frontend-testing-strategy
- **Works with:** responsive-design, frontend-accessibility-review, e2e-test-reliability
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

A matrix is successful when it gives justified confidence for supported users without consuming disproportionate CI capacity.
