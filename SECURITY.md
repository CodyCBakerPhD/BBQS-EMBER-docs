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
`.github/workflows/preview.yaml` or `.github/workflows/preview-cleanup.yaml`
should read this section first.**

### Why the split exists

A workflow triggered by `pull_request` runs in the context of the contributor's
fork and receives a read-only token. It cannot publish a preview to this
repository, and no setting a contributor changes on their end can grant it that
access. Publishing therefore has to happen in a workflow that runs in *this*
repository's context — which means `workflow_run` or `pull_request_target`, both
of which carry a write token.

That is the whole reason these two workflows exist, and it is also exactly what
makes them dangerous: they hold a token that can write to this repository, and
they are triggered by events that an untrusted party controls.

### Trust boundary

| Workflow | Trigger | Token | Runs untrusted code? |
| --- | --- | --- | --- |
| `check-docs.yaml` | `pull_request` | read-only, no secrets | **Yes** — builds pull request code |
| `preview.yaml` | `workflow_run` | **write** | No — only unpacks a prebuilt artifact |
| `preview-cleanup.yaml` | `pull_request_target` | **write** | No — no checkout of pull request code, no build |

Untrusted code is built exactly once, in the one workflow that has nothing worth
stealing. The privileged workflows consume only the *output* of that build.

### Invariants

These must hold. Breaking any of them hands repository write access to anyone who
can open a pull request.

1. **`preview.yaml` must never check out or execute pull request code.** The
   artifact it downloads was produced from untrusted code; it may only be
   unpacked and published, never run. The `actions/checkout` step in it
   deliberately checks out the default branch.
2. **`preview-cleanup.yaml` must never check out or execute pull request code.**
   `pull_request_target` is only safe here because the workflow does no build and
   its checkout is the base branch. Do not add a `ref:` pointing at the pull
   request head, and do not add a build step.
3. **`check-docs.yaml` must stay read-only and secret-free.** It is the only
   workflow that runs untrusted code, and it is safe solely because it has no
   privileges to abuse. Do not add secrets or elevated `permissions` to it.
4. **Data crossing the boundary must be validated.** The pull request number is
   passed from the untrusted build via an artifact and is interpolated into a
   deploy path, so `preview.yaml` rejects anything that is not a plain positive
   integer. Any new value crossing this boundary needs comparable treatment.

A useful test when editing these files: *if a contributor opened a pull request
whose only change was a malicious `mkdocs.yml` hook, would this workflow run it
with a write token?* If the answer is anything other than a confident no, the
change is wrong.

### Accepted risk

The published preview is HTML built from unreviewed contributor code and is
served from a path on `docs.emberarchive.org`. The build itself is sandboxed, so
this is not a path to repository secrets or write access. The residual risk is
that a malicious pull request can serve arbitrary content from a preview path
until it is closed. This is a deliberate tradeoff made in exchange for previews
that work on fork pull requests.
