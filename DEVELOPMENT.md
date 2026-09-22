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

Every pull request opened from a branch in this repository gets a live preview
of the built site, deployed by `.github/workflows/preview.yaml` using
[`rossjrw/pr-preview-action`](https://github.com/rossjrw/pr-preview-action).

- The preview is published to the `gh-pages` branch under
  `pr-preview/pr-<number>/`, alongside the production site, and is reachable at
  `https://docs.emberarchive.org/pr-preview/pr-<number>/`.
- A comment with the preview link is posted on the pull request and updated on
  every push.
- The preview is removed automatically when the pull request is closed or
  merged.

Two caveats:

- Pull requests from forks are skipped, because they run with a read-only token
  and cannot push to `gh-pages`. They are still built (without a preview) by
  `.github/workflows/check-docs.yaml`.
- `deploy-pages.yaml` publishes with `keep_files: true` so that it does not wipe
  the `pr-preview/` directory. As a side effect, files deleted from the docs are
  not removed from the live site; delete them from the `gh-pages` branch by hand
  if that matters.
