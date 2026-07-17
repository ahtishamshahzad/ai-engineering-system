# Checklist: Project Discovery

> Verifiable items for intake/requirements. Maps to Gate 1 (`../system/QUALITY_GATES.md`) and `../hooks/before-discovery.md`. Each item is observable — pass only when true.

## Pass Criteria

- [ ] Request classified into one primary type (+ secondary noted).
- [ ] Goals, users, constraints, and non-functional requirements captured.
- [ ] Success criteria are **measurable**.
- [ ] Confirmed facts, assumptions, and open questions are separated into three lists.
- [ ] **Blocking** questions identified; non-blocking items proceed on recorded assumptions.
- [ ] If a codebase exists, it has been **audited** before any change is proposed.
- [ ] No application code written and no stack committed yet.

## Fail / Stop

- Request unclassifiable and no clarifying question can unblock it → ask the user.
- Codebase exists but changes proposed before an audit → audit first.

## Related

Template: `../templates/REQUIREMENTS.md`, `DISCOVERY_QUESTIONS.md`. Skill: `../skills/requirements-analysis`.
