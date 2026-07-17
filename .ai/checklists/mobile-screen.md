# Checklist: Mobile Screen

> Verifiable items for building a mobile screen.

## Pass Criteria

- [ ] Uses the design system / tokens (no ad-hoc styling) (`../skills/mobile/mobile-design-system`).
- [ ] Navigation wired with typed params; protected if it requires auth (`mobile-navigation`).
- [ ] Server state fetched/cached correctly; loading/error/empty states handled (`mobile-server-state`).
- [ ] Forms use the form library with validation; client validation is UX, server enforces (`mobile-forms`, `mobile-validation`).
- [ ] Accessibility: labels/roles, focus order, adequate touch targets, dynamic type (`mobile-accessibility`).
- [ ] Authorization-denied and error states render safely (no leak, no crash).
- [ ] Component/interaction tests for the screen's behavior (`mobile-component-testing`).

## Fail / Stop

- Ad-hoc styling; missing error/denied states; no accessibility; unvalidated inputs.

## Related

Skills: `../skills/mobile/`. Reference: `../references/mobile/screens/`.
