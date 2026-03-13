---
name: uou-islab-report
description: Generate ISLAB weekly meeting presentation slides as a single-file HTML/CSS/JS web presentation from a markdown weekly report. Use this skill whenever the user wants to create presentation slides, weekly meeting slides, seminar slides, or convert a weekly report markdown file into a presentation for the University of Ulsan ISLAB. Also trigger when the user mentions "발표자료", "프레젠테이션", "슬라이드", "weekly report to slides", or wants to update/regenerate presentation HTML from a report file.
---

# UOU-ISLAB Presentation Generator

Generate standardized ISLAB web presentations from weekly report markdown files. The output is a **single self-contained HTML file** using pure vanilla HTML, CSS, and JavaScript — no external libraries or frameworks.

## Overview

This skill converts `YYYYMMDD_weekly_report.md` into `YYYYMMDD_presentation.html`. The presentation includes:
- Title slide with presenter info, logos, and date
- Content slides derived from markdown H1 sections
- Settings popup (background, font color, transitions)
- Keyboard/touch/click navigation
- PDF export via `window.print()`
- Fragment auto-reveal animations

## Step 1: Read Input Files

1. **Weekly report**: Read the `YYYYMMDD_weekly_report.md` file the user provides
2. **Presenter info**: Read `presenter.json` in the project root
3. **Template reference**: Read `references/template.html` from this skill's directory for the exact HTML/CSS/JS structure

### Weekly Report Format

```markdown
---
date:YYYY-MM-DD
---
# Section Title (becomes a slide)
## Subsection (stays on the same slide)
- Bullet points with nesting
    - Sub-items indented with 4 spaces
        - Third-level items
- Items with (draft) suffix get a draft tag badge

# Another Section
- More content...

image: relative/path/to/image.png
```

### Presenter JSON Format

```json
{
    "affiliation": "University of Ulsan",
    "name": "Presenter Name",
    "email": "email@example.com",
    "date": "YYYY-MM-DD"
}
```

## Step 2: Parse the Markdown

### Date Extraction
- Read the `date` field from YAML frontmatter (e.g., `2026-03-13`)
- Convert to `YYYYMMDD` for filename: `20260313_presentation.html`
- Convert to `MM. DD. YYYY` for display on the title slide: `03. 13. 2026`

### Slide Mapping Rules

These rules are critical — they were refined through multiple iterations of user feedback:

1. **Each `# H1` heading creates a new slide concept.** All content under the same H1 belongs to that slide section.

2. **Content under the same H1 stays on one slide** unless it would overflow the viewport. If it overflows, split into multiple slides but keep the same section label.

3. **`## H2` headings are subsections within the same slide**, not separate slides.

4. **The `# Activities` section** is special:
   - `## Last Week` and `## This Week` are always on the **same slide**, separated by a gold divider line
   - Never split Last Week and This Week into separate slides

5. **Images** (`image: path/to/file.png` lines):
   - An image belongs to the H1 section it appears under
   - If adding the image to the text slide would cause overflow, create a **separate image slide** with the same section label
   - Image slides use the `.slide-image` class (max-width: 90%, max-height: 75vh)

6. **Draft tags**: Items ending with `(draft)` get a `<span class="tag" style="font-size:0.75em;">draft</span>` badge

## Step 3: Generate the HTML

Use the template from `references/template.html` as the exact structural reference. The generated file must be a **single self-contained HTML file** with all CSS and JS inline.

### Slide Structure

#### Slide 0: Title (always first)
```html
<section class="slide active" id="slide-0">
  <div class="slide-content title-slide">
    <h1 class="fragment">Regular Meeting</h1>
    <div class="fragment presenter-info">
      <p class="presenter-name">{name from presenter.json}</p>
      <p class="presenter-affiliation">{affiliation}, ISLAB</p>
      <p class="presenter-email">{email}</p>
      <p class="presenter-date">{MM. DD. YYYY}</p>
    </div>
  </div>
  <div class="title-logos">
    <img src="log/UOU.png" alt="University Logo" class="title-logo">
    <img src="log/ISLAB.png" alt="ISLAB Logo" class="title-logo">
  </div>
</section>
```

- Title is always "Regular Meeting"
- Logos are in the `log/` directory relative to the HTML output location
- The first slide always has the `active` class

#### Content Slides (from H1 sections)

Each H1 section becomes one or more slides:

```html
<section class="slide" id="slide-{N}">
  <div class="slide-content align-left">
    <p class="fragment section-label">{H1 Title}</p>
    <!-- content here -->
  </div>
</section>
```

#### Activities Slide Pattern
```html
<p class="fragment section-label">Activities</p>
<h3 class="fragment week-heading">Last Week</h3>
<ul>
  <li class="fragment">Item
    <ul>
      <li class="fragment">Sub-item</li>
    </ul>
  </li>
</ul>
<div class="fragment week-divider"></div>
<h3 class="fragment week-heading">This Week</h3>
<ul>
  <li class="fragment">Item</li>
</ul>
```

#### Image Slide Pattern
```html
<section class="slide" id="slide-{N}">
  <div class="slide-content">
    <p class="fragment section-label">{Same H1 Section Title}</p>
    <img class="fragment slide-image" src="{image path}" alt="Data Image">
  </div>
</section>
```

Image paths from the markdown should be converted to relative paths from the HTML output location. The `data/` folder structure is `data/YYYY-MM-DD/filename.ext`.

#### Last Slide: Thank You (always last)
```html
<section class="slide" id="slide-{last}">
  <div class="slide-content">
    <h1 class="fragment" style="font-size: clamp(1.8rem, 5vw, 3.5rem);">Thank You!</h1>
    <p class="fragment thankyou-sub">Q &amp; A</p>
  </div>
</section>
```

### Fragment Classes

Every visible content element gets the `fragment` class for auto-stagger reveal animation. This includes:
- Section labels, headings, list items, images, dividers
- Nested list items each get their own `fragment` class

### Nested List Rendering

Convert markdown nesting to HTML nested `<ul>` lists:
```
- Level 1          → <li class="fragment">
    - Level 2      → <ul><li class="fragment">
        - Level 3  → <ul><li class="fragment">
```

List items use `›` (rsaquo) as bullet via CSS `::before` pseudo-element (already in the template CSS). Third-level items get dimmed color automatically via `ul ul ul li` CSS rule.

### Slide Counter

Update the slide counter text to match total slides:
```html
<div class="slide-counter" id="slideCounter">1 / {total}</div>
```

## Step 4: Include All CSS and JS

Copy the **complete CSS and JS** from the template reference. The key components are:

### CSS Includes
- Design tokens (`:root` custom properties)
- Reset & base styles
- Deck container (fullscreen fixed positioning)
- Slide stacking (absolute positioning)
- Transition animations (fade, slide, zoom)
- Typography with `clamp()` responsive sizing
- Week section styles (heading, divider)
- Fragment animation styles
- Progress bar & slide counter
- Settings button & popup styles
- White theme override (`.theme-white`)
- Print/PDF styles (`@media print`)
- Title slide styles (presenter info, logos)
- Slide image styles

### JS Includes
- Navigation state management
- Fragment auto-reveal with 120ms stagger
- Keyboard navigation (arrows, space, page up/down, home, end)
- Touch/swipe navigation (50px threshold)
- Click navigation (left/right halves)
- Hash sync via `replaceState`
- PDF export (reveal all fragments before print)
- Settings system (background, font color with presets + HEX, transitions)
- Fullscreen toggle (F key)
- Speaker notes toggle (N key)

## Step 5: Write Output

Save the generated HTML as `YYYYMMDD_presentation.html` in the same directory as the weekly report file.

## Important Reminders

- The HTML file must be completely self-contained (inline CSS + inline JS)
- Only external dependencies: Google Fonts CDN links and local image/logo files
- Logo paths: `log/UOU.png` and `log/ISLAB.png` (relative to HTML file)
- Image paths: use relative paths from the HTML file location to the `data/` folder
- All text content uses the `fragment` class for reveal animations
- The `active` class goes only on the first slide (`slide-0`)
- Slide IDs are sequential: `slide-0`, `slide-1`, `slide-2`, etc.
- Always escape HTML entities: `&` → `&amp;`, `<` → `&lt;`, etc.
- When updating RESEARCH.md after changes, keep it synchronized with the actual HTML output
