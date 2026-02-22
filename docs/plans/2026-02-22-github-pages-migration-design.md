# GitHub Pages Migration Design

## Problem

bossagyu.com is hosted on Netlify (free plan) with only 6 CDN POPs worldwide. US and other international visitors experience slow load times, negatively impacting SEO and user experience. Netlify-specific features (redirects, forms, functions) are not used.

## Decision

Migrate hosting from Netlify to GitHub Pages (Fastly CDN, 80+ POPs). The GitHub Actions deployment pipeline already exists (`gh-pages.yml`).

## Trade-offs Accepted

- **HTTP header customization not available**: GitHub Pages serves all assets with `Cache-Control: max-age=600` (10 min). Cannot set long cache times for fingerprinted assets or security headers (CSP, X-Frame-Options). Accepted because CDN performance gain outweighs caching limitation for a personal blog.
- **No deploy previews**: PR preview URLs will not be generated. Accepted for a single-author blog.
- **No one-click rollback**: Must redeploy via GitHub Actions. Accepted as rollback is still fast.
- **HTTPS gap during migration**: 5-60 minute window where HSTS-enforced browsers may see errors. Mitigated by migrating during low-traffic hours.

## Code Changes

### 1. Add CNAME parameter to GitHub Actions workflow

Add `cname: bossagyu.com` to the peaceiris/actions-gh-pages deploy step. Without this, every deployment overwrites the gh-pages branch and removes the CNAME file, causing the custom domain to reset.

### 2. Update Hugo version in workflow

Update `hugo-version` from `0.119.0` to `0.120.3` to match `netlify.toml` and ensure build parity.

## DNS Migration (Manual)

### Pre-migration
- Verify GitHub Pages works at bossagyu.github.io/my-blog/
- Merge code changes (CNAME + Hugo version) to main
- Verify deployment succeeds with CNAME file in gh-pages branch

### Migration Day (low-traffic hours)
1. GitHub Settings > Pages: set custom domain to `bossagyu.com`
2. Netlify DNS: delete old A records (52.74.6.109, 13.215.239.219)
3. Netlify DNS: add A records for GitHub Pages (185.199.108-111.153)
4. Netlify DNS: add AAAA records for IPv6
5. Netlify DNS: change www to CNAME `bossagyu.github.io`
6. Wait for DNS propagation (~2-5 min, TTL is already 120s)
7. Wait for HTTPS certificate provisioning (5-60 min)
8. Enable "Enforce HTTPS" in GitHub Pages settings
9. Test all pages (JP, EN, aliases)

### Post-migration
- Keep Netlify site active for 48h as fallback
- Resubmit sitemap in Google Search Console
- Monitor crawl stats for 2 weeks
- Optionally increase DNS TTL to 3600s

## Rollback Plan

Revert Netlify DNS A records to original IPs. Propagation ~2 min (TTL 120s). Keep Netlify site active until confirmed stable.

## Success Criteria

- `curl -I https://bossagyu.com` shows GitHub Pages headers (no `server: Netlify`)
- HTTPS works with valid certificate
- All JP and EN pages load correctly
- Hugo alias redirects work
- www redirects to apex domain
