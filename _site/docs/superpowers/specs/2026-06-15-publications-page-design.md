# Publications Page Redesign

## Goal

Redesign the Publications page so it shows locally maintained publication records in a compact academic-list style. The page must not depend on Google Scholar as the source of truth, because some papers may not be indexed there.

## Current Structure

- `_pages/publications.html` renders the Publications page.
- `_publications/*.md` stores each publication as a Jekyll collection item.
- `_includes/archive-single.html` currently renders publication items, but it is shared by other archive pages.

## Data Source

Keep `_publications/*.md` as the publication data source.

Each publication can use structured front matter like:

```yaml
---
title: "FedRAHi: Reliability-Aware Hierarchical Collaboration for Federated Graph Foundation Models"
collection: publications
category: conferences
permalink: /publication/fedrahi
date: 2026-01-01

venue_tag: "KDD 26"
authors: "Xiangkai Zhu, Yeyu Yan, Pengpeng Qiao, Tingrui Pei, Yanchun Li, Saiqin Long"
venue: "ACM Conference on Knowledge Discovery and Data Mining"
year: 2026
rank: "CCF-A"

paperurl:
codeurl:
projecturl:
bibtexurl:
scholarurl:
---
```

Google Scholar is optional metadata only. If `scholarurl` is absent, the publication still renders normally.

## Rendering Design

Add a dedicated include:

```text
_includes/publication-single.html
```

`_pages/publications.html` should call this include for items in `site.publications` instead of reusing `_includes/archive-single.html`.

Each publication item should render as:

```text
[KDD 26] Paper Title
Author A, Author B, Author C
- ACM Conference on Knowledge Discovery and Data Mining (KDD 2026), CCF-A
[Paper] [Code] [Project] [BibTeX] [Google Scholar]
```

Rules:

- Show `venue_tag` as a compact badge before the title when present.
- Use the local `title`, `authors`, `venue`, `year`, and `rank` fields.
- Show optional links only when the corresponding URL exists.
- Preserve the existing category grouping from `_config.yml` such as `Conference Papers`.
- Avoid showing long `citation` text on the list page.

## Styling Design

Add publication-specific SCSS rather than changing global archive styles.

Target style:

- Compact vertical spacing.
- Small blue venue badge.
- Title as the primary clickable link.
- Authors as secondary text, with readable line wrapping.
- Venue/rank line as lightweight secondary metadata.
- Optional links as small inline text links.

The styling must work in both light and dark themes by using existing theme variables where practical.

## Scope

In scope:

- New publication-specific include.
- Update Publications page to use it.
- Add publication-specific styles.
- Keep existing publication files compatible where possible.

Out of scope:

- Fetching from Google Scholar.
- Replacing the entire site theme.
- Redesigning individual publication detail pages.
- Editing all publication records with final real metadata.

## Verification

Run:

```powershell
bundle exec jekyll build --trace
```

Then inspect `/publications/` locally to verify:

- Publications render from local `_publications/*.md` files.
- Missing optional links do not show empty labels.
- Existing category headings still appear.
- No unrelated archive page changes are required.
