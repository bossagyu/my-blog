# GitHub Pages Migration Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Prepare the repository for GitHub Pages hosting by adding CNAME support and updating Hugo version, enabling DNS migration from Netlify.

**Architecture:** Two changes to `.github/workflows/gh-pages.yml`: add `cname` parameter to preserve custom domain across deploys, and update Hugo version to 0.120.3 for build parity. DNS migration is a manual step documented in the design doc.

**Tech Stack:** GitHub Actions, peaceiris/actions-gh-pages, Hugo 0.120.3

---

### Task 1: Update GitHub Actions workflow

**Files:**
- Modify: `.github/workflows/gh-pages.yml:21-34`

**Step 1: Update Hugo version**

Change line 23 from:
```yaml
          hugo-version: '0.119.0'
```
To:
```yaml
          hugo-version: '0.120.3'
```

**Step 2: Add CNAME parameter to Deploy step**

Add `cname: bossagyu.com` to the Deploy step's `with` block. Change lines 30-34 from:
```yaml
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
To:
```yaml
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          cname: bossagyu.com
```

**Step 3: Verify Hugo build locally**

Run: `hugo --minify`
Expected: Build succeeds without errors (211 EN / 218 JP pages)

**Step 4: Commit**

```bash
git add .github/workflows/gh-pages.yml
git commit -m "feat: add CNAME for GitHub Pages and update Hugo to 0.120.3"
```

---

### Task 2: Verify deployment pipeline

**Step 1: Push branch and create PR**

```bash
git push -u origin feat/github-pages-migration
gh pr create --title "feat: GitHub Pagesへの移行準備" --body "..."
```

**Step 2: Verify GitHub Actions build succeeds**

Check the PR's Actions tab. Expected: Build succeeds (Hugo 0.120.3).

**Step 3: Merge PR**

After build passes, merge to main.

**Step 4: Verify CNAME file in gh-pages branch**

After the main branch deploys:
```bash
gh api repos/bossagyu/my-blog/contents/CNAME?ref=gh-pages
```
Expected: CNAME file exists with content `bossagyu.com`

---

### Task 3: DNS Migration (Manual — documented for reference)

This task is performed manually in the Netlify DNS dashboard and GitHub Settings. Not automatable via code.

**Step 1: Pre-configure GitHub Pages custom domain**

Go to GitHub repo Settings > Pages > Custom domain: enter `bossagyu.com`. It will fail DNS check — that's expected.

**Step 2: Update DNS records in Netlify DNS**

Delete existing A records:
- `52.74.6.109`
- `13.215.239.219`

Add GitHub Pages A records:
- `185.199.108.153`
- `185.199.109.153`
- `185.199.110.153`
- `185.199.111.153`

Add AAAA records (IPv6):
- `2606:50c0:8000::153`
- `2606:50c0:8001::153`
- `2606:50c0:8002::153`
- `2606:50c0:8003::153`

Change www record:
- Type: CNAME
- Value: `bossagyu.github.io`

**Step 3: Verify DNS propagation**

Run: `dig bossagyu.com +short`
Expected: GitHub Pages IPs (185.199.108-111.153)

**Step 4: Wait for HTTPS certificate**

Check GitHub Pages settings for the green checkmark. Takes 5-60 minutes.
Enable "Enforce HTTPS" once available.

**Step 5: Verify site**

```bash
curl -sI https://bossagyu.com | head -5
```
Expected: HTTP/2 200, no `server: Netlify` header

Test pages:
- https://bossagyu.com/ (JP home)
- https://bossagyu.com/en/ (EN home)
- https://bossagyu.com/blog/001-hugo-netlify-build/ (article)
- https://bossagyu.com/blog/010-favicon/ (alias redirect)
- https://www.bossagyu.com/ (www redirect)

**Step 6: Post-migration**

- Resubmit sitemap in Google Search Console
- Keep Netlify site active for 48 hours
- Monitor for 404s or certificate errors
