# Security Policy

This is the organisation-wide default. A repository that ships its own
`SECURITY.md` — [`vibey`](https://github.com/the-vibey-project/vibey) does,
because its threat model is specific to running autonomous engines against a
working tree — overrides this one, and that copy is what its **Security** tab
shows.

## Reporting a vulnerability

**Do not open a public issue.** Use GitHub's private reporting on the affected
repository: **Security → Report a vulnerability**. If that is unavailable, email
**adam@matthewsteinberger.com** with `SECURITY` in the subject.

Please include: the repository and version, what an attacker gains, the smallest
reproduction you have, and whether it is already public.

**What to expect:** acknowledgement within 3 working days; an assessment with a
severity and a rough timeline within 10. You will be credited in the release
notes unless you ask not to be. This is a small project maintained by one person
— there is no bounty, and no SLA beyond making a genuine effort.

Please give a reasonable window to ship a fix before disclosing publicly.

## Supported versions

Only the latest released version of each package receives security fixes. These
are pre-1.0 projects on a fast release cadence; there are no long-term support
branches. Pin a version, and upgrade to take a fix.

## What is in scope

- The published packages: `vibey`, `vibey-gh`, `vibey-skills`, `vibey-bootstrap`,
  and the `*loop` runners.
- The GitHub Actions workflows these projects render into a repository that
  adopts them — in particular anything reachable from a `pull_request_target`
  trigger, which runs with the base repository's permissions.
- Secret handling: anything that could cause a token, key or credential to be
  written to a log, an artifact, a published page, or a commit.

## What is not

- Vulnerabilities in the autonomous engines themselves (Claude Code, Codex,
  Cursor, Antigravity, Qwen) — report those to their vendors.
- Anything that requires an attacker to already have write access to the
  repository or to the machine. These tools execute code from the working tree
  by design; a contributor who can change that tree can run code, and that is a
  property of the tool rather than a flaw in it.
- Findings from a scanner with no demonstrated impact.

## Two properties worth knowing

**Execution is the product.** These projects run agents that edit and execute
code in a working tree. Run them against repositories you trust, on a machine
you are willing to let them change. Isolation is per-worktree by default.

**Provenance is enforced, not advisory.** Every commit carries an attribution
trailer and every source file a header, checked in CI. That is an integrity and
authorship control — it tells you what produced a change. It is not an access
control, and it does not authenticate anyone.
