# CMS Admin Panel Setup Guide

This guide walks you through the one-time setup to enable the "Login with GitHub" button on your site's CMS admin panel at `https://hskim1324.github.io/admin/`.

## Overview

Your site has a built-in CMS admin panel (Sveltia CMS) at `/admin/`. To log in, you need a small OAuth proxy because GitHub's OAuth flow requires a server-side component to exchange auth codes for access tokens. The recommended (and free) approach is a **Cloudflare Worker**.

**Total cost: $0/month** (Cloudflare Workers free tier: 100,000 requests/day).

---

## Step 1: Deploy the OAuth Proxy on Cloudflare

1. Go to [github.com/sveltia/sveltia-cms-auth](https://github.com/sveltia/sveltia-cms-auth)
2. Click the **"Deploy to Cloudflare Workers"** button in the README
3. Sign up for a free Cloudflare account if you don't have one
4. Follow the prompts to complete deployment
5. Once deployed, you'll get a Worker URL like:
   ```
   https://sveltia-cms-auth.<YOUR-SUBDOMAIN>.workers.dev
   ```
6. **Save this URL** — you'll need it in the next steps

## Step 2: Create a GitHub OAuth App

1. Go to [github.com/settings/applications/new](https://github.com/settings/applications/new)
2. Fill in the form:

   | Field | Value |
   |-------|-------|
   | **Application name** | `Site CMS` (or any name you like) |
   | **Homepage URL** | `https://hskim1324.github.io` |
   | **Authorization callback URL** | `https://sveltia-cms-auth.<YOUR-SUBDOMAIN>.workers.dev/callback` |

   > **Important:** The callback URL must be your Worker URL with `/callback` appended. No trailing slash. Must match exactly.

3. Click **Register application**
4. Copy the **Client ID**
5. Click **Generate a new client secret** and copy the **Client Secret** immediately (shown only once)

## Step 3: Configure the Cloudflare Worker

1. Go to the [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. Navigate to **Workers & Pages** > select your `sveltia-cms-auth` worker
3. Go to **Settings** > **Variables and Secrets**
4. Add these environment variables:

   | Variable | Value | Encrypt? |
   |----------|-------|----------|
   | `GITHUB_CLIENT_ID` | Your Client ID from Step 2 | No |
   | `GITHUB_CLIENT_SECRET` | Your Client Secret from Step 2 | **Yes** |
   | `ALLOWED_DOMAINS` | `hskim1324.github.io` | No |

5. Save and redeploy the Worker

## Step 4: Update the CMS Config

Edit `static/admin/config.yml` in your repo. Uncomment and update the `base_url` line:

```yaml
backend:
  name: github
  repo: hskim1324/hskim1324.github.io
  branch: master
  base_url: https://sveltia-cms-auth.<YOUR-SUBDOMAIN>.workers.dev
```

Replace `<YOUR-SUBDOMAIN>` with your actual Cloudflare Workers subdomain.

> **Do NOT include `/callback` in the `base_url`.** The CMS appends it automatically.

Commit and push this change.

## Step 5: Test

1. Wait for GitHub Pages to rebuild (check the Actions tab)
2. Go to `https://hskim1324.github.io/admin/`
3. Click **"Login with GitHub"**
4. Authorize the OAuth app when prompted
5. You should now see the CMS dashboard with your blog posts

---

## Using the CMS

Once logged in:

- **Create a post**: Click "Blog Posts" > "New Blog Post"
- **Fill in fields**: Title, date, tags, summary, cover image
- **Write content**: Use the visual Markdown editor
- **Upload images**: Drag and drop images directly into the editor
- **Publish**: Click "Publish" — this commits directly to your repo and triggers auto-deploy

---

## Quick Alternative: Token-Based Login

If you don't want to set up OAuth, you can also log in with a GitHub Personal Access Token:

1. Go to [github.com/settings/tokens/new](https://github.com/settings/tokens?type=beta)
2. Create a fine-grained token with **Contents: Read and write** permission for the `hskim1324.github.io` repo
3. On the CMS login page, click **"Sign in with token"** (or similar option)
4. Paste your token

This works immediately with no OAuth proxy, but is less convenient for regular use.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Login with GitHub" does nothing | Check browser console (F12) for errors. Verify `base_url` in config.yml doesn't have `/callback`. |
| OAuth redirect fails | Ensure the callback URL in GitHub OAuth App exactly matches `<WORKER_URL>/callback`. No trailing slash. |
| Blank admin page | Clear browser cache. Check that `static/admin/index.html` exists and references the Sveltia CMS script. |
| Worker returns error | Go to Cloudflare Dashboard > Workers > your worker > Logs to see error details. Verify environment variables are set. |
| Can't upload images | Ensure `static/img/blog/` directory exists in the repo (can be empty with a `.gitkeep` file). |
