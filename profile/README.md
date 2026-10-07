# The Vibey Project

**Vibey is a free, open-source (MIT) orchestrator for AI coding agents: it interviews you
until the spec is sharp, builds the software unattended across a pool of engines,
reviews it with you, and records every decision in an append-only PostgreSQL ledger,
running on a local model on your own machine by default.**

[![Latest vibey-engine release on PyPI](https://img.shields.io/pypi/v/vibey-engine?label=vibey-engine)](https://pypi.org/project/vibey-engine/)
[![Python versions supported by vibey-engine](https://img.shields.io/pypi/pyversions/vibey-engine)](https://pypi.org/project/vibey-engine/)
[![Result of the latest CI run on vibey's develop branch](https://github.com/the-vibey-project/vibey/actions/workflows/ci.yml/badge.svg?branch=develop)](https://github.com/the-vibey-project/vibey/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-yellow.svg)](https://github.com/the-vibey-project/vibey/blob/develop/LICENSE)

You've used an AI coding agent. Then you babysat it: re-prompting when it lost the
thread, re-explaining everything after a crash, watching a run die at 2am because one
vendor's credits ran out. **Vibey does the babysitting.** It brings you back only for
the decisions that are yours, and it keeps going without you for everything else.

It is for developers who want that on their own hardware, with a record they can read.

**[Try it](#try-it)** · [Start contributing](#start-contributing) ·
[Read the paper](https://the-vibey-project.github.io/vibey/main/paper/) ·
[Ask a question](https://github.com/the-vibey-project/vibey/discussions/categories/q-a)

## How does it work?

You describe what you want. Vibey runs it through six phases, and the ones in bold talk
to you:

**① Design** → ② Build ⇄ **③ Review** → then, only if you opt in, **④ Deploy design** →
⑤ Deploy execute → **⑥ Deploy review**

An optional visual-design stage can sit between design and build. Declining deployment
is a successful finish, not a failure. Build and deploy-execute run unattended and hand
work between engines as capacity comes and goes.

## Why trust it with your repository?

**Nothing is lost when an agent dies or runs dry.** Work lives in a durable queue and a
PostgreSQL ledger, never inside one vendor's chat session. When an engine runs out of
credits, the next one is seeded only after a no-loss gate confirms that no open
question, decision, assumption or finding was dropped. That gate is deterministic code
with no model call. A chaos test abandons worker claims at random mid-job and passes
only if no job is lost or committed twice.
<br>Evidence: [ADR-0004](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0004-no-loss-gate-on-handoff.md)
· [the gate's tests](https://github.com/the-vibey-project/vibey/blob/develop/tests/domain/test_noloss.py)
· [the chaos test](https://github.com/the-vibey-project/vibey/blob/develop/tests/infrastructure/db/test_chaos.py)
· [case study: how vibey survives a crashed agent](https://github.com/the-vibey-project/vibey/blob/develop/docs/case-studies/how-vibey-survives-a-crashed-agent.md)
· [paper: the no-loss handoff gate](https://the-vibey-project.github.io/vibey/main/paper/#the-no-loss-handoff-gate)

**The record cannot be quietly rewritten.** Database triggers refuse every update,
delete and truncate of the ledger, even by its owner, and the application connects as a
role that can only read and append. A SHA-256 hash chain, derived over every event, lets
an exported ledger be checked link by link.
<br>Evidence: [ADR-0055](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0055-the-ledger-is-append-only-by-the-database.md)
· [the chain's tests](https://github.com/the-vibey-project/vibey/blob/develop/tests/domain/test_ledger_chain.py)
· [paper: the ledger invariant](https://the-vibey-project.github.io/vibey/main/paper/#the-ledger-invariant)

**A person approves what cannot be undone.** A question for you parks the job and frees
the worker; nothing sits waiting on a terminal. Deployment needs your explicit consent,
recorded in the ledger. The Azure stage set signs in with workload identity (OIDC),
creates no client secrets, and treats any new role assignment as a human gate.
<br>Evidence: [ADR-0009](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0009-human-gates-are-parked-jobs.md)
· [ADR-0014](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0014-optional-visual-design-and-deployment-opt-in.md)
· [ADR-0013's safety model](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0013-deployment-is-a-three-phase-stage-set.md#safety-model)

**It runs on your machine.** The default engine is GPT-OSS 20B, served by Ollama on your
own hardware. Local engines are tried before any paid one, local turns are recorded at
$0, and there is no cloud control plane to trust with your working tree.
<br>Evidence: [ADR-0038](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0038-local-engines-are-preferred-first.md)
· [ADR-0064](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0064-gptossloop-is-the-sovereign-engine.md)
· [paper: the sovereign driver](https://the-vibey-project.github.io/vibey/main/paper/#the-sovereign-driver-and-local-fit)

**Its gates are checks, not promises.** Each of the four architectural layers holds a
100% branch-coverage floor as its own CI gate. Every commit carries a provenance trailer
that CI verifies. Releases reach PyPI through trusted publishing, so no upload token is
stored anywhere. Each engine process starts from an allow-listed environment, so the
database DSN and other services' secrets never reach it.
<br>Evidence: [ADR-0023](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0023-four-layers-four-floors.md)
· [ADR-0028](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0028-vibey-gh-owns-release-and-provenance.md)
· [the release workflow](https://github.com/the-vibey-project/vibey/blob/develop/.github/workflows/vibey-engine.yml)
· [paper: what an engine may see](https://the-vibey-project.github.io/vibey/main/paper/#what-an-engine-may-see)

## Try it

You need macOS or Linux, Python 3.12+ and PostgreSQL 14+ (vibey can install PostgreSQL
for you). The local model is a 13.79 GB download. 24 GB of memory is the minimum for
running it, and the 24 GB machine it was measured on swapped during design, so more is
better; a 16 GB Mac cannot run it ([system requirements](https://github.com/the-vibey-project/vibey/blob/develop/docs/reference/system-requirements.md)).

```bash
ollama pull gpt-oss:20b                  # the local model, served by Ollama
uv tool install vibey-engine             # or: pipx install vibey-engine
vibey install --postgres                 # only if you need a local PostgreSQL

# The app connects as a role that can only read and append the ledger;
# the owner's DSN is handed to `vibey migrate` alone, never exported.
export VIBEY_PG_URL=postgresql://vibey_app:change-me@localhost:5432/vibey
VIBEY_PG_MIGRATE_URL=postgresql://$USER@localhost:5432/vibey vibey migrate

vibey new my-app --repo ~/src/my-app     # a project, and its first design interview
vibey doctor --conformance --record      # check the engines and record what passed
vibey worker --engines gptossloop        # design and build on the local model
vibey gates                              # in a second terminal: what is waiting for you
```

`vibey gates` prints each open question with the exact `vibey answer` command that
answers it. The [README](https://github.com/the-vibey-project/vibey#readme) has the full
walkthrough: paid engines, budget caps, database authentication and troubleshooting.

## Start contributing

A first pull request should take an afternoon, not a week. Here is the shortest path.

1. **Say hello.** Ask anything in
   [Discussions](https://github.com/the-vibey-project/vibey/discussions/categories/q-a),
   or join the [Discord](https://discord.gg/Qvu8aYnVS). "Where should I start?" is a
   good first message.
2. **Pick something small.** Look for
   [good first issue](https://github.com/the-vibey-project/vibey/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)
   and [help wanted](https://github.com/the-vibey-project/vibey/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22).
   If both lists are empty, the documentation is always open: anything unclear there
   counts as a bug.
3. **Set up.** The default test suite needs PostgreSQL but no paid accounts and no
   engine binaries.

   ```bash
   git clone https://github.com/the-vibey-project/vibey.git && cd vibey
   uv sync --extra dev
   uv run pre-commit install --hook-type pre-commit --hook-type commit-msg --hook-type pre-push
   uv run vibey-gh install
   ```

4. **Open a pull request against `develop`.** Use a
   [Conventional Commits](https://www.conventionalcommits.org/) subject; the hook adds the
   provenance trailer for you. The gates do the reviewing, so run them before you push.

[CONTRIBUTING.md](https://github.com/the-vibey-project/vibey/blob/develop/CONTRIBUTING.md)
has every command and gate. Working with a coding agent? Point it at
[AGENTS.md](https://github.com/the-vibey-project/vibey/blob/develop/AGENTS.md).

## Read deeper

- **The research paper**, *Ledger-Mediated Orchestration: Vendor-Independent Autonomous
  Software Delivery over a Pool of Coding Agents*:
  [HTML](https://the-vibey-project.github.io/vibey/main/paper/) ·
  [PDF](https://the-vibey-project.github.io/vibey/main/paper.pdf)
- **The book**, every page of the documentation in reading order:
  [PDF](https://the-vibey-project.github.io/vibey/main/book.pdf) ·
  [EPUB](https://the-vibey-project.github.io/vibey/main/book.epub) ·
  [print HTML](https://the-vibey-project.github.io/vibey/main/book-print.html)
- **The documentation site**: [the-vibey-project.github.io/vibey](https://the-vibey-project.github.io/vibey/main/)
- **The decision records**, why each hard call was made:
  [docs/architecture/decisions](https://github.com/the-vibey-project/vibey/tree/develop/docs/architecture/decisions)

## What ships in the packages

`vibey-engine` is the engine and every tool, from one install; `krypton-app` is the apps.
The source is one monorepo.

| Component | Source | What it does |
| --- | --- | --- |
| Conductor | [`src/vibey`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey) | The six-phase machine, queue, ledger, CLI and TUI |
| Engine runners | [`src/vibey_runners`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_runners) | `claudeloop` (Claude Code), `codexloop` (OpenAI Codex), and the local runner as `gptossloop` (GPT-OSS 20B, the default) and `qwenloop` (Qwen, opt-in) |
| `vibey-gh` | [`src/vibey_tools/gh`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/gh) | Provenance, exact-head review, merge train, promotion and release |
| `vibey-skills` | [`src/vibey_tools/skills`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/skills) | A deterministic Claude Code plugin marketplace of evidence-grounded skills |
| `vibey-bootstrap` | [`src/vibey_tools/bootstrap`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/bootstrap) | Optional Azure, telemetry, configuration and messaging foundations |
| Krypton apps | [`clients`](https://github.com/the-vibey-project/vibey/tree/develop/clients) | The VS Code extension, desktop, mobile and web apps (`krypton-app`) |

## Questions people ask

### Is Vibey an AI coding agent?

No. It conducts them. The agents write the code; Vibey owns the spec interview, the
queue, the handoffs between engines, the review loop and the record of what happened.

### Does my code leave my machine?

Not with the defaults. The default engine runs on your hardware through Ollama. A paid
engine joins only once you install its vendor's command-line tool and sign in, and even
then local engines are tried first. Deployment is opt-in, and consent is asked for again
rather than assumed.

### Which coding agents does it work with?

Claude Code and OpenAI Codex, each through its own runner, plus local models on Ollama:
GPT-OSS 20B by default, and Qwen as an opt-in. Runners for Cursor Agent and Google
Antigravity existed earlier and were retired, with the reasoning in
[ADR-0078](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0078-retire-cursorloop-and-agyloop.md).

### Can a team run it?

Yes. Several workers can claim jobs at once against one PostgreSQL ledger, and a
Kubernetes operator runs projects as a `VibeyProject` resource with Helm and KEDA
autoscaling. The
[Kubernetes guide](https://github.com/the-vibey-project/vibey/blob/develop/docs/guides/kubernetes.md)
and [ADR-0025](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0025-kubernetes-operator-crd-keda.md)
cover it.

### What does it cost?

The software is MIT-licensed and free. Local turns cost nothing per token. Paid engines
bill your own vendor account, and each project carries dollar and turn caps summed from
the ledger, so a cap that trips parks the work and tells you the command to grant more.

### Why PostgreSQL and not SQLite?

Several worker processes claim jobs at the same time, which needs row-level locking,
and the ledger is guarded by database roles the application cannot step outside.
SQLite has neither. The reasoning is in
[ADR-0002](https://github.com/the-vibey-project/vibey/blob/develop/docs/architecture/decisions/0002-postgres-not-sqlite.md).

### How do I cite Vibey?

Use GitHub's "Cite this repository" on [vibey](https://github.com/the-vibey-project/vibey),
which reads [CITATION.cff](https://github.com/the-vibey-project/vibey/blob/develop/CITATION.cff).
Cite the paper for the design and the repository for the software.

### Where do I report a security issue?

Privately, through **Security → Report a vulnerability** on the affected repository.
Never in a public issue. The details are in
[SECURITY.md](https://github.com/the-vibey-project/vibey/blob/develop/SECURITY.md).

### Who maintains Vibey, and can I hire the maintainer?

Vibey is maintained by Adam Matthew Steinberger, an independent AI platform engineer in
Greenville, South Carolina, working US-remote. Separately from the project, Adam takes
fixed-scope contract work in the same territory: AI codebase and security reviews, RAG
chatbots, LLM cost and policy gateways, Okta and Entra ID governance, and SOC 2 and
OWASP LLM Top 10 readiness for AI features.

Each engagement starts with a [written intake](https://github.com/adammatthewsteinberger/resume/blob/develop/freelance/intake.md)
rather than a discovery call, and a fixed scope and acceptance checklist are agreed in
writing before work begins. The offers, and the work behind each one, are in
[SERVICES.md](https://github.com/adammatthewsteinberger/resume/blob/develop/SERVICES.md).
Paid work never buys a place in the project's roadmap or review queue: contributions are
reviewed by the same gates whoever sends them.

---

MIT-licensed. Built in the open and maintained by
[Adam Matthew Steinberger](https://vibewithadam.matthewsteinberger.com/)
([résumé](https://github.com/adammatthewsteinberger/resume#readme) ·
[contract work](https://github.com/adammatthewsteinberger/resume/blob/develop/SERVICES.md)).
