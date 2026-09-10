# Mobile-Native E2E Testing

## Purpose

Validate mobile-native user journeys across device capabilities, lifecycle events, permissions, navigation, and platform behavior.

## When to Use

- Testing iOS or Android applications end to end
- Validating deep links, permissions, push notifications, biometrics, or offline behavior
- Releasing changes that interact with camera, files, location, or native payment flows
- Investigating device- or platform-specific defects

## When NOT to Use

- For responsive web testing alone
- For unit or native-module tests in isolation
- For exhaustive physical-device coverage without risk justification

## Inputs

- Mobile user journeys and acceptance criteria
- Supported OS versions, devices, orientations, and network states
- Native capabilities, permissions, app lifecycle, and backend dependencies
- Test data, device lab, simulator, and release constraints

## Expected Outputs

- Mobile journey and device-risk matrix
- Native capability and permission scenarios
- Automation, fixture, and environment plan
- Release evidence and unsupported-platform policy

## Workflow

1. Identify critical journeys and platform-specific capabilities.
2. Map lifecycle, permissions, deep links, interruptions, connectivity, and OS behavior.
3. Select representative simulators, emulators, and physical devices by user risk.
4. Define isolated data, accounts, app installation, reset, and backend dependencies.
5. Automate user-visible journeys with stable accessibility or semantic selectors.
6. Validate offline, background, rotation, notification, and permission transitions.
7. Capture device logs, screenshots, video, network, and native diagnostics.
8. Connect smoke and release suites to the mobile delivery pipeline.

## Decision Framework

Use simulators for speed and broad deterministic coverage; use physical devices for hardware, OS, rendering, sensors, performance, and permission behavior that simulators cannot represent. Keep the matrix risk-based rather than device-count based.

## Quality Checklist

- [ ] Supported OS and device policy is explicit
- [ ] Permission and lifecycle transitions are tested
- [ ] Offline, interruption, orientation, and deep-link behavior are covered
- [ ] Test accounts and app state reset deterministically
- [ ] Device and native failure artifacts are retained
- [ ] Release gates distinguish simulator and physical-device evidence

## Common Mistakes

- Treating mobile as a smaller browser
- Testing only the latest OS on one simulator
- Leaving permissions or app state from a previous test
- Ignoring backgrounding, network changes, and interrupted flows

## Related Skills

- **Requires:** e2e-test-design, e2e-test-environment, e2e-cross-browser-testing
- **Works with:** e2e-test-automation, e2e-test-data-management, e2e-test-reliability
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

A mobile E2E program is successful when supported users' critical journeys remain reliable across representative devices, capabilities, and lifecycle states.
