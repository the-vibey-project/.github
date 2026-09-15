# `.github` — the organisation's shared defaults

This repository is not a project. It holds the files GitHub reads at the
**organisation** level, so that every other repository in
[the-vibey-project](https://github.com/the-vibey-project) inherits them without
carrying its own copy.

| Path | What GitHub does with it |
|---|---|
| `profile/README.md` | Rendered as the organisation's landing page at [github.com/the-vibey-project](https://github.com/the-vibey-project) |
| `CODE_OF_CONDUCT.md` | The default code of conduct for any repository here that does not define its own |
| `CONTRIBUTING.md` | The default contributing guide, linked from every new issue and pull request |
| `SECURITY.md` | The default security policy, linked from each repository's **Security** tab |
| `SUPPORT.md` | The default support routing, linked from the issue chooser |
| `.github/ISSUE_TEMPLATE/` | The default issue forms and chooser |
| `.github/PULL_REQUEST_TEMPLATE.md` | The default pull-request template |

**A repository's own file always wins.** These are fallbacks, not overrides —
`vibey` ships its own `SECURITY.md` because its threat model is specific to
running autonomous engines against a working tree, and that copy is what its
Security tab shows. Put something here only when it is true of *every*
repository in the organisation.

## Working in this repository

It carries the same provenance rules as the rest of the family, installed by
[vibey-gh](https://github.com/the-vibey-project/vibey-gh):

- Every commit carries a `Made-With` trailer and a Conventional Commits subject.
  The `commit-msg` hook adds the trailer; `conventional-commits.yml` and
  `provenance.yml` enforce both server-side.
- Work lands on `develop` and is promoted to `main`. Nothing is committed to
  `main` directly.

After cloning:

```bash
pip install vibey-gh
vibey-gh install     # installs the hooks and re-renders the managed workflows
vibey-gh check       # verifies both halves of the provenance rule
```

`vibey-gh install` is idempotent and is the only supported way to change the
managed workflows — edit the template in `vibey-gh`, not the rendered file here.
