# Release Notes Storage

This folder is structured for GitHub-hosted release notes with jsDelivr delivery.

## Recommended Setup

- Keep one JSON file per app version.
- Keep release-note images inside `release-notes/images/<version>/`.
- Point `RELEASE_NOTES_URL` in the app to `release-notes/index.json`.
- Use relative image paths inside each version JSON file. The app resolves them against the note file URL.

## CDN URL

When the repo is public, the app can load:

`https://cdn.jsdelivr.net/gh/tyodsgn/Spenzy-ExpenseTracker-IOS-App@main/release-notes/index.json`

## Important

`jsDelivr` only works for public GitHub repositories. The current repo URL returned `404` from both `raw.githubusercontent.com` and `cdn.jsdelivr.net`, so this folder is ready for hosting, but it will need to live in a public repo or public mirror before the app can fetch it remotely.

## JSON Shape

The app currently expects the richer `AppReleaseNote` structure used by the existing release-note screens:

```json
{
  "version": "1.1.7",
  "color": "sysTeal",
  "headline": "What's new in Spenzy",
  "footerTitle": "Hey, Tyo again here!",
  "footerMessage": "Thanks for using Spenzy.",
  "sections": [
    {
      "title": "Feature title",
      "description": "Feature description",
      "items": ["Optional bullet"],
      "images": ["images/1.1.7/example.png"]
    }
  ]
}
```

## Notes

- The app now supports:
  - a single remote JSON array
  - a single remote JSON object
  - an index file with `notes` or `versions` pointing to per-version JSON files
- `1.1.7` images are included in this folder today.
- `1.1.3` and `1.1.1` are text-only here because those image assets are not present in the repo yet.
