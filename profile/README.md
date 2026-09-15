# The Vibey Project

**Autonomous software delivery, built so you can check its work.**

An agent that writes code is easy. An agent you can leave running is a different
problem: it has to know when it is out of credits, hand off without losing the
thread, refuse to claim a job it did not finish, and leave a record you can read
afterwards. That is what lives here.

---

## Start here

### 🎼 [vibey](https://github.com/the-vibey-project/vibey)

The conductor. You describe what you want; vibey runs it through six phases —
**design → build → review**, with an optional visual-design step and an opt-in
deployment stage set — and comes back when it needs you.

Every job is a row in PostgreSQL, leased with `FOR UPDATE SKIP LOCKED`. Workers
die and the lease expires and another worker picks the job up, because every job
is idempotent under replay. The conversation is an append-only ledger: no
updates, no deletes, corrections are new events that supersede old ones.

Three rules it will not bend:

- **It never blocks a worker on a human.** Waiting for you is a parked job and a
  gate row, never a thread waiting on stdin.
- **A capacity rejection outranks a completion claim.** An engine that says "done"
  while it is out of credit is not believed.
- **A handoff is gated by a predicate, not a vibe.** Passing work between models
  has to pass a deterministic no-loss check — matching on ids minted by vibey, not
  on text — before it is accepted. A failed gate is a retry, an escalation, or a
  human gate. Never a silent partial.

Python 3.12+, PostgreSQL, pre-1.0.

---

## The runners

vibey does not reimplement the agents. It drives them, and rotates between them
by smooth weighted round-robin so one provider running dry does not stop the
work. Each is a standalone autonomous session runner you can use on its own:

| | |
|---|---|
| [**claudeloop**](https://github.com/the-vibey-project/claudeloop) | Claude |
| [**codexloop**](https://github.com/the-vibey-project/codexloop) | OpenAI |
| [**cursorloop**](https://github.com/the-vibey-project/cursorloop) | Cursor |
| [**agyloop**](https://github.com/the-vibey-project/agyloop) | Antigravity |
| [**qwenloop**](https://github.com/the-vibey-project/qwenloop) | Local Qwen — the standby tier, so a run can finish with no paid credits at all |

---

## The libraries

Each is independently useful, independently versioned, and on PyPI.

### 📚 [vibey-skills](https://github.com/the-vibey-project/vibey-skills)

**127 plugins. 644 skills.** A Claude Code plugin marketplace of
evidence-grounded practitioner references — architecture, security, testing,
engineering process, and a long tail of domains — plus a deterministic retrieval
engine that compiles a token-budgeted context packet from them. Lexical, not
embedding-based: the same request returns the same packet, with a provenance
comment over every chunk. Zero dependencies, so `uvx vibey-skills` just runs.

### 🔁 [vibey-gh](https://github.com/the-vibey-project/vibey-gh)

The GitHub automation the whole family runs on: provenance fingerprints that make
every change attributable, versions derived from what actually changed rather than
chosen, a merge train, branch realignment, and a documentation channel that
publishes a site, a book and a paper from the same source. **Zero dependencies,
stdlib only** — deliberately, because it runs in every CI job of every repository
that adopts it, and a dependency it grows is a dependency they all grow.

### 🥾 [vibey-bootstrap](https://github.com/the-vibey-project/vibey-bootstrap)

The cross-cutting layer, solved once. One call brings a cold process up:
structured logging that works immediately, configuration and secrets loaded into
the environment, telemetry attached as soon as there is something to attach it
to. Everything past that — tiered alerts, health probes, a hardened HTTP client,
ten log transports, a transactional outbox — is opt-in behind a pip extra.

---

## How it is built

Two rules hold across every repository here, enforced in CI rather than asked
for in review:

- **Every commit is attributable**, by a file header *and* a commit trailer — two
  halves, because a rule that only covers files cannot express itself in JSON or
  in generated Markdown.
- **Nothing merges to `main` directly.** Feature work squashes into `develop`;
  `develop` merge-commits into `main`.

The code gates are per-repository and deliberately not uniform, because the
repositories are not alike. vibey and the runners carry **100% branch coverage as
a per-layer floor**, not an average, plus `import-linter` contracts that make
"dependencies point inward" a build failure rather than a convention.
vibey-bootstrap carries a 100% line floor. vibey-skills is mostly a Markdown
corpus, and its real gate is a manifest validator that checks every skill's
frontmatter, every plugin's version, and that the published wheel actually
carries all 644 of them.

---

## Using any of it

```bash
pip install vibey          # the conductor
pip install vibey-gh       # the GitHub automation
pip install vibey-skills   # the skills marketplace and context engine
pip install vibey-bootstrap
```

Every repository carries its own README, quickstart, and architecture decision
records — the ADRs are worth reading first if you want to know *why* something
works the way it does rather than how.

Built by [Adam Matthew Steinberger](https://vibewithadam.matthewsteinberger.com/).
