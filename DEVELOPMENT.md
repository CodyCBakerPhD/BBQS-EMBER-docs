# Development Notes

## Initial Setup

The following describes the prerequisites and one-time manual steps that were needed for setting up this repository.

### Prerequisites

- AWS Account
    - Ownership of the domain emberarchive.org

### Manual Steps

1. Enable GitHub Pages
    - In this repository, go to `Settings` -> `Pages`
    - Under `Build and Deployment` > `Source` select `Deploy from a branch`
    - Select the `gh-pages` branch and the `/ (root)` folder, then click `Save`
    - The `gh-pages` branch is created automatically by the first run of
      `.github/workflows/deploy-pages.yaml`, so this setting can only be applied
      after that workflow has run once
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

- The preview is published to the `gh-pages` branch under
  `pr-preview/pr-<number>/`, alongside the production site, and is reachable at
  `https://docs.emberarchive.org/pr-preview/pr-<number>/`.
- A comment with the preview link is posted on the pull request and updated on
  every push.
- The preview is removed automatically when the pull request is closed or
  merged.

Contributors do not need to configure anything.

### How fork previews work

A workflow triggered by `pull_request` runs in the context of the *contributor's*
fork and receives a read-only token, so it cannot publish anything to this
repository. The work is therefore split across three workflows:

| Workflow | Trigger | Token | Role |
| --- | --- | --- | --- |
| `check-docs.yaml` | `pull_request` | read-only | Builds the site from pull request code and uploads it as an artifact |
| `preview.yaml` | `workflow_run` | write | Downloads that artifact and publishes the preview |
| `preview-cleanup.yaml` | `pull_request_target` | write | Removes the preview when the pull request closes |

`workflow_run` and `pull_request_target` both run in the context of *this*
repository on the default branch, which is what gives them a write token for a
fork's pull request.

> [!IMPORTANT]
> `preview.yaml` and `preview-cleanup.yaml` run with a write token on triggers
> an untrusted party controls. The invariants that keep this safe, and the
> reasoning behind them, are in [SECURITY.md](SECURITY.md). Read it before
> changing either workflow.

Two consequences worth understanding:

- Because `workflow_run` only fires for workflow files on the default branch,
  changes to the preview workflows cannot be fully tested in a pull request —
  they take effect once merged to `main`.
- The published preview is HTML built from unreviewed contributor code, served
  from a path on `docs.emberarchive.org`. The build itself is sandboxed, so this
  is not a route to repository secrets, but a malicious pull request could serve
  arbitrary content from that path until it is closed.

### Optional: hosting your own preview from a fork

Contributors who would rather not wait for the upstream preview, or who want a
preview before opening a pull request, can publish one from their own fork with
`.github/workflows/fork-preview.yaml`. It is disabled by default. To enable it
on a **public** fork:

1. Go to `Actions` in your fork and enable workflows (forks ship with Actions
   disabled).
2. Go to `Settings` -> `Secrets and variables` -> `Actions` -> `Variables` and
   add a repository variable `ENABLE_FORK_PREVIEW` with the value `true`.
3. Push a branch. The workflow builds the docs and publishes them to the
   `gh-pages` branch of your fork under `branch-preview/<branch>/`.
4. Go to `Settings` -> `Pages` and set the source to `Deploy from a branch`,
   selecting `gh-pages` and `/ (root)`.

The preview is then served at
`https://<your-username>.github.io/BBQS-EMBER-docs/branch-preview/<branch>/`;
the workflow run summary prints the URL. Note that your fork's token cannot
comment on an upstream pull request, so you will need to paste the link
yourself. GitHub Pages on a private repository requires a paid plan.

### Production deploys and `keep_files`

`deploy-pages.yaml` publishes with `keep_files: true` so that it does not wipe
the sibling `pr-preview/` directory on the same branch. As a side effect, files
deleted from the docs are not removed from the live site; delete them from the
`gh-pages` branch by hand if that matters.

The custom domain is written to `CNAME` on every deploy, but only when the
workflow runs in the upstream repository — a fork that syncs `main` publishes to
its own `<owner>.github.io` URL instead of trying to claim
`docs.emberarchive.org`.
