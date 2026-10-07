# Security Policy

Thank you for looking. Reporting privately is the most useful thing you can do with a
vulnerability, and it is appreciated.

This is the organisation-wide default. A repository that ships its own `SECURITY.md`
overrides this one, and that copy is what its **Security** tab shows.
[`vibey`](https://github.com/the-vibey-project/vibey) ships its own, because its threat
model is specific to running autonomous engines against a working tree.

## Reporting a vulnerability

**Do not open a public issue.** Use GitHub's private reporting on the affected
repository: **Security → Report a vulnerability**. If that is unavailable, email
**[adam@matthewsteinberger.com](mailto:adam@matthewsteinberger.com)** with `SECURITY` in the subject.

Please include: the repository and version, what an attacker gains, the smallest
reproduction you have, and whether it is already public. A partial report is still
welcome; say what you could not establish.

**What to expect:** acknowledgement within 3 working days; an assessment with a
severity and a rough timeline within 10. You will be credited in the release notes
unless you ask not to be. This is a small project maintained by one person: there is
no bounty, and no SLA beyond making a genuine effort.

Please give a reasonable window to ship a fix before disclosing publicly.

## Supported versions

Only the latest release of each published package receives security fixes:
[`vibey-engine`](https://pypi.org/project/vibey-engine/), which carries the engine, the
`*loop` runners and the `vibey-gh`, `vibey-skills` and `vibey-bootstrap` tools, and
[`krypton-app`](https://pypi.org/project/krypton-app/), which carries the apps. There
are no long-term support branches: pin a version when you need reproducibility, and
upgrade to take a fix.

## What is in scope

- The published packages, `vibey-engine` and `krypton-app`, and everything they carry.
- The GitHub Actions workflows these projects render into a repository that adopts
  them, in particular anything reachable from a `pull_request_target` trigger, which
  runs with the base repository's permissions.
- Secret handling: anything that could cause a token, key or credential to be written
  to a log, an artifact, a published page, or a commit.

## What is not

- Vulnerabilities in the third-party engines and model servers themselves (Claude
  Code, Codex, Ollama and the models it serves). Report
  those to their vendors.
- Anything that requires an attacker to already have write access to the repository
  or to the machine. These tools execute code from the working tree by design; a
  contributor who can change that tree can run code, and that is a property of the
  tool rather than a flaw in it.
- Findings from a scanner with no demonstrated impact.

## Two properties worth knowing

**Execution is the product.** These projects run agents that edit and execute code in
a working tree. Run them against repositories you trust, on a machine you are willing
to let them change. Isolation is per-worktree by default.

**Provenance is enforced, not advisory.** Every commit carries an attribution trailer
and every source file a header, checked in CI. That is an integrity and authorship
control: it tells you what produced a change. It is not an access control, and it does
not authenticate anyone.
