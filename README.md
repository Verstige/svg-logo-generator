# SVG Logo Generator: Concept to Full Brand Kit

> Go from a blank page to a complete brand asset kit. Scans your project for existing colours and type, proposes concepts, then exports SVG variants, favicons and social cards.

![License: MIT](https://img.shields.io/badge/License-MIT-green.svg) ![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet.svg) [![Available on Agensi](https://img.shields.io/badge/Agensi-%249-ff5a5f.svg)](https://agensi.io/skills/svg-logo-generator-concept-to-full-brand-kit)

## What it does

Generate custom SVG logos with project context scanning, adaptive brainstorming, and full brand kit export (favicons, social images, PWA manifest). For the creative framework (brand strategy, AI prompts, SVG rules, motion, brand systems), see logo-design. Use when asked to generate a logo, create an SVG logo, or build a brand asset kit for an existing project.

Generates SVG logos through project context scanning + adaptive brainstorming, then exports a complete brand asset kit.

## The failure it prevents

Working this by hand means rediscovering the same edge cases every time. They are written down here instead, with the fix for each one.

## When to use it

- generate custom SVG logos
- produce logo variants fast
- scan a project for brand context

## What's inside

```
plugins/
└── svg-logo-generator/
    ├── .claude-plugin/plugin.json
    └── skills/svg-logo-generator/
        ├── SKILL.md
        ├── references/            (1 files)
        │   └── premium-mark-design.md
```

## Install

Add this repo as a Claude Code marketplace, then install the skill:

```bash
claude plugin marketplace add Verstige/svg-logo-generator
claude plugin install svg-logo-generator@svg-logo-generator
```

Or copy the skill straight into your skills directory:

```bash
git clone https://github.com/Verstige/svg-logo-generator.git
cp -R svg-logo-generator/plugins/svg-logo-generator/skills/svg-logo-generator ~/.claude/skills/
```

## Usage

The skill activates on the work it describes. Ask for the outcome, and it
supplies the method:

```
> "generate custom SVG logos"
```

## Reference material

Loaded on demand — the skill only reads what the task needs.

| File | Covers |
| --- | --- |
| `premium-mark-design.md` | Premium Mark Design — What Separates Nike from Everything Else |

## License

MIT. See [LICENSE](LICENSE).

---

**Also available as a paid skill on Agensi** — $9. Install it with one command, no clone required:

[agensi.io/skills/svg-logo-generator-concept-to-full-brand-kit](https://agensi.io/skills/svg-logo-generator-concept-to-full-brand-kit)

Part of the [Verstige](https://github.com/Verstige) skills portfolio. More at [github.com/Verstige](https://github.com/Verstige).
