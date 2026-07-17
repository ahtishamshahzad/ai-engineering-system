---
name: dependency-security
description: Use to assess third-party dependency risk from a security angle — known vulnerabilities, supply-chain integrity (lockfiles, pinning, provenance), typosquats/malicious packages, and CI enforcement — driving prioritized upgrades. The security lens on dependencies; dependency-audit is the general inventory.
---

# Dependency Security

## Purpose

Reduce the risk that a dependency compromises the system: find known-vulnerable packages, harden the supply chain (integrity, pinning, provenance), catch malicious/typosquatted packages, and enforce it in CI — prioritized by real exploitability. The general inventory is `../../dependency-audit`; this is its security lens.

## When to Use

- Auditing dependencies for security, in `../../security-review`, or setting up supply-chain defenses.
- **Not** for general dependency inventory/licensing (`../../dependency-audit`) or secret leaks (`secrets-audit`).

## Inputs

- The dependency tree + lockfiles across apps (`../../dependency-audit` inventory).
- CI/build setup (`../../devops/ci-cd`, `../../devops/github-actions`), image base (`../../devops/docker-foundation`).

## Discovery Questions

- What **known vulnerabilities** exist in the tree (direct + transitive), and which are actually reachable/exploitable in this app?
- Are installs **reproducible and pinned** (committed lockfile, frozen installs, pinned base images/actions)?
- Any signs of **malicious or typosquatted** packages (suspicious names, install scripts, recent ownership changes)?
- Is dependency scanning **enforced in CI**, and are updates a managed process?

## Responsibilities

- **Vulnerability assessment**: audit direct + transitive deps against advisory databases; **prioritize by reachability + severity** — a critical CVE in unused code path is lower than a medium one in the request path (don't blindly chase every advisory, but never ignore reachable criticals).
- **Supply-chain integrity**: committed lockfiles, **frozen/`ci` installs** (`../../devops/pnpm-workspaces`/`npm-workspaces`), pinned base images and CI actions (`../../devops/docker-foundation`, `../../devops/github-actions`) — floating versions are an injection surface; provenance/signature checks where available.
- **Malicious-package detection**: watch for typosquats, unexpected install scripts, packages with sudden maintainer changes or huge unexplained dependency additions; scrutinize new dependencies in review.
- **Enforcement**: dependency scanning as a CI job (`../../devops/ci-cd`), a managed update cadence, and review discipline for adding dependencies (`../../code-review`).
- Drive **prioritized upgrades/removals**; record accepted risks with justification.

## Required Workflow

1. Inventory direct + transitive deps + lockfile state (`../../dependency-audit`).
2. Assess known vulnerabilities; prioritize by reachability × severity.
3. Check supply-chain integrity (pinning, frozen installs, provenance).
4. Scan for malicious/typosquatted packages.
5. Recommend prioritized upgrades/removals; wire CI scanning + update process.

## Decision Rules

- Prioritize by exploitability in *this* app — reachable criticals first; don't drown in unreachable advisories, but never wave one through unassessed.
- Pin everything that runs your build/deploy (packages, base images, CI actions) — floating refs are supply-chain risk.
- A new dependency is a trust decision; scrutinize provenance and install scripts before adding.
- Accepted risks are recorded with justification and an owner, not silently ignored.

## Rules

- Lockfiles committed; installs frozen; build inputs pinned.
- Dependency scanning enforced in CI, not run ad hoc.
- Reachable critical/high vulns are fixed or explicitly risk-accepted with justification.

## Anti-Patterns

- Ignoring transitive dependencies (most vulns live there).
- Floating versions / unpinned base images and CI actions.
- Blindly bumping to satisfy a scanner without checking reachability or breakage.
- Adding dependencies without vetting provenance/install scripts.
- Audit run once manually, never enforced in CI.

## Validation Checklist

- [ ] Direct + transitive vulns assessed, prioritized by reachability × severity.
- [ ] Lockfiles committed; frozen installs; pinned images/actions.
- [ ] Malicious/typosquat scan done.
- [ ] Prioritized upgrades/removals recommended.
- [ ] CI scanning + managed update cadence wired; accepted risks recorded.

## Definition of Done

A recorded dependency security assessment — reachability-prioritized vulnerabilities, hardened supply chain (pinned, frozen, provenance-checked), malicious-package scan — with prioritized remediation, enforced CI scanning, and any accepted risk justified.

## Related Skills

`../../dependency-audit`, `secrets-audit`, `../../devops/ci-cd`, `../../devops/github-actions`, `../../devops/docker-foundation`, `../../devops/pnpm-workspaces`, `../../devops/npm-workspaces`, `../../security-review`, `security-regression-testing`.

## Related Knowledge

`../../../knowledge/` (dependency tree, accepted risks).

## Related References

`../../../references/security/` (supply-chain checklists, when populated).

## Context Loading Guidance

- **Requires:** dependency tree + lockfiles, CI/build setup, image bases.
- **Does not require:** app feature logic, license detail (that's `../../dependency-audit`).
- **May load:** `../../dependency-audit`, `../../devops/ci-cd`.
- **Stop when:** prioritized findings + enforced scanning are recorded.

## Token Efficiency Guidance

The vuln table (package, path, severity, reachable?, fix) plus the pinning checklist are the artifacts; don't dump the full tree.
