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

**Check a claim before writing it.** Several repositories are archived and their
code now lives inside `vibey`; versions, counts and gate descriptions go stale.
Verify against the repository, not against memory.

## The rules that do apply

- **Conventional Commits**, enforced by `conventional-commits.yml` and by the
  installed `commit-msg` hook.
- **Every commit carries a `Made-With` trailer**, enforced by `provenance.yml`.
  File headers are deliberately *not* required here: a provenance comment at the
  top of `CONTRIBUTING.md` is noise in the one place it would actually be read,
  and the trailer is the half of the rule that covers such files.
- **Work lands on `develop`** and is promoted to `main`. Never commit to `main`.
- **`.github/workflows/*` are generated.** They are rendered by `vibey-gh install`
  from templates in the `vibey-gh` package, which now lives in
  [`vibey`](https://github.com/the-vibey-project/vibey) under
  `src/vibey_tools/gh`. Editing a rendered workflow here is reverted by the next
  install and reported as drift by CI. Change the template.

## Before you push

```bash
pip install vibey-gh
vibey-gh install     # idempotent; installs hooks and re-renders managed workflows
vibey-gh check       # exactly what CI will say
```
