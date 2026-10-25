# Deploy: Cloudflare Pages (staging — no domain yet)

Built to be uploaded to Cloudflare Pages while the domain is still being bought.
The site is **not indexed** until the domain is live (robots.txt + headers + meta noindex).

## 1) What was prepared for the no-index rule (already applied)

| File | Rule | What it does |
|---|---|---|
| `site/dist/robots.txt` | `User-agent: *` + `Disallow: /` | Search engines refuse to crawl the staging site |
| `site/public/_headers` | `/*` + `X-Robots-Tag: noindex` | Cloudflare adds `X-Robots-Tag: noindex` to every response |
| `site/src/pages/*.astro` | `<meta name="robots" content="noindex, nofollow">` | Page-level noindex (already in every template) |
| `site/src/data/site.json` | `noindex: true` | Build flag keeps staging out of index |

## 2) Upload to Cloudflare Pages (GitHub integration)

1. Push this folder to a GitHub repository (or import it).
2. Cloudflare Pages → **Create → Connect to GitHub** → pick the repo.
3. **Build settings**
   - Framework preset: Astro
   - Build command: `npm run build`
   - Output directory: `dist`
   - Node version: `24` (project uses Node 24)
4. Save and wait for the deploy. It finishes in ~30 s.

## 3) After deploy — verification

```
# live staging URL, e.g. https://eastbay-insulation.pages.dev/
curl -sI https://eastbay-insulation.pages.dev/ | grep -iE 'robots|x-robots|200'
```

Expected: `robots.txt` → `Disallow: /` and every page header has
`X-Robots-Tag: noindex`. Do not type a domain on this step.

## 4) Buying the domain (later)

1. Register `eastbayinsulation.com` (Doa of .com is already pending, contact owner).
2. Cloudflare Pages → **Custom domains** → add the domain,
   change DNS `CNAME / A` to the Pages hostname, enable HTTPS.
3. Remove the noindex rules (they stay off until the domain is live).
4. Set the production URL in `site/src/data/site.json` → `site_url`.

## 5) Local rebuild (one command)

```bash
cd sites/eastbay-insulation/site
npm run build        # → dist/, 52 pages
npm run preview      # → http://localhost:4321/
```

> Note: node 24 is required (the project was built with Node 24.22.0);
> `npm install` on an older Node may fail on optional dependencies.
