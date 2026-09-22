# Development Notes

## Initial Setup

The following describes the prerequisites and one-time manual steps that were needed for setting up this repository.

### Prerequisites

- AWS Account
    - Ownership of the domain emberarchive.org

### Manual Steps

1. Enable GitHub Pages
    - In this repository, go to `Settings` -> `Pages`
    - Under `Build and Deployment` > `Source` select `GitHub Actions`
2. Verify domain in GitHub
    - In this organization, go `Settings` -> `Pages`
    - Click `Add a domain`
    - Enter the domain name `"docs.emberarchive.org"`
    - Copy the provided TXT record and token
    - Click Verify
3. Add TXT record in Route 53
    - In AWS, go to Route 53 -> Hosted Zones
    - Click `emberarchive.org`
    - Click `Create record` and enter the following:
        - Type: `TXT`
        - Name: copied TXT record name from GitHub in above step
        - Value: copied token from GitHub in above step
4. Add DNS record in Route53
    - In AWS, go to Route 53 -> Hosted Zones
    - Click `emberarchive.org`
    - Click `Create record` and enter the following:
        - Type: `CNAME`
        - Name: `"docs"`
        - Value: `"aplbrain.github.io"`
5. Set the custom domain
    - In this repository, go to `Settings` -> `Pages`
    - Under `Custom domain`, enter `"docs.emberarchive.org"`
    - Click `Save`
6. Deploy
    - Merge/Push to `main` to trigger the initial GitHub Pages deployment
7. Enable HTTPS
    - In this repository, go to `Settings` -> `Pages`
    - Check the box next to `Enforce HTTPS`

## Pull request previews

Every pull request gets a live preview of the built site, **including pull
requests from forks**, deployed with
[`rossjrw/pr-preview-action`](https://github.com/rossjrw/pr-preview-action).

Previews are published to a **separate previews repository**, not to this one.
That keeps this repository's GitHub Pages setup (Source: `GitHub Actions`, via
`deploy-pages.yaml`) completely untouched, and keeps unreviewed contributor HTML
off the `docs.emberarchive.org` origin. See [SECURITY.md](SECURITY.md).

Contributors do not need to configure anything.

### One-time setup

1. Create a public repository to hold the previews, e.g.
   `aplbrain/bbqs-ember-docs-previews`.
2. Create a **fine-grained** personal access token (or a GitHub App
   installation token) with `Contents: Read and write` on **only** that
   repository. Do not grant it access to this repository.
3. In this repository, go to `Settings` -> `Secrets and variables` -> `Actions`
   and add:
    - Secret `PREVIEW_DEPLOY_TOKEN` — the token from step 2
    - Variable `PREVIEW_REPOSITORY` — e.g. `aplbrain/bbqs-ember-docs-previews`
    - Variable `PREVIEW_BASE_URL` — the URL the previews repo's Pages is served
      from, e.g. `https://aplbrain.github.io/bbqs-ember-docs-previews`
4. Open a pull request. The first run creates the `gh-pages` branch in the
   previews repository.
5. In the **previews** repository, go to `Settings` -> `Pages` and set the source
   to `Deploy from a branch`, selecting `gh-pages` and `/ (root)`.

Until `PREVIEW_REPOSITORY` is set, the preview workflow skips itself, so this can
be merged before the setup above is done.

### How it works

A workflow triggered by `pull_request` runs in the contributor's fork and cannot
read secrets, so it cannot publish anything. The work is therefore split across
two workflows:

| Workflow | Trigger | Privileges | Role |
| --- | --- | --- | --- |
| `check-docs.yaml` | `pull_request` | read-only, no secrets | Builds the site and uploads it as an artifact |
| `preview.yaml` | `workflow_run` | reads the deploy token | Downloads that artifact and publishes the preview |

> [!IMPORTANT]
> `preview.yaml` can read the preview deploy token on a trigger an untrusted
> party controls. The invariants that keep this safe are in
> [SECURITY.md](SECURITY.md). Read it before changing that workflow.

Because `workflow_run` only fires for workflow files on the default branch,
changes to the preview workflow cannot be fully tested in a pull request — they
take effect once merged to `main`.

### Cleaning up old previews

There is no teardown workflow, so a preview stays published after its pull
request is merged or closed. Prune the previews repository periodically by
deleting the stale directories under `pr-preview/` on its `gh-pages` branch:

```bash
git clone --branch gh-pages <previews-repo-url> previews && cd previews
git rm -r pr-preview/pr-<number>
git commit -m "Remove preview for PR <number>" && git push
```
