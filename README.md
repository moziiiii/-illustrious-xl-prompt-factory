# Illustrious XL Prompt Factory

[中文](./README.zh.md)

A **Claude Code Skill** that generates structured NSFW prompts for **Illustrious XL v2.0** based Stable Diffusion models.

## What it does

You say: `"Nahida, corruption arc, 5 images"`

The skill:
1. Looks up the character's Danbooru tags from a 70+ character database
2. Matches the theme to an arc type (corruption, seduction, exhibition, etc.)
3. Assigns S/A/B/C erotic levels across N images following the arc shape
4. Selects tags from level-based pools (expressions, clothing, camera, poses, body fluids)
5. Checks pose↔camera compatibility
6. Shows you a human-readable story outline (no tag knowledge needed)
7. Expands into complete prompts (positive + negative + parameters) ready to paste into Forge/ComfyUI

## Features

- **S/A/B/C Erotic Level System** — Every tag category has graded pools. Say "B-level" and get appropriate tags without memorizing hundreds of Danbooru words
- **12 Scene Transition Arcs** — Corruption, seduction, exhibition, NTR, contrast, training, romance, and more — each with verified level distribution curves
- **70+ Character Danbooru Database** — Exact working tags for Genshin/Star Rail/ZZZ/Wuthering Waves/Frieren/Nier/etc.
- **Pose↔Camera Compatibility Matrix** — 25+ positions with recommended and forbidden camera angles
- **Pixiv Cover Templates** — 5 verified cover types (direct gaze, over-shoulder, M-spread, kneeling tease, mid-undress)
- **Latex Catsuit Uniform Template** — 9 anchor points for consistent outfit generation
- **Body Writing Module** — Corruption markings for S-level scenes
- **Level-Linked Negative Prompts** — Negative prompt adapts to clothing state at each level
- **Pixiv Publishing Helper** — Auto-generate Chinese/Japanese bilingual titles and tags

## Requirements

- SD WebUI **Forge** or **ComfyUI**
- **Illustrious XL v2.0** (or compatible: NoobAI, WaiNSFW, HassakuXL)
- Optional: Any style LoRA (Renbocloud, etc.)

## Installation

```bash
npx skills add <your-github-username>/illustrious-xl-prompt-factory
```

Or manually: copy the `illustrious-xl-prompt-factory/` folder to `~/.claude/skills/`

## Quick Start

**Style configuration** (say once per session):
```
"I'm using Renbocloud LoRA"
"I'm using vanilla Illustrious XL"
```

**Sequence mode** (the main way):
```
"Raiden Shogun, corruption arc, 5 images"
"Frieren, Pixiv doujinshi arc, 10 images"  
"Nahida, from pure dendro archon to broken slut, 6 images"
"Silver Wolf, seduction at the arcade, 4 images"
```

**Single image mode**:
```
"Acheron, B-level expression, standing pose"
"Ruan Mei, S-level, latex catsuit"
```

**Output control**:
- `"全展开"` — Output all prompts at once
- `"一张一张来"` — Output one at a time
- `"生成发布信息"` — Generate Pixiv title and tags

## Reference Files

| File | Content |
|------|---------|
| `references/danbooru-character-tags.md` | 70+ characters with verified Danbooru tags |
| `references/erotic-ranking.md` | S/A/B/C tag pools across 6 dimensions |
| `references/compatibility-matrix.md` | Pose↔camera compatibility table |
| `references/character-database.md` | Character corruption color palettes |

## Parameters (locked)

| Parameter | Value |
|-----------|-------|
| Sampler | Euler a |
| Steps | 35 |
| CFG | 4.5 |
| Hires | 4x-UltraSharp 2x, CFG 4, Denoising 0.38 |
| Resolution | 832×1216 / 1216×832 |

## License

MIT
