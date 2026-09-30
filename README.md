# `.github`: the organisation's shared defaults

This repository holds the files GitHub reads at the **organisation** level for
[the-vibey-project](https://github.com/the-vibey-project). It is not a product: the
product is [`vibey`](https://github.com/the-vibey-project/vibey), and if you came here
to use or contribute to it, start with
[the organisation profile](https://github.com/the-vibey-project) or
[vibey's CONTRIBUTING.md](https://github.com/the-vibey-project/vibey/blob/develop/CONTRIBUTING.md).

## What lives here

| Path | What GitHub does with it |
| --- | --- |
| `profile/README.md` | Renders it as the organisation's landing page at [github.com/the-vibey-project](https://github.com/the-vibey-project) |
| `CODE_OF_CONDUCT.md` | The default code of conduct for any repository here that does not define its own |
| `CONTRIBUTING.md` | The default contributing guide, linked from new issues and pull requests |
| `SECURITY.md` | The default security policy, shown on each repository's **Security** tab |
| `SUPPORT.md` | The default support routing, linked from the issue chooser |
| `.github/ISSUE_TEMPLATE/` | The default issue forms and chooser |
| `.github/PULL_REQUEST_TEMPLATE.md` | The default pull-request template |

**A repository's own file always wins.** These are fallbacks, not overrides. `vibey`
ships its own copy of every one of them, so what applies here in practice is this
repository itself, and any repository the organisation adds later. Put something here
only when it is true of *every* repository in the organisation.

## Changing something here

Most changes are to `profile/README.md`, and [AGENTS.md](AGENTS.md) says what that page
is for and how claims on it are checked. The short version: write for someone who has
never heard of these projects, and trace every claim to the `vibey` repository or its
published docs.

The same provenance rules as the rest of the family apply, installed by `vibey-gh`
from [`vibey`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/gh):

- Every commit carries a Conventional Commits subject and a `Made-With` trailer. The
  `commit-msg` hook adds the trailer; `conventional-commits.yml` and `provenance.yml`
  enforce both server-side.
- Work lands on `develop` and is promoted to `main`. Nothing is committed to `main`
  directly.

After cloning:

```bash
pip install vibey-engine   # carries vibey-gh; match the version the workflows pin
vibey-gh install           # installs the hooks and re-renders the managed workflows
vibey-gh check             # verifies both halves of the provenance rule
```

`vibey-gh install` is idempotent and is the only supported way to change the managed
workflows. To change what they do, edit the template in
[`vibey/src/vibey_tools/gh`](https://github.com/the-vibey-project/vibey/tree/develop/src/vibey_tools/gh),
not the rendered file here.
