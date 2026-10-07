# Agent guidance for `the-vibey-project/.github`

This repository holds GitHub's **organisation-wide defaults**: the profile
README rendered on the organisation page, and the community health files every
other repository inherits when it does not define its own. There is no package,
no test suite, and nothing to build.

## What that means for a change here

**These files are read by people on github.com.** They are the first thing a
stranger sees. Write for that reader: someone who does not yet know what these
projects are, arriving from a search result or a link.

**A repository's own file always wins.** Something belongs here only if it is
true of *every* repository in the organisation. If it is specific to one — the
way `vibey`'s security policy is specific to running autonomous engines against
a working tree — it belongs in that repository instead.

**Check a claim before writing it.** The runners and tools that once had their
own repositories now live inside `vibey`, and versions, counts and gate
descriptions go stale. Verify against the repository, not against memory.

## The organisation profile

`profile/README.md` is the organisation's front door, and it is written to be
found by people, by search engines and by answer engines, and to turn a newcomer
into a contributor within an hour. That decision is recorded as vibey ADR-0076
(proposed); these are the rules it sets for anyone editing the page:

- **The first sentence defines the project.** It says what Vibey is in words an
  answer engine can quote verbatim. Change it only when the project changes.
- **Every claim is true today and traceable.** Each proof point links to the ADR,
  test or paper section that establishes it, at `vibey`'s `develop` branch or the
  published docs. No invented users, adopters, stars, testimonials or metrics.
- **Prefer claims that do not drift.** A number that changes (a version, a count of
  plugins, skills or ADRs) either comes from a badge that computes it or stays off
  the page. A measured figure names where it was measured.
- **Findable honestly.** Question-shaped headings, alt text on every image, a text
  equivalent for every diagram, stable links. No keyword stuffing, hidden text or
  other black-hat SEO.
- **Progressive disclosure.** Short paragraphs up top, depth one click down. The
  page links to the README, CONTRIBUTING, the paper and the decision records rather
  than repeating them.
- **The page is about the project.** Vendors appear only as the engines Vibey
  supports, as `vibey` itself describes them, and no private person is named.
- **One bounded exception: the maintainer.** The last question in *Questions people
  ask* and the footer may say who maintains Vibey and that the maintainer takes paid,
  fixed-scope work. Keep it there, below the contributor path, and keep it to links:
  the offers, their evidence and any prices live in the maintainer's
  [`resume`](https://github.com/adammatthewsteinberger/resume) repository, not here. It
  may not name clients, quote testimonials or add figures this page cannot trace, and it
  must say that paid work buys no place in the project's review queue. The order the
  page serves is contributors first, people hiring for contract work second, and
  employers third.

## The rules that do apply

- **Conventional Commits**, enforced by `conventional-commits.yml` and by the
  installed `commit-msg` hook.
- **Every commit carries a `Made-With` trailer**, enforced by `provenance.yml`.
  File headers are deliberately *not* required here: a provenance comment at the
  top of `CONTRIBUTING.md` is noise in the one place it would actually be read,
  and the trailer is the half of the rule that covers such files.
- **Work lands on `develop`** and is promoted to `main`. Never commit to `main`.
  Pull requests squash into `develop`; the promotion pull request is a **rebase**
  merge, which rewrites the commits, so `develop` and `main` differ by SHA afterwards
  even though their contents match. Run `vibey-gh realign` to converge them. It refuses
  unless the two trees are identical, so it cannot discard work, and the next promotion
  is blocked until it has run. This repository does not install the promotion workflows,
  so neither step happens by itself.
- **`.github/workflows/*` are generated.** They are rendered by the `vibey-gh`
  command shipped in the `vibey-engine` package, from templates in
  [`vibey`](https://github.com/the-vibey-project/vibey) under
  `src/vibey_tools/gh`. Editing a rendered workflow here is reverted by the next
  install and reported as drift by CI. Change the template in `vibey`.

## Before you push

```bash
pip install vibey-engine   # carries vibey-gh; match the version the workflows pin
vibey-gh install           # idempotent; installs hooks and re-renders managed workflows
vibey-gh check             # exactly what CI will say
```
