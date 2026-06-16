---
name: fix-dep-audit
description: Resolve dependency security audit findings by preferring minor version upgrades to base packages first, then targeted fixes, verifying with pnpm audit after each change. Use when fixing dependency vulnerabilities or remediating a dependency audit.
---

Fix this dep audit. prefer minor version upgrades to base packages, then specific, targeted fixes if the minor version updates don't work. verify after each change by running:
`pnpm audit --audit-level high --prod`
