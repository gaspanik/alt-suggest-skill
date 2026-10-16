# alt-suggest — Figma Image Alt-Text Audit (Figma Design Agent edition)

Register this skill in Figma's custom skill feature, select a FRAME, SECTION, COMPONENT, or COMPONENT_SET on the canvas, and run it from the chat. It finds every image in the selection, actually looks at what each one shows, and proposes a specific alt-text description — or flags it as decorative with no description needed.

> *日本語 README はこちら: [README.ja.md](README.ja.md)*

---

## What this is

Alt text usually gets decided (or skipped) at implementation time, by whoever is coding the page — and they're often guessing from a filename or a layer name, not looking at the actual image. This skill moves that judgment call earlier, into the design review, where the person looking at the file can see exactly what each image contains.

It walks the node tree directly through Figma's Plugin API (no external MCP tools, no generated-code guessing), captures every image's actual rendered content, and reports specific alt-text suggestions — never guessing from a filename or layer name like `Rectangle` or `Image 1`.

```
Select a FRAME / SECTION / COMPONENT / COMPONENT_SET on canvas
        │
        ▼
Walk tree via Plugin API, don't skip INSTANCE subtrees
        │
        ▼
Group identical images (imageHash) — review duplicates once
        │
        ▼
Capture actual appearance via screenshot(), check nearby TEXT nodes
        │
        ▼
Classify decorative vs. needs-a-description, propose specific alt text
        │
        ▼
Suggestion report (nothing written to the file)
```

`INSTANCE` subtrees are never skipped — a photo dropped inside a card/product component instance is still a rendered, real image, so it stays in scope.

---

## Scope

- **Reads only.** This skill inspects images and reports; it never writes anything to the file — no annotations, no fills, no layer names.
- **Target node types:** `FRAME`, `SECTION`, `COMPONENT`, or `COMPONENT_SET`.
- **In scope:** every visible `IMAGE` fill anywhere in the selection subtree, including generically-named nodes (name-based filtering is never used, since a name tells you nothing about what's actually in the fill).
- **Out of scope:** vector icons/illustrations with no `IMAGE` fill, WCAG conformance beyond the alt-text content itself (color contrast is a separate concern), and accessibility notes beyond ALT text (landmarks, ARIA patterns).

---

## Requirements

- **Figma (Design Agent / custom skill feature).** This skill only uses Figma's built-in Plugin API script execution tool (e.g. `evaluate_script`). No external MCP servers or API keys required — register `SKILL.md` as a custom skill and it runs.

---

## Repo structure

```
skills/
  alt-suggest/
    SKILL.md          — skill definition to register in Figma's custom skill feature
    LICENSE
```

---

## Getting started

**Once published on Figma Community**, you'll be able to add this skill directly from the [AI Skills library](https://www.figma.com/community/ai-skills) — no download needed. Until then, register it from source:

**From source:** clone this repo and register the skill file yourself.

```bash
git clone https://github.com/gaspanik/alt-suggest-skill
```

**1. Register `skills/alt-suggest/SKILL.md`** in Figma's custom skill feature.

**2. Select a FRAME, SECTION, COMPONENT, or COMPONENT_SET** on the canvas.

**3. Run it from the chat**

```
/alt-suggest
```

```
Suggest alt text for the images in this frame
```

```
このフレーム内の画像にどんなALTテキストが適切か提案して
```

Frames with many images take longer — auditing one section at a time is recommended.

---

## Example output

```
## Image Alt-Text Suggestion Report

Target: Hero Section (nodeId)

### Summary

| Classification                     | Count |
|-------------------------------------|-------|
| Needs a description                 | 3     |
| Decorative (no description needed)  | 2     |
| ⚠️ Capture failed                    | 0     |

### A few of the suggestions (first 3)

1. Hero > BackgroundPhoto — Suggested ALT: "Steam rising from a clay pot as broth
   is poured, in the kitchen of a Japanese restaurant"
2. CTA > ArrowIcon — Decorative (redundant with the adjacent "Learn more" label)
3. Footer > MapThumbnail — Suggested ALT: "A map of the area around the shop.
   A red pin marks the shop's location, a 5-minute walk from the nearest station"

If you'd like the full list, say "show me the details."
```

---

## Part of a larger set

This is a simplified extract from **KMRVID Figma Skills**, a 30-skill bundle covering AI-slop-resistant page generation, multi-layout-pattern exploration, layer cleanup, contrast/accessibility checks, tokenization, component audits, and more. The full-featured version in that bundle also writes the approved suggestions into Figma's Dev Mode → Annotations panel after confirmation, so an engineer picking up the file sees the alt text alongside the design — this lite edition stops at the report, with no write access to the file: [kmrvidskills.dev/en/figma/](https://kmrvidskills.dev/en/figma/)

---

Built by Masaaki Komori - [@cipher](https://x.com/cipher) · Skill for [Figma](https://www.figma.com/)
