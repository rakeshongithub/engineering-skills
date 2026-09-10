# Responsive Design

## Purpose

Design frontend layouts and interactions that remain usable across viewport sizes, input methods, content lengths, and device capabilities.

## When to Use

- Designing a new responsive page or feature
- Reviewing mobile and tablet behavior
- Supporting touch, keyboard, pointer, and reduced-motion preferences
- Fixing overflow, layout shift, or content-collision issues

## When NOT to Use

- For desktop-only internal tools with verified fixed hardware
- For visual branding decisions without layout behavior
- For backend or API responsiveness

## Inputs

- User journeys and priority content
- Target viewport, browser, and input-method matrix
- Content length, localization, and zoom requirements
- Design tokens, layout primitives, and performance constraints

## Expected Outputs

- Responsive layout rules and breakpoint rationale
- Content priority and reflow behavior
- Interaction adaptations for touch, keyboard, and pointer
- Visual and automated validation plan

## Workflow

1. Define content priority and minimum usable task width.
2. Design the smallest viewport and primary task flow first.
3. Establish fluid layout rules before adding breakpoints.
4. Define reflow, wrapping, truncation, scrolling, and overflow behavior.
5. Adapt controls for touch targets, keyboard access, zoom, and reduced motion.
6. Validate representative content at mobile, tablet, desktop, zoomed, and localized states.
7. Record exceptions as explicit component or page rules.

## Decision Framework

Use breakpoints where content or interaction becomes unusable, not where device labels change. Prefer fluid grids, intrinsic sizing, and content-driven wrapping. Never hide essential actions solely because the viewport is narrow.

## Quality Checklist

- [ ] Primary task works at narrow and wide viewports
- [ ] Text wraps without overlap or clipping
- [ ] Controls have usable touch and keyboard targets
- [ ] Zoom, localization, and long content are considered
- [ ] Orientation and reduced-motion behavior are defined
- [ ] No layout shift occurs during loading or state changes

## Common Mistakes

- Designing only at one desktop width
- Using fixed heights for dynamic content
- Hiding important information on mobile without an alternative
- Treating responsive behavior as a final CSS patch

## Related Skills

- **Requires:** requirements-analysis, component-design
- **Works with:** frontend-architecture, frontend-accessibility-review, frontend-performance-analysis
- **Commonly followed by:** frontend-testing-strategy

## Evaluation Criteria

A responsive design is successful when users can complete the primary task across supported sizes and input methods without overlap, clipping, unusable controls, or unexpected state loss.
