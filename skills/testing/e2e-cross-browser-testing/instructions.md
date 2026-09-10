# E2E Cross-Browser Testing Instructions

1. Establish supported platforms from policy and real user distribution.
2. Rank browsers and devices by reach, criticality, and defect history.
3. Select representative journeys and states for each matrix tier.
4. Include viewport, touch, keyboard, locale, timezone, reduced-motion, and network variations where relevant.
5. Run a fast smoke tier and broader scheduled or release regression tier.
6. Classify failures as product, browser, environment, or unsupported-platform behavior.
7. Update the matrix as traffic, risk, or support policy changes.

Keep pull-request coverage small and high-signal; reserve broad matrices for scheduled or release runs.
