---
title: How to Set Up a Jekyll Documentation Site on GitHub Pages
date: 2026-05-30 08:00:00 +0100
categories: [Documentation, Jekyll]
tags: [jekyll, github, github-pages, chirpy, self-hosted, documentation]
---

This is a complete guide on how to set up a documentation and blog website exactly like [Techno Tim's](https://technotim.live) using Jekyll, the Chirpy theme, and GitHub Pages — for free.

## What We Are Building

- A static documentation/blog website
- Hosted for free on GitHub Pages
- Using the open source [Chirpy theme](https://github.com/cotes2020/jekyll-theme-chirpy)
- Written in Markdown
- Auto-deployed via GitHub Actions on every push

## Prerequisites

- A [GitHub](https://github.com) account
- Git installed on your machine
- WSL (Windows) or a Linux/macOS terminal
- VS Code (optional but recommended)

---

## Step 1 — Create the Repository from Chirpy Starter

1. Go to [github.com/cotes2020/chirpy-starter](https://github.com/cotes2020/chirpy-starter)
2. Click **"Use this template"** → **"Create a new repository"**
3. Name it exactly: `yourusername.github.io`
4. Set visibility to **Public**
5. Click **"Create repository"**

---

## Step 2 — Enable GitHub Pages

1. In your repo → **Settings**
2. Left sidebar → **Pages**
3. Under **"Build and deployment"** → Source → select **"GitHub Actions"**

GitHub will now auto-build and deploy your site on every push.

---

## Step 3 — Clone the Repo Locally

```bash
cd ~
mkdir dokumentacija
cd dokumentacija
git clone https://YOUR_TOKEN@github.com/yourusername/yourusername.github.io.git
cd yourusername.github.io
```

> Replace `YOUR_TOKEN` with a GitHub Personal Access Token. Generate one at:
> GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token → tick `repo` → copy it.

---

## Step 4 — Install Dependencies (WSL/Ubuntu)

```bash
sudo apt update
sudo apt install -y ruby-full ruby-dev build-essential zlib1g-dev
```

Then install Bundler and Jekyll gems:

```bash
sudo gem install bundler
```

Then install the project dependencies:

```bash
cd yourusername.github.io
sudo bundle install
```

---

## Step 5 — Configure Git Identity

```bash
git config --global user.email "your@email.com"
git config --global user.name "Your Name"
```

This only needs to be done once.

---

## Step 6 — Configure Your Site

Edit `_config.yml` in VS Code and update these key fields:

```yaml
title: Your Site Name
tagline: Your subtitle here
description: A documentation website

# Remove trailing slash!
url: "https://yourusername.github.io"

timezone: Europe/Zagreb

github:
  username: yourusername

social:
  name: Your Name
  email: your@email.com
  links:
    - https://github.com/yourusername
```

> **Important:** The `url` field must be set correctly or posts will not appear on the homepage.

---

## Step 7 — Preview Locally

Start the local Jekyll server:

```bash
bundle exec jekyll serve
```

Open your browser and go to:

```
http://localhost:4000
```

You will see a live preview of your site. Jekyll auto-reloads when you save files — just refresh the browser to see changes.

> If config changes don't appear, stop the server with `Ctrl+C` and restart it.

---

## Step 8 — Create Your First Post

Create a new file in the `_posts/` folder. The filename **must** follow this exact format:

```
YYYY-MM-DD-your-post-title.md
```

For example: `2026-05-30-hello-world.md`

Every post needs a frontmatter block at the very top:

```markdown
---
title: Hello World
date: 2026-05-30 08:00:00 +0100
categories: [Category, Subcategory]
tags: [tag1, tag2, tag3]
---

Your content goes here in Markdown!

## A Heading

Some text, **bold**, *italic*, `inline code`.

```bash
sudo apt update && sudo apt upgrade -y
```
```

### Important rules for posts:

- Always include `---` before and after the frontmatter
- Set the date/time **in the past** — Jekyll hides future-dated posts!
- Use `00:00:00 +0100` as the time to be safe
- Never rename post files after creation (can cause corruption on WSL)
- Always create post files with the correct name from the start

---

## Step 9 — Push to GitHub

Once you are happy with your changes:

```bash
git add .
git commit -m "add first post"
git push
```

GitHub Actions will automatically build and deploy your site in about 40 seconds. Check the **Actions** tab in your repo to monitor the build.

---

## Step 10 — View Your Live Site

Once the Actions build shows a green tick, visit:

```
https://yourusername.github.io
```

Do a hard refresh with **Ctrl+Shift+R** if you don't see changes immediately.

---

## Daily Workflow

From now on, every time you want to add or edit content:

1. Create or edit `.md` files in `_posts/`
2. Preview locally with `bundle exec jekyll serve`
3. When happy, push to GitHub:

```bash
git add .
git commit -m "describe what you changed"
git push
```

That's it — GitHub does the rest automatically.

---

## Optional — Add a Custom Domain

If you buy a domain (e.g. from Namecheap or Cloudflare):

1. Go to repo → **Settings → Pages → Custom domain** → enter `yourdomain.com`
2. At your registrar, add these DNS **A records**:

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

3. Add a **CNAME record**: `www` → `yourusername.github.io`
4. Check **"Enforce HTTPS"** in GitHub Pages settings

DNS propagation can take up to 24-48 hours.

---

## Useful References

| Resource | URL |
|---|---|
| Techno Tim's site source | [github.com/techno-tim/techno-tim.github.io](https://github.com/techno-tim/techno-tim.github.io) |
| Chirpy starter template | [github.com/cotes2020/chirpy-starter](https://github.com/cotes2020/chirpy-starter) |
| Chirpy theme docs | [chirpy.cotes.page](https://chirpy.cotes.page) |
| Techno Tim's Jekyll video | [technotim.live/posts/jekyll-docs-site](https://technotim.live/posts/jekyll-docs-site/) |
| Writing posts guide | [chirpy.cotes.page/posts/write-a-new-post](https://chirpy.cotes.page/posts/write-a-new-post/) |