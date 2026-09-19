# The Vibey Project

**Open-source infrastructure for autonomous software delivery that remains inspectable.**

An AI coding session can write code. Delivery is the harder system: the work has to
survive a crashed agent, an exhausted vendor account, a bad handoff, and the gap between
what a model claims and what the repository can prove. Vibey is the conductor for that
system.

## Start here

### 🎼 [vibey](https://github.com/the-vibey-project/vibey)

Vibey handles intake and then runs a six-phase delivery machine:
**design → build → review → deploy design → deploy execute → deploy review**.
The visual-design interstitial and deployment stage set are opt-in. It runs locally on
macOS or Linux, uses PostgreSQL for durable state, and has no cloud control plane you
must trust with the working tree.

One `pip install vibey` delivers the conductor, five engine runners, the GitHub
automation, the skills marketplace, and the bootstrap tooling. Local engines are
preferred first when enabled; paid engines remain the fallback when the sovereign lane
is unavailable.

The system is built around a few rules:

- **Nothing waits on a human thread.** A human decision is an append-only gate row and a
  parked job, never a worker blocked on stdin.
- **Nothing disappears when an agent dies.** Decisions, findings, handoffs, and spend are
  written to an append-only PostgreSQL ledger before they take effect. Leases use
  `FOR UPDATE SKIP LOCKED`, and replay is idempotent.
- **Capacity outranks confidence.** A completion claim from a model that is out of
  credits or otherwise unavailable is not accepted as success.
- **Handoffs are deterministic.** A cross-engine handoff must pass a model-free no-loss
  gate before the receiving runner can continue; otherwise it retries, escalates, or
  parks for a human.
- **Reviews are sovereign-first where they can be.** Diff-groundable review work runs
  through the local lane first; paid high-context reasoning stays available for the
  architectural questions that require it.

**Read the design first:**

[Research paper (HTML)](https://the-vibey-project.github.io/vibey/main/paper/) ·
[paper PDF](https://the-vibey-project.github.io/vibey/main/paper.pdf) ·
[documentation book (HTML)](https://the-vibey-project.github.io/vibey/main/) ·
[book PDF](https://the-vibey-project.github.io/vibey/main/book.pdf) ·
[book EPUB](https://the-vibey-project.github.io/vibey/main/book.epub) ·
[print HTML](https://the-vibey-project.github.io/vibey/main/book-print.html)

The current development release also generates editable DOCX versions of the paper and
book; those files will join the published documentation surfaces with the next release.

## What ships in the distribution

The source is one uv-workspace monorepo. These are components of `vibey`, not a set of
separate packages that must be installed and versioned independently.

| Component | Source | Role |
|---|---|---|
| Conductor | [`src/vibey`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey) | Domain, application, infrastructure, CLI and TUI for the six-phase delivery machine |
| Engine runners | [`src/vibey_runners`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_runners) | Claude Code, OpenAI Codex, Cursor Agent, Google Antigravity/Gemini, and local Qwen; each shares a common runner contract |
| `vibey-gh` | [`src/vibey_tools/gh`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/gh) | Provenance, exact-head review, merge train, branch promotion, release surfaces, paper/book exports, and continuous delivery estimates |
| `vibey-skills` | [`src/vibey_tools/skills`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/skills) | A deterministic Claude Code marketplace with 135 plugins and 710 evidence-grounded skills |
| `vibey-bootstrap` | [`src/vibey_tools/bootstrap`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/bootstrap) | Optional Azure, telemetry, configuration, Service Bus, outbox, and dead-letter foundations |

The command-line surface includes `vibey`, `vibey-gh`, `vibey-skills`,
`vibey-bootstrap`, and the `*loop` runner commands after the single install. The
individual component directories keep their own contracts and tests while the release
is one coherent distribution.

## How it is built

The repository treats architecture and operations as code:

- Onion layers point inward, with import-linter contracts and a pure domain layer.
- `domain`, `application`, `infrastructure`, and `cli` each carry a 100% branch-coverage
  floor; the absorbed workspace tenants keep their own gates.
- Conventional Commits, provenance trailers, exact-head claims, security scanning,
  SBOM/signing, and release promotion are enforced in CI.
- `develop` is the integration line; `main` is promoted from it for the stable release.
- Documentation is generated from the same source into a website, paper, editable DOCX
  files, EPUB, print HTML, and browser-produced PDFs.

The repository's [README](https://github.com/the-vibey-project/vibey) is the operational
quickstart. The [architecture decisions](https://github.com/the-vibey-project/vibey/tree/develop/docs/architecture/decisions)
explain why the hard constraints exist.

## Contribute

Start with an issue when a change is non-trivial, read the repository's
[`AGENTS.md`](https://github.com/the-vibey-project/vibey/blob/develop/AGENTS.md), and let
the gates prove the patch. The project is MIT-licensed and welcomes careful, evidence-
backed contributions.

Built and maintained by
[Adam Matthew Steinberger](https://vibewithadam.matthewsteinberger.com/).
