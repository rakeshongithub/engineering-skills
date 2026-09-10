# Frontend Performance Analysis Instructions

1. Define critical journeys and budgets for loading, interaction, layout stability, and resource use.
2. Capture lab and real-user baselines across representative devices and networks.
3. Inspect request chains, bundles, images, fonts, third-party scripts, caching, and data volume.
4. Profile rendering, hydration, re-renders, main-thread work, memory, and layout shifts.
5. Rank bottlenecks by user impact, confidence, effort, and regression risk.
6. Measure each change against the baseline and check accessibility and correctness.
7. Add build budgets, runtime telemetry, dashboards, and release regression gates.

Do not optimize a metric without connecting it to a user journey or business outcome.
