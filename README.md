# Spenzy Config

Remote content for the Spenzy app, served as static files by
**Cloudflare Pages** at `https://spenzy-config.pages.dev`.

| File | Used for | Live after edit |
|---|---|---|
| `announcements.json` | In-app bubble: update prompts, incident notices, info | ~1 min |
| `release-notes/index.json` + `<version>.json` | "What's new" bubble & release notes screen | ~5 min |
| `release-notes/images/<version>/…` | Release-note images | ~5 min |

Edit a file on GitHub (web or phone) → commit to `main` → Pages redeploys
automatically. If a fetch fails, the app keeps its last cached copy
(announcements) or the release notes bundled in the build.

## Cloudflare Pages setup (one time)

1. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** → pick `tyodsgn/Spenzy-Release-Notes`.
2. Project name: **`spenzy-config`** (the app expects
   `spenzy-config.pages.dev`; if you pick another name, update
   `ANNOUNCEMENTS_URL`, `RELEASE_NOTES_URL`, `RELEASE_NOTES_IMAGE_BASE_URL`
   in the app's `Spenzy/Info.plist`).
3. Framework preset: **None**. Build command: *(empty)*. Output directory: **`/`**.
4. Save and Deploy.

`_headers` sets cache lifetimes. Pages static requests are free and
unlimited, and don't count against the AI proxy Worker's quota.

## Announcements

`announcements.json` holds a list; the app shows the **highest-priority**
entry that matches the installed version and date window. Keep it
`{"announcements": []}` when there's nothing to say. See
`announcements.example.json` for ready-to-copy examples.

| Field | Required | Notes |
|---|---|---|
| `id` | ✅ | Unique, never reuse. Dismissals are counted per id — a new id shows again. |
| `kind` | ✅ | `info` · `warning` · `critical`. Sets default color; critical can't be closed by default. |
| `title` | ✅ | Bubble text (one line). |
| `body` | | Longer text, shown in the detail card. |
| `badge` | | Pill text, e.g. `Update`, `1.2.4`. Omit for none. |
| `color` | | App color name (`sysRed`, `sysOrange`, `sysCyan`, `sysGreen`, …). Defaults by kind. |
| `minAppVersion` / `maxAppVersion` | | Inclusive range. Target "please update" only at buggy versions. |
| `startsAt` / `endsAt` | | ISO 8601 UTC, e.g. `2026-09-24T00:00:00Z` (no milliseconds). |
| `priority` | | Higher wins. Default 0. |
| `dismissible` | | Override close button. Default: `true` except `critical`. |
| `maxDismissals` | | Times the user can close it before it stops showing. Default 3. |
| `blocking` | | `critical` only: full-screen gate the user can't close. Use with `appStore` action, for severe issues (data loss) only. |
| `action.type` | | `appStore` · `url` · `releaseNotes` · `sheet` (detail card) · `none` |
| `action.url` | | For `url` (or custom App Store link). |
| `action.buttonTitle` | | Detail card button label. |

A malformed entry is skipped by the app; the others still show.
Validate before committing:

```bash
python3 -m json.tool announcements.json > /dev/null && echo ok
```

### Typical: "update now, bug in 1.2.3"

```json
{
  "announcements": [
    {
      "id": "2026-09-scan-fix",
      "kind": "critical",
      "badge": "Update",
      "title": "Update to fix receipt scanning",
      "body": "Version 1.2.3 can save the wrong amount from scanned receipts.",
      "maxAppVersion": "1.2.3",
      "priority": 100,
      "action": { "type": "appStore" }
    }
  ]
}
```

Users on 1.2.4+ never see it. Remove it (or set `endsAt`) once most users
have updated.

## Release notes

- One JSON file per version in `release-notes/`, listed newest-first in
  `release-notes/index.json`.
- Image paths are relative, e.g. `images/1.2.3/rn-pn.webp`.
- The app hides notes for versions newer than the installed build, so you
  can publish a note before the App Store release goes live.
