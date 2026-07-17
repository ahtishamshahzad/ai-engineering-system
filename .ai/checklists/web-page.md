# Checklist: Web Page

> Verifiable items for building a web page/route.

## Pass Criteria

- [ ] Uses the design system / components; consistent with the app's UI conventions.
- [ ] Loading / error / empty states handled; server state fetched and cached correctly.
- [ ] Forms validated (client = UX, server enforces); accessible labels and errors.
- [ ] **Accessibility:** semantic markup, keyboard navigation, focus management, contrast.
- [ ] **Web security:** output encoded (no unsanitized HTML injection); no secrets in the client bundle (`../skills/security/web-security`).
- [ ] SEO/meta handled where the page is public (title, description, structured data as relevant).
- [ ] Authorization-denied and error states render safely; server enforces access.
- [ ] Component/interaction tests; critical journeys covered by Playwright only if warranted.

## Fail / Stop

- Unsanitized user HTML; bundle secrets; missing accessibility; no error/denied states.

## Related

Skills: `../skills/testing/` (component, playwright-e2e), `../skills/security/web-security`. Reference: `../references/web/pages/`.
