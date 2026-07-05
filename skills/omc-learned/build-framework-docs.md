---
name: build-framework-docs
description: Build an offline-capable framework documentation knowledge base with full original content and layered summary
triggers:
  - "build docs for"
  - "framework documentation"
  - "框架文档"
  - "knowledge base for"
  - "离线文档"
---

# Build Framework Docs

Build an offline-capable knowledge base for any framework/library, with full original documentation content and a layered summary index. Designed so an agent can fully understand the framework even without network access.

## Quality Gate

- Requires Playwright to fetch pages from documentation sites
- Strips web scraping artifacts (navigation, zero-width spaces)
- Adds markdown formatting (code fences, pipe tables)
- Creates layered abstraction: summary index → full content detail files

## Workflow

### Phase 1: Discover Documentation Structure

1. Fetch the main documentation page
2. Extract all navigation links (sidebar, TOC)
3. Identify the full list of documentation pages to crawl
4. Save link list to `/tmp/{framework}_links.json`

```bash
cd /tmp && node -e "
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({ channel: 'chrome', headless: true });
  const page = await browser.newPage();
  await page.goto('DOC_URL', { waitUntil: 'domcontentloaded', timeout: 60000 });
  await page.waitForTimeout(8000);
  const links = await page.evaluate(() => {
    return Array.from(document.querySelectorAll('a[href]'))
      .map(a => ({ text: a.innerText.trim(), href: a.href }))
      .filter(l => l.href.includes('/versions/') || l.href.includes('/docs/'))
      .filter(l => l.text.length > 0)
      .filter((v, i, a) => a.findIndex(t => t.href === v.href) === i);
  });
  require('fs').writeFileSync('/tmp/FRAMEWORK_links.json', JSON.stringify(links, null, 2));
  console.log('Found ' + links.length + ' links');
  await browser.close();
})();
"
```

### Phase 2: Fetch All Pages (One by One)

Fetch each page individually with Playwright. **No scripts** — one page at a time.

```bash
cd /tmp && node -e "
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({ channel: 'chrome', headless: true });
  const page = await browser.newPage();
  try {
    await page.goto('PAGE_URL', { waitUntil: 'domcontentloaded', timeout: 30000 });
    await page.waitForTimeout(3000);
    const content = await page.evaluate(() => {
      const el = document.querySelector('article') || document.querySelector('main') || document.body;
      return el.innerText;
    });
    require('fs').writeFileSync('/tmp/KEY.txt', content, 'utf-8');
    console.log('KEY: ' + content.length);
  } catch(e) { console.error('FAILED: ' + e.message); }
  await browser.close();
})();
"
```

### Phase 3: Create Directory Structure

```
~/.claude/knowledge/{category}/{framework}/
├── summary_index.md       ← Layered abstraction (architecture, file index, concept glossary)
└── details/               ← Full original content files
    ├── 01-topic-a.md
    ├── 02-topic-b.md
    └── ...
```

### Phase 4: Create Summary Index

The summary index provides **layered abstraction** for quick navigation:

1. **Architecture diagram** — ASCII tree showing component relationships
2. **File index** — Table mapping each detail file to a one-line description
3. **Concept glossary** — Key terms with definitions and cross-references to detail files
4. **Reading order** — Suggested paths for different use cases

### Phase 5: Write Detail Files with Full Original Content

Each detail file must contain:
- YAML frontmatter (title, source URL, version, language)
- **Complete original documentation text** (not summarized)

```bash
# Copy raw fetched content into detail files
cat /tmp/raw_content.txt > details/01-topic.md

# Add metadata header
cat > header.tmp << 'EOF'
---
title: "Topic Name"
source: "https://..."
version: "x.y.z"
language: "zh"
---

# Topic Name

> 原始文档来源：URL

---
EOF
cat details/01-topic.md >> header.tmp
mv header.tmp details/01-topic.md
```

### Phase 6: Format Cleanup

**Critical** — raw Playwright innerText has formatting issues that must be fixed:

#### 6.1 Remove Zero-Width Spaces (U+200B)
```bash
for f in details/*.md; do
  sed -i '' 's/​//g' "$f"
done
```

#### 6.2 Strip Navigation Artifacts
```bash
for f in details/*.md; do
  sed -i '' '/^在此页面$/d' "$f"
  sed -i '' '/^复制页面$/d' "$f"
  sed -i '' '/^核心模块$/d' "$f"
  sed -i '' '/^This documentation is built and hosted on Mintlify/d' "$f"
  # Add more patterns as needed for the specific site
done
```

#### 6.3 Add Code Fences
Use a Python script to detect Python/bash code blocks by keyword patterns and wrap in fenced blocks.

#### 6.4 Convert Tables
Convert tab-delimited tables to markdown pipe syntax.

#### 6.5 Collapse Extra Blank Lines
```bash
for f in details/*.md; do
  awk 'NF{c=0} !NF{c++} c<=2' "$f" > "${f}.tmp" && mv "${f}.tmp" "$f"
done
```

### Phase 7: Update CLAUDE.md

Add to `~/.claude/CLAUDE.md`:
```markdown
## {Framework} Projects

When working on **{Framework}** projects, read the following files before writing code:

1. `~/.claude/knowledge/{category}/{framework}/summary_index.md` — Architecture overview and quick reference
2. `~/.claude/knowledge/{category}/{framework}/details/*.md` — Complete original documentation
```

### Phase 8: Review Cycle

1. Architect review (structure, completeness, gaps)
2. Critic review (formatting, missing pages, artifacts)
3. Apply fixes
4. Re-review until pass

## Success Criteria

- [ ] All documentation pages from source site are captured
- [ ] Each detail file has YAML frontmatter + full original content
- [ ] No zero-width spaces (U+200B)
- [ ] No navigation artifacts ("在此页面", "复制页面", etc.)
- [ ] Code examples wrapped in markdown fences
- [ ] Tables in markdown pipe syntax
- [ ] Summary index has architecture diagram, file index, concept glossary
- [ ] CLAUDE.md references the knowledge base
- [ ] Architect review passes
- [ ] Critic review passes

## Pitfalls

- **Playwright networkidle timeout**: Use `domcontentloaded` + manual wait (3-8s)
- **Dynamic pages**: Some sites need 10s+ wait for content to load
- **Tab-delimited tables**: Raw innerText uses tabs, not pipe syntax — must convert
- **Zero-width spaces**: Invisible in editors, break string matching — must strip
- **Navigation artifacts**: Every Mintlify site has "在此页面"/"复制页面" — must strip
- **Code blocks lose fences**: innerText strips markdown fences — must re-add
- **Missing pages**: Check sidebar navigation for pages not in main content area

## Verified By

AgentScope documentation knowledge base:
- 18 pages fetched via Playwright
- 16 detail files with full original content (228KB → 300KB after formatting)
- 189 code blocks fenced, 121 tables converted
- Architect review: PASSED
- Critic review: PASSED after formatting fixes
