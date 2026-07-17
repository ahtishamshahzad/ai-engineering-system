# Checklist: Mobile Foundation

> Verifiable items when standing up a mobile app foundation (`../skills/mobile/README.md`).

## Pass Criteria

- [ ] Runtime decided on real native needs — **Expo vs RN CLI**, not defaulted (`../skills/mobile/mobile-stack-selection`).
- [ ] Foundation set up per the chosen runtime (`expo-foundation` / `react-native-cli-foundation`).
- [ ] Navigation structure defined; protected routes planned (`mobile-navigation`).
- [ ] Design system / tokens / theme baseline in place (`mobile-design-system`, `mobile-theme`).
- [ ] State categories separated (local/form/server/persisted) — not everything in one store (`mobile-state-management`).
- [ ] Environment config split dev/staging/prod; **no secrets in the client bundle** (`mobile-environment-config`).
- [ ] Server enforces authorization; client checks are UX only.

## Fail / Stop

- CLI chosen with no concrete native requirement; secrets in the bundle; server enforcement missing.

## Related

Skills: `../skills/mobile/`. Reference material: `../references/mobile/`.
