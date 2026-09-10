# Frontend State Management

## Purpose

Choose and structure frontend state so ownership, lifetime, synchronization, and update behavior remain understandable and testable.

## When to Use

- Introducing shared client state
- Choosing between local, URL, form, server, and global state
- Debugging stale data, unnecessary renders, or state duplication
- Migrating state libraries or data-fetching patterns

## When NOT to Use

- For a static component with no meaningful state
- For backend data-model design alone
- For choosing a library before understanding state ownership

## Inputs

- User journeys and interaction transitions
- Data sources, cache requirements, and mutation behavior
- Route, form, session, and persistence requirements
- Performance, testing, and team constraints

## Expected Outputs

- State inventory with owner and lifetime
- Source-of-truth and synchronization rules
- Store, cache, form, and URL strategy
- Update, invalidation, error, and recovery behavior

## Workflow

1. Inventory every state value and classify it as server, URL, form, local UI, session, or shared client state.
2. Assign the narrowest responsible owner and define its lifetime.
3. Identify derived values and calculate them instead of duplicating them.
4. Define loading, stale, optimistic, failed, and recovered transitions.
5. Select APIs or libraries that match the ownership and synchronization needs.
6. Define selectors, boundaries, reset behavior, and test seams.
7. Verify render behavior and document migration or adoption rules.

## Decision Framework

Use local state by default. Put shareable navigation state in the URL, server-owned data in a query/cache layer, and global client state only when multiple distant consumers need the same client-owned value. Avoid two writable sources of truth.

## Quality Checklist

- [ ] Every state value has one clear owner
- [ ] Server data is not copied into an unrelated global store
- [ ] Derived state is not stored redundantly
- [ ] Stale, loading, error, retry, and reset behavior are explicit
- [ ] State updates are observable and testable
- [ ] Persistence and sensitive-data rules are documented

## Common Mistakes

- Putting all state in one global store
- Copying props or server data without invalidation rules
- Using effects to synchronize values that can be derived
- Forgetting reset behavior when routes, accounts, or sessions change

## Related Skills

- **Requires:** frontend-architecture
- **Works with:** component-design, frontend-data-fetching, frontend-testing-strategy
- **Commonly followed by:** frontend-performance-analysis

## Evaluation Criteria

A state design is successful when ownership and transitions are explicit, stale data is handled deliberately, and components remain easy to reason about and test.
