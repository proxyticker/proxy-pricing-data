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
