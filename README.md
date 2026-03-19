# Spenzy Release Notes

This repo is meant to host Spenzy release-note JSON and assets for CDN delivery.

## Structure

- `release-notes/index.json`
- `release-notes/<version>.json`
- `release-notes/images/<version>/...`

## Publish

When GitHub API access is working again, create and push the public repo with:

```bash
gh repo create tyodsgn/Spenzy-Release-Notes --public --source /Users/tyodsgn/Documents/GitHub/Spenzy-Release-Notes --remote origin --push
```

## App URL

After that, set `RELEASE_NOTES_URL` in the app to:

`https://cdn.jsdelivr.net/gh/tyodsgn/Spenzy-Release-Notes@main/release-notes/index.json`
