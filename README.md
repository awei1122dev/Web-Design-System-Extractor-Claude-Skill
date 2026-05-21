# Web Design Extractor

A [Claude Agent Skill](https://www.anthropic.com/news/skills) that extracts a website's visual design system from its source code and produces a clean, structured `design-system.md` — optimized to be fed to an AI (e.g. Claude or Claude Design) to generate new pages or apps in the same visual style.

It pulls out colors, fonts, type scale, spacing, radii, shadows, breakpoints, and components, separates the real brand palette from incidental values, and writes a copy-paste `:root { }` variables block plus concrete "build in this style" guidance.

## What it does

Given a site's HTML and/or CSS, the skill produces a single `design-system.md` containing:

- **Visual identity summary** — the overall vibe in one paragraph.
- **Color palette** — from CSS custom properties, hex/rgb/hsl literals, and gradients, with brand/global colors separated from actually-used surface/accent/text colors, and where each is used.
- **Typography** — family, full list of loaded weights, weight→role mapping, complete fallback stack, source (Google Fonts / self-hosted), and the actual loader code. Real text fonts are distinguished from icon fonts (FontAwesome, etc.) and incidental mono fallbacks.
- **Type scale** — every distinct size with line-height, weight, role, and desktop vs mobile values.
- **Spacing, radius, shadows, borders, transitions, breakpoints.**
- **Style direction for AI generation** — concrete do/don't guidance.
- Explicit **confidence flags** on anything low-confidence or missing.

## ⚠️ Important limitation — read before using

**This skill does not fetch live URLs.** It works only on source code that *you supply*:

- pasted HTML,
- an uploaded `.html` / `.css` file, or
- markup grabbed from the browser (View Source, DevTools → Network → the main `.css` file, or the Claude in Chrome extension).

If you give it only a bare URL with no source, it will ask you for the markup. This is by design — it never invents hex values, font names, or sizes. If something isn't in the source you provide, it says so rather than guessing.

Consequences worth knowing:

- **CSS in an external stylesheet matters.** If the page loads its tokens from a separate `.css` file (common with Bootstrap/Tailwind/Rails sites), paste *that file too* — not just the HTML — or many values will come back marked as missing.
- **Runtime-only values** (JS-injected styles, Tailwind atomic classes resolved at runtime) may not appear in static source and will be flagged as low-confidence.

## Installation

A skill is just a folder containing `SKILL.md` plus optional reference files. There is no special `.skill` file format — distribution is a plain folder (often zipped).

### Claude.ai / Claude desktop app (web & desktop)

Requires a paid plan with code execution / skills enabled.

1. Download or clone this repo and zip the `web-design-extractor` folder (keep `SKILL.md` at the root of the zip, with `references/` beside it).
2. In Claude, go to **Settings → Capabilities/Skills → Upload skill** and select the zip.
3. Toggle it on. Custom skills are private to your account and don't sync across surfaces (claude.ai vs API are separate uploads).

### Claude Code (CLI)

Copy the `web-design-extractor` folder into your skills directory:

- Personal: `~/.claude/skills/web-design-extractor/`
- Project: `.claude/skills/web-design-extractor/`

Claude Code discovers and loads it automatically when relevant.

## Usage

Once installed, just give Claude a website's source and ask for its design system. Examples that trigger it:

- "Extract the design system from this site" *(then paste the HTML/CSS)*
- "Turn this website into a DESIGN.md"
- "Get the styles/tokens from this page"
- "Make a design system file I can feed to Claude Design"
- "Analyze the fonts and colors of this page"

It will produce `design-system.md`. For Claude Design specifically, you can then upload that file as a project asset or as an organization design-system source (Settings → design system → upload). Importing and naming it inside Claude Design is a manual step you do in that product — the skill produces the file, it can't write into your account.

## Repository structure

```
web-design-extractor/
├── SKILL.md                      # skill definition + workflow
├── README.md                     # this file
└── references/
    ├── extraction-guide.md       # where to find each token type; framework gotchas
    ├── fonts-guide.md            # complete font-extraction procedure
    └── output-template.md        # the design-system.md output structure
```

## Security note

The skill treats any `<script>` content in supplied markup as untrusted data — it extracts visual tokens only and never executes or follows instructions found inside page source. As with any skill, only install ones from sources you trust, since skills can run code.

## License
GNU General Public License v3.0
