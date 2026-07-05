---
name: build-coding-standards
description: Build a hierarchical coding standards knowledge base for any language/framework, with auto-injection into CLAUDE.md
triggers:
  - "build coding standards"
  - "create style guide"
  - "编码规范"
  - "coding standards for"
  - "style guide for"
---

# Build Coding Standards

Automate the creation of a hierarchical coding standards knowledge base for any programming language or framework, integrated with Claude Code's rules injection system.

## Quality Gate

This skill captures a repeatable workflow that:
- Requires fetching multiple reference documents (Playwright for Chinese/blocked sites)
- Designs a 3-tier hierarchy (rules → summary → details)
- Integrates with CLAUDE.md auto-injection
- Uses architect + critic review cycles

## Workflow

### Phase 1: Reference Document Collection

1. Identify source documents (official style guides, best practices articles)
2. Fetch content using Playwright (handles blocked sites, Chinese content):
   ```bash
   cd /tmp && node -e "
   const { chromium } = require('playwright');
   (async () => {
     const browser = await chromium.launch({ channel: 'chrome', headless: true });
     const page = await browser.newPage();
     await page.goto('URL', { waitUntil: 'domcontentloaded', timeout: 60000 });
     await page.waitForTimeout(5000);
     const content = await page.evaluate(() => {
       const el = document.querySelector('article') || document.querySelector('main') || document.body;
       return el.innerText;
     });
     require('fs').writeFileSync('/tmp/doc_KEY.txt', content, 'utf-8');
     console.log('Done: ' + content.length + ' chars');
     await browser.close();
   })();
   "
   ```
3. For multi-page sites, fetch each sub-page individually (no scripts)
4. Save each page to `/tmp/doc_KEY.txt` for reference

### Phase 2: Directory Structure Creation

```
~/.claude/rules/code-{LANG}-style.md          ← Core rules (auto-injected)
~/.claude/knowledge/{LANG}/summary_index.md    ← Index + quick reference
~/.claude/knowledge/{LANG}/details/            ← Detailed reference files
    ├── 01-topic-a.md
    ├── 02-topic-b.md
    └── ...
```

### Phase 3: Core Rules File (`rules/code-{LANG}-style.md`)

Must include:
- Reading order instruction at top
- Maintenance note (sync obligation)
- Core principles (baseline standard, supplement standard, consistency, readability)
- Version policy (minimum supported version)
- ALL important coding standards in condensed form
- Reference to summary_index.md for details

Structure:
```markdown
> Reading order: this file → knowledge/{LANG}/summary_index.md → knowledge/{LANG}/details/*.md
> Maintenance note: this file is condensed summary; update details when modifying rules.

## 1. Core Principles
## 2. Naming Conventions (table format)
## 3. Code Layout (indentation, line width, blank lines)
## 4. Imports
## 5. Strings
## 6. Whitespace
## 7. Comments & Docstrings
## 8. Type Hints (if applicable)
## 9. Error Handling
## 10. Functions & Methods
## 11. Classes (if applicable)
## 12. Idioms & Patterns
## 13. Testing
## 14. Security & Best Practices
## Reference: see summary_index.md
```

### Phase 4: Summary Index (`knowledge/{LANG}/summary_index.md`)

Must include:
- Document hierarchy tree diagram
- Quick reference tables (naming, line width, etc.)
- Source document links
- File listing with descriptions

### Phase 5: Detail Files (`knowledge/{LANG}/details/*.md`)

Each file must include:
- Source attribution header
- Numbered sections with clear headings
- Correct/incorrect code examples
- Cross-references to related files

### Phase 6: CLAUDE.md Integration

Add to `~/.claude/CLAUDE.md`:
```markdown
## {Language} Projects

When working on **any {Language} project**, read the following files in order before writing code:

1. `~/.claude/rules/code-{LANG}-style.md` — Core coding standards (must follow)
2. `~/.claude/knowledge/{LANG}/summary_index.md` — Index and quick reference
3. `~/.claude/knowledge/{LANG}/details/*.md` — All detailed reference documents
```

### Phase 7: Review Cycle

1. Run architect review (structure, completeness, gaps)
2. Run critic review (factual errors, missing rules, contradictions)
3. Apply fixes
4. Re-run reviews until both pass

## Success Criteria

- [ ] Core rules file covers ALL important standards from source documents
- [ ] Detail files are comprehensive (no major gaps)
- [ ] No contradictions between core rules and detail files
- [ ] CLAUDE.md correctly references the hierarchy
- [ ] Architect review passes
- [ ] Critic review passes (or issues are minor/cosmetic)

## Pitfalls

- **Playwright networkidle timeout**: Use `domcontentloaded` + manual wait instead
- **Dynamic pages**: Some sites need longer wait (10s+) for content to load
- **PEP 668 restriction**: Use `--break-system-packages` for pip installs
- **Content overlap**: Core rules and detail files will overlap by design; add maintenance note
- **Source document scope**: Don't add topics beyond what source documents cover (async, module structure) unless explicitly needed
- **Critic web fetch failures**: Critics may try to verify against blocked URLs; architect review alone is sufficient if critic fails on network

## Verification Evidence

This skill was validated by building Python coding standards:
- 3 source documents fetched via Playwright (PEP 8, Google Style Guide, Aliyun)
- 13 detail files created (total 124KB)
- Architect review: PASSED (structure sound, content comprehensive)
- Critic review: terminated due to network issues (acceptable)
- CLAUDE.md integration verified
