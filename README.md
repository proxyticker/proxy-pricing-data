https://proxyticker.com

# proxy-pricing-data

Open dataset of residential proxy pricing, published daily by
[ProxyTicker](https://proxyticker.com) — an independent price index for residential
proxies. This repository is a daily mirror of the data behind the site — coverage is
growing towards the whole residential proxy market, not stopping at today's list.

This repository is intentionally data-only: no scraper code, no application logic, just
the machine-readable snapshot behind the site.
`.github/workflows/publish.yml` only copies the published files from the API — no
scraping or application code lives here. Every provider tracked on the site is
included here with its headline price — nothing is filtered out to make the numbers look
better. Deeper data (effective price with its full breakdown, tiers, historical charts,
provider comparisons) lives on [proxyticker.com](https://proxyticker.com).

## Files

- **`current.json`** — current snapshot of headline prices for every tracked provider.
  The headline price is the advertised price at the 50 GB reference tier (not a "from $X"
  marketing figure — those aren't comparable across providers).
- **`providers.json`** — the provider directory: names, slugs, and other static metadata
  that doesn't change with a daily price update.

Both files share the same envelope:

```json
{
  "generated_at": "2026-09-22T04:00:00Z",
  "data_changed_at": "2026-09-20T04:00:00Z",
  "license": "CC-BY-4.0",
  "attribution": "ProxyTicker, https://proxyticker.com",
  "providers": []
}
```

`generated_at` is when the file was written; `data_changed_at` is when the underlying data
last actually changed (so you can tell freshness from a file that's regenerated daily but
whose numbers didn't move).

## Provider status

Every provider in both files carries a `status`:

- **`active`** — currently tracked; prices are checked daily and `headline_price`,
  `checked_at`, and `source_url` in `current.json` are populated.
- **`price_not_public`** — the provider no longer publishes pricing we can scrape (for
  example, quote-only or behind a login).
- **`rebranded`** — reserved for a future rename-in-place case (same `slug`, new
  `name`); not currently assigned to any provider.
- **`seized`** — taken down by law enforcement.
- **`defunct`** — no longer operating.

Only `active` providers with a price on record have `headline_price`/`checked_at`/
`source_url` set; for every other status those three fields are `null`. Providers are
never removed from these files when their status changes — the record stays, with an
updated `status`.

Staleness isn't a status: a provider can be `active` with a `checked_at` that's older
than usual if the last check failed or was skipped. Compare `checked_at` (or
`data_changed_at` for the whole file) against the current time to judge freshness
yourself.

## Update frequency

Data is collected once a day, every day at **04:00 UTC**. There is no embargo or delay —
this dataset is published at the same time the numbers go live on the site.

## License and attribution

This dataset is licensed under [Creative Commons Attribution 4.0 International
(CC BY 4.0)](LICENSE). You are free to share and adapt it for any purpose, including
commercially, as long as you give appropriate credit. When you use this data, please
attribute it as:

> Data from [ProxyTicker](https://proxyticker.com), licensed under CC BY 4.0.

## Usage examples

curl:

```bash
curl -s https://proxyticker.com/api/v1/current.json | jq .
curl -s https://proxyticker.com/api/v1/providers.json | jq .
```

Python:

```python
import httpx

resp = httpx.get("https://proxyticker.com/api/v1/current.json")
data = resp.json()
print(data["attribution"], data["generated_at"])
for provider in data["providers"]:
    print(provider)
```
