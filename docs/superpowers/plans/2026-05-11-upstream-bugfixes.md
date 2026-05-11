# Upstream Bugfixes & Enhancements Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Apply 8 validated fixes/enhancements from upstream issues and PRs to the local fork.

**Architecture:** All changes are edits to skill documentation files (`SKILL.md`, `html-template.md`, `viewport-base.css`) and `README.md`. No new files needed. Changes are independent and can be committed individually. The files under `plugins/frontend-slides/skills/frontend-slides/` are the canonical installed copies (symlinks were replaced with real files); always edit those paths.

**Tech Stack:** Markdown, CSS, JavaScript (inline in `html-template.md` code blocks)

---

## File Map

| File | Changes |
|------|---------|
| `plugins/frontend-slides/skills/frontend-slides/SKILL.md` | Task 1 (frontmatter), Task 8 (diagram detection) |
| `plugins/frontend-slides/skills/frontend-slides/viewport-base.css` | Task 2 (overflow), Task 3 (4K breakpoint) |
| `plugins/frontend-slides/skills/frontend-slides/html-template.md` | Task 4 (async fonts), Task 5 (bullet CSS), Task 6 (inline edit), Task 7 (fullscreen), Task 9 (diagram CSS) |
| `README.md` | Task 0 (typo) |

---

## Task 0: Fix README Typo — "Soft Pastel" → "Split Pastel"

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Grep to confirm the typo location**

  Run: `grep -n "Soft Pastel" /Users/jake.li/Documents/github/frontend-slides/README.md`
  Expected: at least one line with "Soft Pastel"

- [ ] **Step 2: Replace the typo**

  Replace every occurrence of "Soft Pastel" with "Split Pastel" in `README.md`.

- [ ] **Step 3: Verify**

  Run: `grep -n "Soft Pastel\|Split Pastel" /Users/jake.li/Documents/github/frontend-slides/README.md`
  Expected: zero lines with "Soft Pastel", one or more with "Split Pastel"

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add README.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "fix: correct theme name Soft Pastel → Split Pastel (issue #17)"
  ```

---

## Task 1: Fix SKILL.md Frontmatter — Add `allowed-tools`

**Context (PR #37):** Newer Claude Code versions require `AskUserQuestion` to be explicitly listed in `allowed-tools` or it falls back to plain text questions.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/SKILL.md` (lines 1–4)

- [ ] **Step 1: View current frontmatter**

  Read lines 1–5 of `plugins/frontend-slides/skills/frontend-slides/SKILL.md`.
  Expected current content:
  ```yaml
  ---
  name: frontend-slides
  description: Create stunning, animation-rich HTML presentations...
  ---
  ```

- [ ] **Step 2: Add `allowed-tools` field**

  Replace the frontmatter block with:
  ```yaml
  ---
  name: frontend-slides
  description: Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to build a presentation, convert a PPT/PPTX to web, or create slides for a talk/pitch. Helps non-designers discover their aesthetic through visual exploration rather than abstract choices.
  allowed-tools: Read, Write, Edit, Bash, AskUserQuestion
  ---
  ```

- [ ] **Step 3: Verify**

  Run: `head -6 /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/SKILL.md`
  Expected: `allowed-tools: Read, Write, Edit, Bash, AskUserQuestion` appears on line 4.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/SKILL.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "fix: add allowed-tools frontmatter so AskUserQuestion works in newer Claude Code (PR #37)"
  ```

---

## Task 2: Fix `viewport-base.css` — `overflow-x` → `overflow`

**Context (Issue #34):** `overflow-x: hidden` on `html, body` still allows a vertical scrollbar to appear on some browsers during presentations, looking unprofessional when screen-sharing.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/viewport-base.css` (line 9)

- [ ] **Step 1: Confirm current line**

  Run: `grep -n "overflow" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/viewport-base.css`
  Expected: `overflow-x: hidden;` on the `html, body` block (around line 9).

- [ ] **Step 2: Apply fix**

  In `viewport-base.css`, find:
  ```css
  html, body {
      height: 100%;
      overflow-x: hidden;
  }
  ```
  Replace with:
  ```css
  html, body {
      height: 100%;
      overflow: hidden;
  }
  ```

- [ ] **Step 3: Verify**

  Run: `grep -n "overflow" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/viewport-base.css | head -5`
  Expected: `overflow: hidden;` (not `overflow-x`) in the `html, body` block.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/viewport-base.css
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "fix: hide vertical scrollbar with overflow:hidden on html,body (issue #34)"
  ```

---

## Task 3: Add 4K / Projector Breakpoint to `viewport-base.css`

**Context (PR #56):** Screens ≥1600px (projectors, 4K monitors) hit the `clamp()` max caps too early. Add a breakpoint that raises the caps so text scales up further.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/viewport-base.css` (append after last `@media` block)

- [ ] **Step 1: Confirm end of file**

  Run: `tail -15 /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/viewport-base.css`
  Expected: ends with the `prefers-reduced-motion` block.

- [ ] **Step 2: Append 4K breakpoint**

  Append to the end of `viewport-base.css`:
  ```css

  /* Large viewports (≥ 1600px — projectors, 4K displays) */
  @media (min-width: 1600px) {
      :root {
          --title-size: clamp(1.5rem, 5vw, 6rem);
          --h2-size: clamp(1.25rem, 3.5vw, 3.5rem);
          --h3-size: clamp(1rem, 2.5vw, 2.5rem);
          --body-size: clamp(0.75rem, 1.5vw, 1.5rem);
          --small-size: clamp(0.65rem, 1vw, 1.125rem);
      }
  }
  ```

- [ ] **Step 3: Verify**

  Run: `grep -A 8 "1600px" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/viewport-base.css`
  Expected: the new block appears with correct property values.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/viewport-base.css
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "feat: add projector/4K breakpoint (>=1600px) for larger type scaling (PR #56)"
  ```

---

## Task 4: Async Font Loading in `html-template.md`

**Context (PR #58):** Synchronous font `<link>` tags block page rendering. The `media="print" onload` trick loads fonts asynchronously without a custom JS loader.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/html-template.md` (Base HTML Structure section, font link lines)

- [ ] **Step 1: Find current font link pattern**

  Run: `grep -n "fontshare\|googleapis\|font" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -10`
  Expected: a line like `<link rel="stylesheet" href="https://api.fontshare.com/v2/css?f[]=..." />`

- [ ] **Step 2: Replace with async pattern**

  Find the comment and font `<link>` block in the Base HTML Structure section:
  ```html
      <!-- Fonts: use Fontshare or Google Fonts — never system fonts -->
      <link rel="stylesheet" href="https://api.fontshare.com/v2/css?f[]=..." />
  ```

  Replace with:
  ```html
      <!-- Fonts: async non-blocking load (media="print" onload trick) -->
      <link rel="preconnect" href="https://api.fontshare.com" />
      <link rel="preconnect" href="https://fonts.googleapis.com" crossorigin />
      <link rel="stylesheet"
            href="https://api.fontshare.com/v2/css?f[]=..."
            media="print"
            onload="this.media='all'" />
      <noscript>
        <link rel="stylesheet" href="https://api.fontshare.com/v2/css?f[]=..." />
      </noscript>
  ```

- [ ] **Step 3: Verify**

  Run: `grep -n "media=\"print\"\|preconnect\|noscript" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -10`
  Expected: lines with `media="print"`, `preconnect`, and `noscript` all present.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/html-template.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "feat: async non-blocking font loading with media=print onload trick (PR #58)"
  ```

---

## Task 5: Fix Bullet Hanging-Indent CSS in `html-template.md`

**Context (Issue #62):** When `<li>` uses `display: grid` for hanging-indent markers, CSS Grid wraps each inline `<code>` element into its own anonymous box and row. Result: every text node and `<code>` chip in `<li>foo <code>bar</code> baz</li>` renders as a separate line.

**Fix:** Switch from grid to absolute-positioned `::before` marker, which lets inline content flow naturally.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/html-template.md` (bullets section)

- [ ] **Step 1: Locate the bullet CSS pattern**

  Run: `grep -n "bullets\|display: grid\|grid-template-columns" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -15`

  Note: this pattern may not appear explicitly in the template (it manifests in generated output). Check the Bullets / List Patterns section of `html-template.md`.

- [ ] **Step 2: Read the Bullets section**

  Run: `grep -n "bullet\|Bullet\|\.bullets" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md`

- [ ] **Step 3: Add or update the bullets CSS guidance**

  Find the section that describes bullet/list CSS in `html-template.md`. Add the following canonical pattern (create the section if it doesn't exist, or update the existing pattern):

  After the "Required JavaScript Features" section, find or create a "## Bullet / List Patterns" section and add:

  ```markdown
  ## Bullet / List Patterns

  **CRITICAL: Never use `display: grid` on `<li>` elements that contain inline `<code>` chips.**

  CSS Grid wraps every text node and inline element into an anonymous block box. A single `<li>foo <code>bar</code> baz</li>` produces three separate rows — content overflows and each fragment renders on its own line.

  Use this hanging-indent pattern instead (absolute `::before` marker):

  ```css
  .bullets {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: clamp(0.5rem, 1vh, 1rem);
  }

  .bullets li {
    position: relative;
    padding-left: clamp(1.4rem, 2.5vw, 2rem);
    font-size: var(--body-size);
    line-height: 1.5;
  }

  .bullets li::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0.65em;
    width: clamp(0.75rem, 1.5vw, 1.2rem);
    height: 2px;
    background: var(--accent);
  }
  ```

  This renders `<li>text <code>chip</code> more text</li>` correctly as a single inline-flowing line regardless of how many `<code>` elements appear.
  ```

- [ ] **Step 4: Verify**

  Run: `grep -n "display: grid\|hanging-indent\|Never use.*grid.*li\|position: absolute" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -10`
  Expected: the "Never use `display: grid`" warning and `position: absolute` on `::before` appear.

- [ ] **Step 5: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/html-template.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "fix: replace display:grid bullet pattern with absolute ::before to fix inline code rendering (issue #62)"
  ```

---

## Task 6: Fix Inline Edit — Enter Key & Paste Font Corruption

**Context (Issue #49):** In inline edit mode:
1. Chrome inserts a `<div>` on Enter inside `contenteditable`, breaking font inheritance in `<h1>`/`<p>` elements.
2. Pasting from clipboard brings foreign inline styles (`font-family`, `color`, `background-color`) that override theme tokens.

Both bugs only appear after Ctrl+S export → reload (not during editing), making them easy to miss.

**Fix:** Three additions to the inline edit JS:
1. `defaultParagraphSeparator` → `'br'`
2. Intercept `keydown` Enter → `insertLineBreak`
3. Intercept `paste` → strip to plain text

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/html-template.md` (Inline Editing Implementation section)

- [ ] **Step 1: Find the enterEditMode / editor toggle section**

  Run: `grep -n "toggleEditMode\|enterEditMode\|isActive\|contenteditable" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -15`

- [ ] **Step 2: Locate where contenteditable is set on elements**

  Read the lines around `contenteditable` in `html-template.md` to find where edit mode is entered.

- [ ] **Step 3: Add Enter/paste fix to the Inline Editing Implementation section**

  In the "Inline Editing Implementation (Opt-In Only)" section of `html-template.md`, after the existing `exportFile()` code block, add a new subsection:

  ````markdown
  ### Inline Edit — Enter Key & Paste Fix

  **Why this is needed:** Chrome inserts `<div>` on Enter inside `contenteditable` (Firefox uses `<br>`, Safari varies). A `<div>` inside `<h1>` or `<p>` is invalid HTML — browsers "fix" it when serializing `outerHTML` for Ctrl+S export, producing a structurally different file where the new line renders in the wrong font. Pasting from external sources brings inline `font-family`/`color`/`background-color` that override CSS theme tokens.

  Add these three items when entering edit mode (call from inside `toggleEditMode()` or `enterEditMode()`):

  ```javascript
  enableEditMode(editableElements) {
    // 1. Set paragraph separator to <br> (prevents Chrome <div>-on-Enter bug)
    document.execCommand('defaultParagraphSeparator', false, 'br');

    editableElements.forEach(el => {
      el.setAttribute('contenteditable', 'true');

      // 2. Intercept Enter: insert <br> instead of <div>
      el.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) {
          e.preventDefault();
          // execCommand is deprecated but has no clean replacement as of 2026
          document.execCommand('insertLineBreak');
        }
      });

      // 3. Intercept paste: strip to plain text only
      el.addEventListener('paste', (e) => {
        e.preventDefault();
        const text = e.clipboardData.getData('text/plain');
        document.execCommand('insertText', false, text);
      });
    });
  }
  ```

  **localStorage version bump:** If previously-saved presentations in localStorage contain `<div>` garbage from the old behavior, bump the storage key version so they don't load corrupted content:
  ```javascript
  const STORAGE_KEY = 'presentation-edits-v2'; // was v1
  ```
  ````

- [ ] **Step 4: Verify**

  Run: `grep -n "insertLineBreak\|defaultParagraphSeparator\|text/plain\|STORAGE_KEY" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md`
  Expected: all four strings appear.

- [ ] **Step 5: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/html-template.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "fix: prevent font corruption on Enter/paste in inline edit mode (issue #49)"
  ```

---

## Task 7: Add Fullscreen Button to `html-template.md`

**Context (Issue #34):** No fullscreen support. Presenters screen-sharing want a single key/button to hide browser chrome. `F` key + a fixed corner button covers both keyboard and pointer users.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/html-template.md` (Required JavaScript Features section)

- [ ] **Step 1: Locate the SlidePresentation / JS features section**

  Run: `grep -n "SlidePresentation\|Required JavaScript" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md | head -5`

- [ ] **Step 2: Add fullscreen to the Required JavaScript Features list**

  Find the list item:
  ```markdown
  3. **Optional Enhancements** (match to chosen style):
  ```

  Before it, add as item 3 (renumber Optional Enhancements to 4 and Inline Editing to 5):
  ```markdown
  3. **FullscreenController** — Standard in every presentation:
     - Fixed `⛶` button, bottom-right corner
     - `F` key shortcut (skip when in `contenteditable`)
     - Button icon toggles to `✕` when fullscreen is active
     - `document.fullscreenElement` check for toggle
  ```

- [ ] **Step 3: Add FullscreenController HTML/CSS/JS block**

  After the Required JavaScript Features list, add a new subsection:

  ````markdown
  ## Fullscreen Controller (Required in Every Presentation)

  Add this HTML, CSS, and JS to every generated presentation — it is not optional.

  HTML (inside `<body>`, before slides):
  ```html
  <button class="fullscreen-btn" id="fullscreenBtn" title="Fullscreen (F)">⛶</button>
  ```

  CSS:
  ```css
  .fullscreen-btn {
    position: fixed;
    bottom: 1rem;
    right: 1rem;
    z-index: 1000;
    background: rgba(255, 255, 255, 0.06);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 8px;
    color: rgba(255, 255, 255, 0.6);
    width: 36px;
    height: 36px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
    transition: background 0.2s, color 0.2s;
  }
  .fullscreen-btn:hover {
    background: rgba(255, 255, 255, 0.12);
    color: #fff;
  }
  ```

  JS (instantiate alongside `new SlidePresentation()`):
  ```javascript
  class FullscreenController {
    constructor() {
      this.btn = document.getElementById('fullscreenBtn');
      this.btn.addEventListener('click', () => this.toggle());
      document.addEventListener('keydown', (e) => {
        if ((e.key === 'f' || e.key === 'F') && !e.target.getAttribute('contenteditable')) {
          this.toggle();
        }
      });
      document.addEventListener('fullscreenchange', () => this.updateIcon());
    }

    toggle() {
      if (!document.fullscreenElement) {
        document.documentElement.requestFullscreen().catch(() => {});
      } else {
        document.exitFullscreen();
      }
    }

    updateIcon() {
      this.btn.textContent = document.fullscreenElement ? '✕' : '⛶';
    }
  }

  new SlidePresentation();
  new FullscreenController();
  ```
  ````

- [ ] **Step 4: Verify**

  Run: `grep -n "FullscreenController\|fullscreenBtn\|requestFullscreen\|fullscreenchange" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md`
  Expected: all four strings appear.

- [ ] **Step 5: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/html-template.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "feat: add fullscreen button and F-key shortcut to every presentation (issue #34)"
  ```

---

## Task 8: Add Diagram Layout Detection to `SKILL.md`

**Context (PR #61):** When users provide images with relationship diagrams (org charts, pipelines, cycle diagrams), the skill currently has no pattern for rendering them faithfully as HTML — it falls back to generic slides that lose spatial semantics.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/SKILL.md` (Phase 1 section)

- [ ] **Step 1: Find Phase 1 image evaluation section**

  Run: `grep -n "Image Evaluation\|Step 1.2\|Scan.*image\|USABLE" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/SKILL.md | head -10`

- [ ] **Step 2: Add diagram detection step after Step 1.2**

  Find the end of the "Step 1.2: Image Evaluation" section (just before "---" or "Phase 2"). After the last line of Step 1.2, add:

  ````markdown
  ### Step 1.3: Diagram Detection

  After evaluating each image, identify any **relationship diagrams** — images with spatial structure that encodes meaning (arrows, nodes, flows, hierarchies). These require dedicated CSS layouts in Phase 3 to preserve that structure.

  Detect and label each diagram image as one of:

  | Type | Visual cues | HTML layout |
  |------|-------------|-------------|
  | `cycle-diagram` | Circular arrows, flywheel, recurring loop | Circle of nodes with arc connectors |
  | `pipeline-diagram` | Left→right flow, input/process/output boxes | Horizontal flex with arrow separators |
  | `feedback-diagram` | Main flow + return arrow, system loop | Forward path + curved return path |
  | `hierarchy-diagram` | Org chart, tree, parent→children | CSS grid tree with connecting lines |
  | `hub-diagram` | Central node + spokes to outer nodes | CSS radial positioning |

  **If any diagram is detected:** note the type for Phase 3. The corresponding CSS layout patterns are documented in [html-template.md](html-template.md) under "Diagram Layout Patterns".

  **If no diagrams detected:** skip to Phase 2 as normal.
  ````

- [ ] **Step 3: Verify**

  Run: `grep -n "cycle-diagram\|pipeline-diagram\|hub-diagram\|Diagram Detection" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/SKILL.md`
  Expected: all four strings appear.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/SKILL.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "feat: add diagram layout detection in Phase 1 (PR #61)"
  ```

---

## Task 9: Add Diagram CSS Layout Patterns to `html-template.md`

**Context (PR #61 continued):** SKILL.md now references these patterns. Add the CSS implementation to `html-template.md`.

**Files:**
- Modify: `plugins/frontend-slides/skills/frontend-slides/html-template.md` (append new section)

- [ ] **Step 1: Confirm end of file**

  Run: `tail -5 /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md`

- [ ] **Step 2: Append diagram layout patterns section**

  Append to `html-template.md`:

  ````markdown

  ## Diagram Layout Patterns

  Use these zero-dependency CSS layouts when a slide needs to render a relationship diagram. All patterns use only CSS (no JS, no canvas, no SVG) and respect `clamp()` sizing and `overflow: hidden` constraints.

  ### cycle-diagram — Circular / Flywheel Processes

  ```html
  <div class="cycle-diagram">
    <div class="cycle-node">Step 1</div>
    <div class="cycle-node">Step 2</div>
    <div class="cycle-node">Step 3</div>
    <div class="cycle-node">Step 4</div>
  </div>
  ```

  ```css
  .cycle-diagram {
    position: relative;
    width: min(40vh, 400px);
    height: min(40vh, 400px);
    margin: auto;
  }
  .cycle-node {
    position: absolute;
    width: clamp(4rem, 8vw, 7rem);
    height: clamp(4rem, 8vw, 7rem);
    border-radius: 50%;
    background: var(--accent);
    color: var(--bg-primary);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: var(--small-size);
    font-weight: 700;
    text-align: center;
  }
  /* Position nodes at 12, 3, 6, 9 o'clock */
  .cycle-node:nth-child(1) { top: 0;   left: 50%; transform: translateX(-50%); }
  .cycle-node:nth-child(2) { top: 50%; right: 0;  transform: translateY(-50%); }
  .cycle-node:nth-child(3) { bottom: 0; left: 50%; transform: translateX(-50%); }
  .cycle-node:nth-child(4) { top: 50%; left: 0;   transform: translateY(-50%); }
  ```

  ### pipeline-diagram — Input → Process → Output

  ```html
  <div class="pipeline-diagram">
    <div class="pipeline-node">Input</div>
    <div class="pipeline-arrow">→</div>
    <div class="pipeline-node">Process</div>
    <div class="pipeline-arrow">→</div>
    <div class="pipeline-node">Output</div>
  </div>
  ```

  ```css
  .pipeline-diagram {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: clamp(0.5rem, 1.5vw, 1rem);
    flex-wrap: nowrap;
    max-width: 90%;
    margin: auto;
  }
  .pipeline-node {
    padding: clamp(0.5rem, 1.5vw, 1rem) clamp(1rem, 2.5vw, 2rem);
    background: var(--accent);
    color: var(--bg-primary);
    border-radius: 8px;
    font-size: var(--body-size);
    font-weight: 700;
    white-space: nowrap;
  }
  .pipeline-arrow {
    font-size: var(--h3-size);
    color: var(--text-secondary);
    flex-shrink: 0;
  }
  ```

  ### hierarchy-diagram — Org Chart / Tree

  ```html
  <div class="hierarchy-diagram">
    <div class="h-root">CEO</div>
    <div class="h-children">
      <div class="h-node">VP Eng</div>
      <div class="h-node">VP Design</div>
      <div class="h-node">VP Sales</div>
    </div>
  </div>
  ```

  ```css
  .hierarchy-diagram {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: clamp(1rem, 2vh, 2rem);
  }
  .h-root {
    padding: clamp(0.5rem, 1.5vw, 1rem) clamp(1.5rem, 3vw, 3rem);
    background: var(--accent);
    color: var(--bg-primary);
    border-radius: 8px;
    font-weight: 700;
    font-size: var(--body-size);
  }
  .h-children {
    display: flex;
    gap: clamp(0.75rem, 2vw, 2rem);
    position: relative;
  }
  .h-children::before {
    content: "";
    position: absolute;
    top: calc(-1 * clamp(0.5rem, 1vh, 1rem));
    left: 50%;
    transform: translateX(-50%);
    width: 1px;
    height: clamp(0.5rem, 1vh, 1rem);
    background: var(--text-secondary);
  }
  .h-node {
    padding: clamp(0.4rem, 1vw, 0.75rem) clamp(1rem, 2vw, 1.5rem);
    border: 1px solid var(--text-secondary);
    border-radius: 8px;
    font-size: var(--small-size);
    color: var(--text-primary);
  }
  ```

  ### hub-diagram — Hub and Spoke

  ```html
  <div class="hub-diagram">
    <div class="hub-center">Core</div>
    <div class="hub-spoke">API</div>
    <div class="hub-spoke">DB</div>
    <div class="hub-spoke">Cache</div>
    <div class="hub-spoke">Auth</div>
  </div>
  ```

  ```css
  .hub-diagram {
    position: relative;
    width: min(38vh, 380px);
    height: min(38vh, 380px);
    margin: auto;
  }
  .hub-center {
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    width: clamp(4rem, 8vw, 7rem);
    height: clamp(4rem, 8vw, 7rem);
    border-radius: 50%;
    background: var(--accent);
    color: var(--bg-primary);
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: var(--body-size);
    z-index: 1;
  }
  /* Position spokes at equal angles using CSS custom properties */
  .hub-spoke {
    position: absolute;
    width: clamp(3rem, 6vw, 5rem);
    height: clamp(3rem, 6vw, 5rem);
    border-radius: 50%;
    border: 1px solid var(--accent);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: var(--small-size);
    top: 50%; left: 50%;
  }
  .hub-spoke:nth-child(2) { transform: translate(-50%, -50%) translate(0, -140%); }
  .hub-spoke:nth-child(3) { transform: translate(-50%, -50%) translate(140%, 0); }
  .hub-spoke:nth-child(4) { transform: translate(-50%, -50%) translate(0, 140%); }
  .hub-spoke:nth-child(5) { transform: translate(-50%, -50%) translate(-140%, 0); }
  ```
  ````

- [ ] **Step 3: Verify**

  Run: `grep -n "cycle-diagram\|pipeline-diagram\|hierarchy-diagram\|hub-diagram" /Users/jake.li/Documents/github/frontend-slides/plugins/frontend-slides/skills/frontend-slides/html-template.md`
  Expected: all four diagram type names appear.

- [ ] **Step 4: Commit**

  ```bash
  git -C /Users/jake.li/Documents/github/frontend-slides add plugins/frontend-slides/skills/frontend-slides/html-template.md
  git -C /Users/jake.li/Documents/github/frontend-slides commit -m "feat: add diagram CSS layout patterns (cycle/pipeline/hierarchy/hub) (PR #61)"
  ```

---

## Task 10: Reinstall Plugin & Verify Skill Loads

After all commits, reinstall the plugin from the local fork so the updated files are reflected in the cache.

- [ ] **Step 1: Reinstall plugin**

  ```bash
  claude plugins uninstall frontend-slides@frontend-slides && claude plugins install frontend-slides@frontend-slides
  ```
  Expected: `Successfully installed plugin: frontend-slides@frontend-slides (scope: user)`

- [ ] **Step 2: Confirm skill files in cache**

  ```bash
  find ~/.claude/plugins/cache/frontend-slides/frontend-slides/ -name "*.md" -o -name "*.css" | grep -v ".in_use"
  ```
  Expected: SKILL.md, html-template.md, STYLE_PRESETS.md, animation-patterns.md, viewport-base.css all present.

- [ ] **Step 3: Run `/reload-plugins` in Claude Code**

  Reload plugins in the current session. The `frontend-slides:frontend-slides` skill should appear in the skill list.
