# atana.lat

Source for the Atana public site at <https://atana.lat>.

**Atana** — evidence-based AI policy for creative economies in Latin America.

The current site is a single-page deployment of *Atana Index Vol. 1: Two Creative Time Zones — Latin America's Cultural Economies in the Agentic Era* (May 2026), with the executive briefing embedded as the homepage and the full PDF available for download.

## Stack

- Static HTML (single self-contained file with base64-inlined charts).
- Hosted on GitHub Pages.
- Custom domain: `atana.lat` (DNS via Cloudflare).

## Files

| File | Purpose |
|------|---------|
| `index.html` | Homepage — full Atana Index Vol. 1 |
| `Atana_Index_Vol1_Two_Creative_Time_Zones.pdf` | Downloadable PDF version |
| `CNAME` | GitHub Pages custom-domain config (`atana.lat`) |
| `.nojekyll` | Disables Jekyll processing (we serve raw HTML) |
| `404.html` | Custom 404 page |

## Local preview

```bash
# Any static server works. Python:
python3 -m http.server 8000
# then open http://localhost:8000
```

## Updates

This is a pre-v1 deploy. Future versions will move from this single-page format to a fuller multi-page site once the brand-designer engagement closes. Until then, content updates land here.

## Contact

João Roque — `joao@atana.lat` · São Paulo · Buenos Aires
