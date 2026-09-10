# Frontend Performance Analysis Examples

## Slow Dashboard

Baseline shows a 2.5 MB initial JavaScript payload and long main-thread work on mid-range laptops. Split route code, defer non-critical charts, reduce table data, and verify interaction latency after each change.

## Image-heavy Catalog

Measure largest contentful paint and layout shifts on a slow mobile network. Use responsive image sources, reserve dimensions, lazy-load below-the-fold media, and confirm that text remains available before images load.
