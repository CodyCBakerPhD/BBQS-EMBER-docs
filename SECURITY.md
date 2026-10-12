# Security

## Reporting a vulnerability

Please report security issues privately using GitHub's
[private vulnerability reporting](https://github.com/aplbrain/BBQS-EMBER-docs/security/advisories/new)
rather than opening a public issue.

## Pull request preview pipeline

This repository publishes a live preview of the built documentation for every
pull request, including pull requests from forks. Building unreviewed code and
publishing the result is inherently a privileged operation, so the workflows
that do it are split along a deliberate trust boundary. **Anyone modifying
`.github/workflows/preview.yaml` should read this section first.**

### Why the split exists

A workflow triggered by `pull_request` runs in the context of the contributor's
fork, receives a read-only token, and cannot read repository secrets. It
therefore cannot publish a preview. Publishing has to happen in a workflow that
runs in *this* repository's context, which is what `workflow_run` provides.

That is the whole reason `preview.yaml` exists, and it is also exactly what
makes it sensitive: it can read a credential with write access to the previews
repository, and it is triggered by an event an untrusted party controls.

No workflow here uses `pull_request_target`. That trigger is the usual source of
Actions privilege-escalation bugs, so avoiding it entirely removes a class of
mistakes a future change could otherwise make.

### Trust boundary

| Workflow | Trigger | Privileges | Runs untrusted code? |
| --- | --- | --- | --- |
| `check-docs.yaml` | `pull_request` | read-only, no secrets | **Yes** — builds pull request code |
| `preview.yaml` | `workflow_run` | reads `PREVIEW_DEPLOY_TOKEN` | No — only unpacks a prebuilt artifact |

Untrusted code is built exactly once, in the one workflow that has nothing worth
stealing. The privileged workflow consumes only the *output* of that build.

### Isolation of preview content

Previews are published to a **separate previews repository** and served from a
**different origin** than the production documentation. This is deliberate: the
preview is HTML built from unreviewed contributor code, and serving it from
`docs.emberarchive.org` would place attacker-controlled content on the same
origin as the real docs.

The production site is untouched by this pipeline. It continues to deploy from
`deploy-pages.yaml` using the GitHub Pages Actions source, and no workflow in the
preview path can write to it.

### Previews are not removed automatically

There is no teardown workflow. A preview stays published after its pull request
is merged or closed, until someone deletes the directory from the previews
repository's `gh-pages` branch.

This means content from a **closed or rejected** pull request remains publicly
served indefinitely. Because previews live on their own origin, this cannot
affect the production docs, but it does mean the previews site should be pruned
periodically — and that a preview from a pull request closed *for being
malicious* needs deleting by hand.

### Invariants

These must hold. Breaking any of them exposes the preview deploy credential to
untrusted code.

1. **`preview.yaml` must never check out or execute pull request code.** The
   artifact it downloads was produced from untrusted code; it may only be
   unpacked and published, never run. Its `actions/checkout` step deliberately
   checks out the default branch.
2. **`check-docs.yaml` and `build-docs.yaml` must stay read-only and
   secret-free.** They are the only place untrusted code runs, and that is safe
   solely because they have no privileges to abuse. Do not add secrets or
   elevated `permissions` to them.
3. **Data crossing the boundary must be validated.** The pull request number is
   passed from the untrusted build via an artifact and is interpolated into a
   deploy path, so `preview.yaml` rejects anything that is not a plain positive
   integer. Any new value crossing this boundary needs comparable treatment.

A useful test when editing this file: *if a contributor opened a pull request
whose only change was a malicious `mkdocs.yml` hook, would this workflow run it
with access to `PREVIEW_DEPLOY_TOKEN`?* If the answer is anything other than a
confident no, the change is wrong.

### The preview deploy token

`PREVIEW_DEPLOY_TOKEN` is a long-lived credential and should be scoped as
tightly as GitHub allows:

- Use a **fine-grained** personal access token, or a GitHub App installation
  token, not a classic PAT.
- Grant it **only** `Contents: Read and write`, and **only** on the previews
  repository. It must have no access to this repository or to any other.
- Set an expiry and rotate it. If it leaks, the blast radius is limited to
  defacing the previews site.
