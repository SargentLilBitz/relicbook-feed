# RelicBook feed schema v1

The deals plates are frozen background art. Slot text is rendered at runtime from this feed. An empty `deals` array means every box on that plate stays a skeleton: blank title line, blank price line, empty rite seal.

```json
{
  "schema": 1,
  "issue": "0",
  "updatedAt": "2026-11-05T00:00:00.000Z",
  "categories": {
    "caf": {
      "title": "CAF",
      "plate": "a",
      "deals": []
    }
  }
}
```

## Categories

Required keys, in fill order: `caf`, `grocery`, `meals`, `dining`, `travel`, `fun`, `cards`, `online`, `vault`.

`plate` is `"a"` (8 row slots) or `"b"` (6 grid slots). Deals from categories that share a plate are packed in that key order. If a plate has more deals than one leaf of slots, the book adds overflow leaves that reuse the same plate texture.

## Deal row

| Field | Rule |
| --- | --- |
| `id` | Stable slug, non-empty string |
| `title` | String. Long titles ellipsize in the slot |
| `merchant` | String |
| `price` | `null`, or `{ "now": number, "was": number or null, "currency": "USD" }` |
| `rite` | `CLIP`, `CODE`, `SHOW`, `APP`, `LINK`, `BOOK`, or `BEST` |
| `code` | String or `null` |
| `url` | Must be `https://`. The whole slot opens it in a new tab |
| `image` | String or `null` |
| `verifiedAt` | ISO timestamp. Missing drops the row |
| `expiresAt` | ISO timestamp or `null`. A past time drops the row |
| `verifiedBy` | String |
| `notes` | String |

Rows that fail those rules are dropped. They are not shown as broken slots.

## Loading

Default `VITE_FEED_URL` is `https://raw.githubusercontent.com/SargentLilBitz/relicbook-feed/main/issue-0.json`. If that host 404s or the network fails, the app warns once and uses the bundled empty `/feed/issue-0.json`. Fetched at startup and when the window regains focus. The service worker uses NetworkFirst (3 second timeout, then cache) for `/feed/`, `issue-0.json`, and `raw.githubusercontent.com`, so a published feed change does not need an app rebuild.

The feed may live on another origin. Fetch is CORS-safe (`mode: "cors"`, no cookies).

Lint a file before publishing:

```bash
node scripts/validate-feed.mjs public/feed/issue-0.json
```
