# Frontend State Management Instructions

1. Inventory state values and classify each as server, URL, form, local UI, session, or shared client state.
2. Assign one owner and define lifetime, persistence, reset, and synchronization behavior.
3. Remove duplicated or derived state where computation is sufficient.
4. Model loading, stale, optimistic, failed, retrying, and recovered transitions.
5. Select a store, cache, form, or URL mechanism only after ownership is clear.
6. Define selectors, update boundaries, invalidation rules, and test seams.
7. Produce a state map, transition table, and migration or adoption guidance.

Do not add global state to solve a component communication problem that composition can solve.
