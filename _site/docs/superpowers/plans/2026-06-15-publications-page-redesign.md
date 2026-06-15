# Publications Page Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the Publications page so it renders compact, locally maintained academic publication entries from `_publications/*.md`.

**Architecture:** Keep `_publications/*.md` as the data source. Add one publication-specific Liquid include and publication-specific SCSS so the page no longer depends on the shared archive item template or on Google Scholar data.

**Tech Stack:** Jekyll 3.10, Liquid templates, SCSS under `_sass`, existing Academic Pages layout.

---

## File Structure

- Create: `_includes/publication-single.html`
  - Responsibility: Render one publication list item from a `post` object.
  - Inputs: `post.title`, `post.url`, `post.venue_tag`, `post.authors`, `post.venue`, `post.year`, `post.date`, `post.rank`, and optional link fields.
  - Behavior: Skip empty optional fields and keep old publication records renderable.

- Create: `_sass/layout/_publications.scss`
  - Responsibility: Style only publication list entries.
  - Boundary: Use `.publication-list` and `.publication-list__*` classes so archive, blog, portfolio, and teaching pages are unaffected.

- Modify: `assets/css/main.scss`
  - Responsibility: Import `layout/publications` after `layout/archive`.

- Modify: `_pages/publications.html`
  - Responsibility: Use `_includes/publication-single.html` for `site.publications` entries while preserving category headings.

- Leave unchanged: `_includes/archive-single.html`
  - Reason: It is shared by other archive views and should not carry publication-specific presentation logic.

---

### Task 1: Add a Pre-Change Smoke Check

**Files:**
- No file changes.

- [ ] **Step 1: Build the current site**

Run:

```powershell
bundle exec jekyll build --trace
```

Expected: exit code `0`.

- [ ] **Step 2: Verify the new publication class is absent before implementation**

Run:

```powershell
Select-String -Path _site\publications\index.html -Pattern 'publication-list__item'
```

Expected: no matches. This proves the later selector check can detect the new template output.

---

### Task 2: Create the Publication Item Include

**Files:**
- Create: `_includes/publication-single.html`

- [ ] **Step 1: Add the include**

Create `_includes/publication-single.html` with:

```liquid
{% include base_path %}

{% assign publication_year = post.year %}
{% unless publication_year %}
  {% assign publication_year = post.date | default: "1900-01-01" | date: "%Y" %}
{% endunless %}

<div class="publication-list__item">
  <article class="publication-item" itemscope itemtype="http://schema.org/ScholarlyArticle">
    <h2 class="publication-item__title" itemprop="headline">
      {% if post.venue_tag %}
        <span class="publication-item__badge">{{ post.venue_tag }}</span>
      {% endif %}
      <a href="{{ base_path }}{{ post.url }}" rel="permalink">{{ post.title }}</a>
    </h2>

    {% if post.authors %}
      <p class="publication-item__authors" itemprop="author">{{ post.authors }}</p>
    {% endif %}

    {% if post.venue or publication_year or post.rank %}
      <p class="publication-item__venue">
        {% if post.venue %}{{ post.venue }}{% endif %}{% if publication_year %} ({{ publication_year }}){% endif %}{% if post.rank %}, {{ post.rank }}{% endif %}
      </p>
    {% endif %}

    {% if post.paperurl or post.codeurl or post.projecturl or post.bibtexurl or post.scholarurl %}
      <p class="publication-item__links">
        {% if post.paperurl %}<a href="{{ post.paperurl }}">Paper</a>{% endif %}
        {% if post.codeurl %}<a href="{{ post.codeurl }}">Code</a>{% endif %}
        {% if post.projecturl %}<a href="{{ post.projecturl }}">Project</a>{% endif %}
        {% if post.bibtexurl %}<a href="{{ post.bibtexurl }}">BibTeX</a>{% endif %}
        {% if post.scholarurl %}<a href="{{ post.scholarurl }}">Google Scholar</a>{% endif %}
      </p>
    {% endif %}
  </article>
</div>
```

- [ ] **Step 2: Check the include syntax visually**

Confirm these fields appear exactly once:

```text
publication-list__item
publication-item__badge
publication-item__authors
publication-item__venue
publication-item__links
```

---

### Task 3: Switch the Publications Page to the New Include

**Files:**
- Modify: `_pages/publications.html`

- [ ] **Step 1: Replace publication list include calls**

In `_pages/publications.html`, replace both occurrences of:

```liquid
{% include archive-single.html %}
```

with:

```liquid
{% include publication-single.html %}
```

- [ ] **Step 2: Keep category grouping unchanged**

Confirm this category loop remains intact:

```liquid
{% if site.publication_category %}
  {% for category in site.publication_category  %}
```

Expected: `Conference Papers` and other configured headings still come from `_config.yml`.

---

### Task 4: Add Publication-Specific Styles

**Files:**
- Create: `_sass/layout/_publications.scss`
- Modify: `assets/css/main.scss`

- [ ] **Step 1: Add SCSS file**

Create `_sass/layout/_publications.scss` with:

```scss
/* ==========================================================================
   PUBLICATIONS
   ========================================================================== */

.publication-list__item {
  margin: 0 0 1.35rem;
}

.publication-item {
  font-size: $type-size-6;
  line-height: 1.45;
}

.publication-item__title {
  margin: 0 0 0.25rem;
  font-family: $global-font-family;
  font-size: $type-size-5;
  line-height: 1.35;

  a {
    text-decoration: underline;
  }
}

.publication-item__badge {
  display: inline-block;
  margin-right: 0.35rem;
  padding: 0.08rem 0.35rem;
  border-radius: 3px;
  background-color: var(--global-link-color);
  color: var(--global-bg-color);
  font-size: 0.72em;
  font-weight: 700;
  line-height: 1.35;
  vertical-align: 0.08em;
}

.publication-item__authors,
.publication-item__venue,
.publication-item__links {
  margin: 0.2rem 0 0;
}

.publication-item__authors {
  color: var(--global-text-color);
}

.publication-item__venue {
  color: var(--global-text-color-light);

  &::before {
    content: "- ";
  }
}

.publication-item__links {
  font-size: 0.9em;

  a {
    margin-right: 0.6rem;
    white-space: nowrap;
  }
}
```

- [ ] **Step 2: Import the new SCSS**

In `assets/css/main.scss`, add `layout/publications` immediately after `layout/archive`:

```scss
    "layout/page",
    "layout/archive",
    "layout/publications",
    "layout/sidebar",
```

---

### Task 5: Build and Verify

**Files:**
- Generated output: `_site/publications/index.html`

- [ ] **Step 1: Build the site**

Run:

```powershell
bundle exec jekyll build --trace
```

Expected: exit code `0`.

- [ ] **Step 2: Verify the new template rendered**

Run:

```powershell
Select-String -Path _site\publications\index.html -Pattern 'publication-list__item|publication-item__title|publication-item__venue'
```

Expected: matches for all three classes.

- [ ] **Step 3: Verify the old long citation block is gone from the list page**

Run:

```powershell
Select-String -Path _site\publications\index.html -Pattern 'Recommended citation'
```

Expected: no matches.

- [ ] **Step 4: Verify category heading still renders**

Run:

```powershell
Select-String -Path _site\publications\index.html -Pattern 'Conference Papers'
```

Expected: at least one match.

- [ ] **Step 5: Inspect in browser**

Run the local server:

```powershell
bundle exec jekyll serve -l -H localhost
```

Open:

```text
http://localhost:4000/publications/
```

Expected visual result:

- Each paper appears as a compact entry.
- Missing optional fields do not produce empty labels.
- The title remains clickable.
- The page stays readable in the existing site theme.

---

### Task 6: Commit the Implementation

**Files:**
- Add: `_includes/publication-single.html`
- Add: `_sass/layout/_publications.scss`
- Modify: `_pages/publications.html`
- Modify: `assets/css/main.scss`

- [ ] **Step 1: Review the diff**

Run:

```powershell
git diff -- _includes\publication-single.html _sass\layout\_publications.scss _pages\publications.html assets\css\main.scss
```

Expected: only the publication rendering implementation appears.

- [ ] **Step 2: Commit the implementation**

Run:

```powershell
git add _includes\publication-single.html _sass\layout\_publications.scss _pages\publications.html assets\css\main.scss
git commit -m "Redesign publications list"
```

Do not include `_site/`, `.sass-cache/`, `.bundle/`, or `vendor/` in this commit.

---

## Self-Review

- Spec coverage: The plan keeps `_publications/*.md` as the data source, adds a publication-specific include, updates the Publications page, adds publication-specific styles, preserves category headings, and avoids Google Scholar as a data source.
- Placeholder scan: No `TBD`, `TODO`, or undefined implementation steps remain.
- Scope check: This is one focused static-page rendering change. It does not redesign individual publication pages or fetch external data.
