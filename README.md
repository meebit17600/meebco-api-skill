# MeebCo API Skill

A [Claude Code](https://claude.ai/claude-code) skill for integrating [Meebits](https://meebits.com) assets into games, apps, and creative projects.

Meebits are 20,000 unique 3D voxel characters created by Larva Labs, now owned and operated by The Meebit Company.

## What's in this skill

- **Public API endpoints** for fetching images (full body, portrait, outfit, shoes), sprite sheets, VRM 3D avatars, and metadata
- **Game development patterns** — Three.js VRM loading, sprite rendering, character galleries, trait-based game mechanics
- **Collection data** — all 7 Meebit types, 22 trait categories, rarity distribution

## Quick example

```javascript
// Fetch a Meebit's metadata
const meebit = await fetch("https://meebits.app/meebit/1").then(r => r.json());

// meebit.image        → full body image URL
// meebit.vrm          → VRM 3D model URL
// meebit.sprite_sheet  → sprite sheet URL
// meebit.attributes   → array of { trait_type, value } pairs
```

## Files

| File | Description |
|------|-------------|
| `Meebitskills.md` | Core skill — endpoints, code patterns, collection data |
| `references/api-reference.md` | Detailed endpoint reference with legacy URLs and domain mapping |

## Usage

Copy into your Claude Code skills directory:

```bash
cp -r . ~/.claude/skills/meebco-api/
```

Or reference it directly in your project's `.claude/skills/` folder.

## License

Meebits IP belongs to The Meebit Company. See [licenseterms.meebits.app](https://licenseterms.meebits.app/) for asset licensing terms.
