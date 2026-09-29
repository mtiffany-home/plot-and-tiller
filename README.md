# EthicScan — Cloudflare Pages deploy guide

## What's in this folder
- `index.html` — the app (no API key inside it)
- `functions/api/claude.js` — Cloudflare Pages Function that holds your
  Anthropic API key server-side and proxies requests to Claude
- (No build step needed — this is a plain static site + one function.)

## 1. Get an API key (if you haven't already)
https://console.anthropic.com → add billing → API Keys → create one.
This is separate from your claude.ai account.

## 2. Push this folder to GitHub
```
cd ethicscan-cf
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/ethicscan.git
git push -u origin main
```

## 3. Connect it in Cloudflare Pages
1. Cloudflare dashboard → Workers & Pages → Create → Pages → Connect to Git.
2. Select the `ethicscan` repo.
3. Build settings:
   - Framework preset: **None**
   - Build command: *(leave blank)*
   - Build output directory: `/`
4. Before deploying (or right after, then redeploy), go to
   **Settings → Environment variables** and add:
   - Key: `ANTHROPIC_API_KEY`
   - Value: your key from step 1
   - Add it for both Production and Preview.
5. Deploy. You'll get a `*.pages.dev` URL immediately.

## 4. Test it
Open the `.pages.dev` URL, run a lookup, confirm results come back.
If something fails, check **Workers & Pages → your project → Functions**
logs (or "Real-time Logs") for errors from `functions/api/claude.js`.

## 5. Custom domain
If the domain is already in your Cloudflare account: Pages project →
Custom domains → Add → pick the domain. DNS is usually automatic since
it's already on Cloudflare.

## 6. Watch usage
Check https://console.anthropic.com for token/web-search cost, especially
in the first few days. The function includes soft rate limiting
(10 requests/minute per IP) — fine for testing, but consider Cloudflare's
built-in Rate Limiting rules for anything public-facing at scale.

## Ongoing development
Every `git push` to `main` triggers an automatic redeploy — that's your
version control and deployment pipeline in one. Consider working in a
branch and using Pages' automatic **preview deployments** (a unique URL
per branch/PR) to test changes before merging to `main`.
