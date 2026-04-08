# Website Redesign: Implementation Plan

## Current State

The site currently uses **al-folio**, a feature-rich academic Jekyll theme. While powerful, it is heavy (30+ plugins, 600+ line config, slow builds, complex dependency chain) and carries significant visual and structural complexity that isn't needed for the amount of content on the site.

### Content to Preserve
- **Profile**: Name, photo, affiliation (Purdue, advisor), bio
- **Research interests**: Energy-efficient Edge Computing, NPU Architecture
- **Education**: Ph.D. (Purdue, 2025–), M.S. (Korea Univ, 2025), B.S. (Korea Univ, 2023)
- **Publications**: 3 papers (ICEdge 2025, ISLPED 2024 w/ Best Paper, IEEE TCC 2022)
- **Honors & Awards**: 5 items
- **Social links**: GitHub, LinkedIn, Google Scholar, email
- **CV**: PDF download

### New Features Requested
- **Blog / Photos section** for academic trip posts
- **Web-based post creation** (not just via source code)

---

## Template Candidates

### Option A: Hugo + PaperMod (Recommended)

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/adityatelange/hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod) (10k+ stars) |
| **Demo** | [adityatelange.github.io/hugo-PaperMod](https://adityatelange.github.io/hugo-PaperMod/) |
| **Style** | Extremely clean, minimal, fast. White space-focused. |
| **Homepage** | "Profile mode" — centered photo, name, bio, social links. Perfect for academic landing page. |
| **Blog** | First-class blog support with archives, tags, categories, search (Fuse.js). |
| **Mobile** | Fully responsive, very lightweight (~50KB total). |
| **Dark mode** | Built-in toggle + auto-detect from browser. |
| **Build speed** | Hugo builds in milliseconds (vs. Jekyll's seconds/minutes). |
| **Deployment** | GitHub Actions workflow (simple, one-time setup). |

**Pros**: Fastest, simplest, cleanest. Zero JS dependencies. Active community. Profile mode is tailor-made for academic homepages.

**Cons**: Requires learning Hugo templating (similar to Jekyll but different syntax). No built-in BibTeX — publications managed as Markdown (fine for 3 papers; easy to add more).

### Option B: Hugo + Michaillat's Academic Template

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/pmichaillat/hugo-website](https://github.com/pmichaillat/hugo-website) (350 stars) |
| **Demo** | [pascalmichaillat.org](https://pascalmichaillat.org/) |
| **Style** | Ultra-minimal academic site. Text-focused, no decoration. |
| **Homepage** | Clean profile with organized sections (Papers, Courses, Data). |
| **Blog** | Supported via Hugo's built-in blog features. |
| **Mobile** | Responsive, scores 90+ PageSpeed on mobile. |

**Pros**: Designed specifically for academics. Proven on a real professor's site. Excellent performance.

**Cons**: Smaller community (350 stars vs. 10k+). More opinionated structure (Papers/Courses/Data). Less blog-focused.

### Option C: Jekyll + Chirpy

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/cotes2020/jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (8k+ stars) |
| **Demo** | [chirpy.cotes.page](https://chirpy.cotes.page/) |
| **Style** | Clean sidebar layout. Blog-first design with pinned posts, categories, tags. |
| **Homepage** | Recent posts list with sidebar showing profile and navigation. |
| **Blog** | Excellent — built for technical writing. TOC, syntax highlighting, archives. |
| **Mobile** | Responsive with collapsible sidebar. |
| **Dark mode** | Built-in. |

**Pros**: Stays in Jekyll ecosystem (no migration learning curve). Best-in-class blog features. Sidebar keeps navigation always visible.

**Cons**: Blog-first rather than portfolio-first — homepage shows posts, not profile. Sidebar layout may feel less "academic homepage" and more "tech blog." Slower builds than Hugo.

### Recommendation: **Option A (Hugo + PaperMod)**

PaperMod's profile mode gives the clean academic landing page you need, the blog is excellent, and the site will be dramatically simpler and faster than the current al-folio setup. The migration is straightforward since your content is small.

---

## Web-Based Post Creation

For writing blog posts directly through the web (without editing source code locally):

### Primary: Pages CMS (Recommended)

- **Site**: [pagescms.org](https://pagescms.org/)
- **How it works**: A Notion-like block editor that commits directly to your GitHub repo. You configure content types in a `.pages.yml` file, then access the CMS at `pagescms.org` with your GitHub login.
- **Setup**: Add one config file (`.pages.yml`) to your repo. No server needed.
- **Pros**: 100% free, open source, modern UI, drag-and-drop image upload, configurable front matter fields, no OAuth proxy needed.
- **Cons**: Relatively new project (but actively maintained, 3.5k stars).

### Fallback: GitHub.dev Web Editor

- Press `.` on your repo page in GitHub to open a full VS Code editor in the browser.
- Create Markdown files, edit front matter, commit — all from the browser.
- **Pros**: Zero setup, always available, built into GitHub.
- **Cons**: No WYSIWYG preview of the rendered site; you need to know Markdown and front matter format.

### Considered but not recommended

| Tool | Why not |
|------|---------|
| **Decap CMS** (formerly Netlify CMS) | Requires an OAuth proxy server when not on Netlify hosting. No longer actively maintained. |
| **Sveltia CMS** | Still in beta. Same OAuth complexity as Decap on GitHub Pages. |
| **Prose.io** | Unmaintained since ~2016. Could break at any time. |
| **JekyllPad** | Free tier limited to 5 posts/month. |

---

## Anticipated Design

See **`mockup.html`** in the repo root — open it in a browser to see the visual mockup.

### Design Principles
1. **Single-column layout** (max-width ~720px) — easy to read on any device
2. **Minimal navigation** — just 4 links: About, Publications, Blog, CV
3. **No heavy frameworks** — no Bootstrap, no jQuery, no Material Design
4. **System fonts** — fast loading, native feel
5. **Automatic dark mode** — follows browser/OS preference
6. **Card-based blog** — photo thumbnails for trip posts, tags for categorization

### Page Structure

```
Home (/)
├── Profile hero (photo, name, subtitle, bio, social links)
├── Interests + Education (two-column grid)
├── Publications (grouped by year, with venue badges)
├── Honors & Awards
└── Recent Posts preview (2 cards, link to full blog)

Blog (/blog/)
├── Post list with thumbnails, dates, tags
└── Pagination

Blog Post (/blog/2026/post-title/)
├── Title, date, tags
├── Cover photo
├── Content (Markdown rendered)
└── Related posts

CV (/cv/)
└── PDF embed or download link
```

---

## Implementation Steps

### Phase 1: Setup (Day 1)

1. **Install Hugo** locally (`brew install hugo`)
2. **Create new Hugo site** in a separate branch (`redesign`)
3. **Add PaperMod theme** as a Git submodule
4. **Configure `hugo.yml`**:
   - Site title, description, URL
   - Profile mode for homepage (photo, bio, social links)
   - Menu items (About, Publications, Blog, CV)
   - Dark mode toggle
   - Search (Fuse.js)

### Phase 2: Content Migration (Day 1–2)

5. **Create homepage content** (`content/_index.md`):
   - Profile info configured in `hugo.yml` params
6. **Create Publications page** (`content/publications.md`):
   - All 3 papers as structured Markdown with venue badges
7. **Create Honors section** (either on homepage or separate page)
8. **Migrate profile photo** (`assets/img/prof_pic.jpeg` → `static/img/`)
9. **Set up CV page** (`content/cv.md`) with PDF download link
10. **Copy over `assets/pdf/` directory** for CV PDF

### Phase 3: Blog Setup (Day 2)

11. **Create blog section** (`content/blog/`):
    - Configure blog list layout with thumbnails
    - Set up tags and categories (e.g., `conference`, `trip`, `life`)
12. **Create sample blog post** with front matter template:
    ```yaml
    ---
    title: "ISLPED 2024 Trip Report"
    date: 2024-08-15
    tags: ["conference", "trip", "award"]
    cover:
      image: "img/islped-2024.jpg"
      alt: "ISLPED 2024 venue"
    ---
    ```
13. **Set up image handling** for trip photos (Hugo's built-in image processing)

### Phase 4: Web-Based CMS (Day 2–3)

14. **Add Pages CMS config** (`.pages.yml`):
    ```yaml
    media:
      input: static/img/blog
      output: /img/blog
    content:
      - name: posts
        label: Blog Posts
        path: content/blog
        type: collection
        fields:
          - name: title
            label: Title
            type: string
          - name: date
            label: Date
            type: date
          - name: tags
            label: Tags
            type: string
            list: true
          - name: cover.image
            label: Cover Image
            type: image
          - name: body
            label: Content
            type: rich-text
    ```
15. **Test CMS workflow**: Create a post via pagescms.org, verify it appears on site

### Phase 5: Deployment (Day 3)

16. **Set up GitHub Actions** for Hugo deployment:
    - Create `.github/workflows/hugo.yml`
    - Configure to build on push to `master`
    - Deploy to GitHub Pages
17. **Test deployment** on the `redesign` branch first
18. **Clean up**: Remove all al-folio files from the repo
19. **Merge** `redesign` branch to `master`
20. **Verify** site is live at hskim1324.github.io

### Phase 6: Polish (Day 3–4)

21. **Custom CSS overrides** if needed (PaperMod supports `assets/css/extended/`)
22. **SEO meta tags** (Open Graph, Twitter cards)
23. **Google Analytics** migration (keep existing GA ID: G-SQVKVZBEEK)
24. **Favicon** setup
25. **Final mobile testing** across devices

---

## File Structure (Final)

```
hskim1324.github.io/
├── .github/
│   └── workflows/
│       └── hugo.yml              # GitHub Actions deployment
├── .pages.yml                    # Pages CMS configuration
├── archetypes/
│   └── default.md                # Default front matter template
├── assets/
│   └── css/
│       └── extended/
│           └── custom.css        # Custom style overrides
├── content/
│   ├── _index.md                 # Homepage (profile mode config in hugo.yml)
│   ├── publications.md           # Publications page
│   ├── cv.md                     # CV page
│   └── blog/
│       ├── _index.md             # Blog list config
│       ├── islped-2024/
│       │   ├── index.md          # Blog post (page bundle with images)
│       │   └── cover.jpg
│       └── ...
├── static/
│   ├── img/
│   │   ├── profile.jpg
│   │   └── blog/                 # Blog images (for CMS uploads)
│   └── pdf/
│       └── CV.pdf
├── hugo.yml                      # Site configuration
└── themes/
    └── PaperMod/                 # Theme (git submodule)
```

---

## Migration Checklist

- [ ] Back up current site (tag current commit)
- [ ] Install Hugo
- [ ] Initialize new Hugo site with PaperMod
- [ ] Configure homepage profile mode
- [ ] Migrate profile photo
- [ ] Create publications page (3 papers)
- [ ] Create honors section
- [ ] Set up CV page with PDF
- [ ] Create blog section with sample post
- [ ] Configure Pages CMS
- [ ] Set up GitHub Actions deployment
- [ ] Test on staging branch
- [ ] Remove old al-folio files
- [ ] Deploy to production
- [ ] Verify Google Analytics
- [ ] Test mobile responsiveness
- [ ] Test web-based post creation via Pages CMS

---

## Estimated Complexity

| Component | Complexity | Notes |
|-----------|-----------|-------|
| Hugo + PaperMod setup | Low | Well-documented, one config file |
| Content migration | Low | Only 3 papers, 5 awards, 1 page of bio |
| Blog setup | Low | PaperMod has built-in blog support |
| Pages CMS integration | Low | One YAML config file |
| GitHub Actions deployment | Low | Standard Hugo deployment workflow |
| Custom styling | Low–Medium | Minor tweaks via `custom.css` |
| **Total** | **Low** | Achievable in 1–2 focused sessions |
