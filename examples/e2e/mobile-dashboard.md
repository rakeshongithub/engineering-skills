# E2E: Mobile Dashboard

## Journey and Risks

A mobile user signs in, views a summary metric, filters a dashboard, and opens a detail view. Risks include unusable touch targets, layout overflow, slow loading, chart accessibility, and state loss during navigation.

## E2E Plan

- Run the primary journey on mobile WebKit and a desktop keyboard case.
- Use a realistic slow network profile and representative dashboard data.
- Assert summary content, filter persistence, loading recovery, and detail navigation.
- Verify charts have an accessible alternative and that content does not overlap at narrow widths.
- Keep the broad browser and viewport matrix in the scheduled regression tier.

## Quality Gates

- The primary insight is usable without loading every desktop visualization.
- Touch and keyboard users can complete the journey.
- Performance and layout budgets are measured on target hardware.
- A network failure provides recovery without losing the selected filters.
