---
name: meebco-api
description: >
  Use when building games, apps, or creative projects that use Meebits assets.
  Covers fetching Meebit images (full body, portrait, outfit, shoes), sprite sheets,
  VRM 3D avatars, and metadata from the Meebits API. Use when requests mention
  Meebits, MeebCo, meebit images, sprites, VRM models, 3D voxel avatars,
  meebits.app, api.meebits.app, files.meebits.app, or integrating Meebit
  characters into Three.js, web games, or metaverse projects.
---

# MeebCo API — Meebits Asset Integration

Meebits are 20,000 unique 3D voxel characters created by Larva Labs. The collection is now owned and operated by The Meebit Company. This skill provides the public API endpoints for loading Meebit images, sprites, and 3D models into games and applications.

## Quick Start

Fetch a Meebit's metadata to discover all available asset URLs:

```javascript
const res = await fetch("https://meebits.app/meebit/1");
const meebit = await res.json();

// meebit.image       → full body image URL
// meebit.vrm         → VRM 3D model URL
// meebit.sprite_sheet → sprite sheet URL
// meebit.attributes  → array of { trait_type, value } pairs
```

Show a Meebit image directly:

```html
<img src="https://api.meebits.app/v2/images/larvalabs_full/1"
     alt="Meebit #1" loading="lazy"
     onerror="this.style.display='none'">
```

Load a VRM 3D avatar:

```javascript
const VRM_URL = (id) => `https://api.meebits.app/v2/3d/larvalabs_vrm/${id}`;
// Also available via CDN: https://files.meebits.app/vrm/${id}.vrm
```

## Public Endpoints

All public endpoints require no authentication. Meebit IDs range from 1 to 20,000.

**Image render origins:**
- **LL (Larva Labs)** — original renders created by Larva Labs when they launched the collection
- **YL (Yuga Labs)** — renders created by Yuga Labs during the period they owned the collection, before selling it to The Meebit Company

| Asset | V2 API URL | CDN/Legacy Alternative | Format |
|-------|-----------|----------------------|--------|
| Metadata | `meebits.app/meebit/{id}` | — | JSON |
| Full Body (LL) | `api.meebits.app/v2/images/larvalabs_full/{id}` | `meebits.app/meebitimages/characterimage?index={id}&type=full&imageType=jpg` | JPEG |
| Portrait (LL) | `api.meebits.app/v2/images/larvalabs_portrait/{id}` | `meebits.app/meebitimages/characterimage?index={id}&type=portrait&imageType=jpg` | JPEG |
| Outfit (LL) | `api.meebits.app/v2/images/larvalabs_outfit/{id}` | `meebits.app/meebitimages/characterimage?index={id}&type=outfit&imageType=jpg` | JPEG |
| Shoes (LL) | `api.meebits.app/v2/images/larvalabs_shoes/{id}` | `meebits.app/meebitimages/characterimage?index={id}&type=shoes&imageType=jpg` | JPEG |
| Full Body (YL) | `api.meebits.app/v2/images/yugalabs_full/{id}` | `files.meebits.app/image/{id}.png` | PNG |
| Portrait (YL) | `api.meebits.app/v2/images/yugalabs_portrait/{id}` | `files.meebits.app/mb1_portrait/{id}.png` | PNG |
| Sprite Sheet | `api.meebits.app/v2/sprites/sheet/{id}` | `files.meebits.app/sprites/{id}.png` | PNG |
| VRM 3D Model | `api.meebits.app/v2/3d/larvalabs_vrm/{id}` | `files.meebits.app/vrm/{id}.vrm` | VRM/glTF |

Additional 3D formats (GLB, FBX, VOX, Blend) and premium image types (posters, isometric views) exist but are token-gated for Meebit owners.

## Metadata Schema

`GET https://meebits.app/meebit/{id}` returns JSON:

```json
{
  "name": "Meebit #1",
  "description": "Meebits are 20,000 unique 3D voxel characters...",
  "image": "https://api.meebits.app/v2/images/yugalabs_full/1",
  "original_image": "https://api.meebits.app/v2/images/larvalabs_full/1",
  "mb1_portrait": "https://api.meebits.app/v2/images/yugalabs_portrait/1",
  "sprite_sheet": "https://api.meebits.app/v2/sprites/sheet/1",
  "vrm": "https://api.meebits.app/v2/3d/larvalabs_vrm/1",
  "license_terms": "https://licenseterms.meebits.app/",
  "external_url": "https://opensea.io/item/...",
  "attributes": [
    { "trait_type": "Type", "value": "Human" },
    { "trait_type": "Hair Style", "value": "Bald" },
    { "trait_type": "Shirt", "value": "Athletic Jacket" }
  ]
}
```

Always prefer fetching metadata first — it provides canonical URLs for all public assets for a given Meebit.

## Game Development Patterns

### Loading a VRM Avatar (Three.js)

VRM files are glTF-based 3D models with a 24-bone humanoid skeleton and PBR materials. Load with `@pixiv/three-vrm`:

```javascript
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { VRMLoaderPlugin } from "@pixiv/three-vrm";

const loader = new GLTFLoader();
loader.register((parser) => new VRMLoaderPlugin(parser));

const gltf = await loader.loadAsync(
  `https://api.meebits.app/v2/3d/larvalabs_vrm/${meebitId}`
);
const vrm = gltf.userData.vrm;
scene.add(vrm.scene);
```

### Sprite Sheets for 2D Games

Each Meebit has a 512x512 PNG sprite sheet:

```javascript
const SPRITE_URL = (id) => `https://files.meebits.app/sprites/${id}.png`;

const img = new Image();
img.crossOrigin = "anonymous";
img.src = SPRITE_URL(meebitId);
img.onload = () => ctx.drawImage(img, 0, 0);
```

### Character Selection Gallery

Display multiple Meebits with lazy loading and fallback handling:

```javascript
const MEEBIT_IMG = (id) =>
  `https://api.meebits.app/v2/images/larvalabs_full/${id}`;

// SVG fallback for failed loads
const FALLBACK = (id) =>
  `data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 width=%22200%22 height=%22200%22><rect fill=%22%23222%22 width=%22200%22 height=%22200%22/><text fill=%22%23666%22 x=%2250%25%22 y=%2250%25%22 dominant-baseline=%22middle%22 text-anchor=%22middle%22 font-size=%2214%22>Meebit %23${id}</text></svg>`;

function meebitCard(id) {
  return `<img src="${MEEBIT_IMG(id)}" alt="Meebit #${id}"
    loading="lazy" onerror="this.src='${FALLBACK(id)}'">`;
}
```

### Trait System for Game Mechanics

Use the `attributes` array from metadata to drive game stats or abilities:

```javascript
const meta = await fetch(`https://meebits.app/meebit/${id}`).then(r => r.json());
const traits = Object.fromEntries(
  meta.attributes.map(a => [a.trait_type, a.value])
);
// traits.Type → "Human", "Robot", "Skeleton", etc.
// Use type rarity for power scaling, traits for visual customization
```

## Meebits Collection Data

- **Total supply:** 20,000 (IDs 1–20000)
- **Creator:** Larva Labs
- **Current owner:** The Meebit Company
- **License:** [licenseterms.meebits.app](https://licenseterms.meebits.app/)

### Types and Rarity

| Type | Count | % |
|------|------:|---:|
| Human | 18,881 | 94.4% |
| Pig | 711 | 3.6% |
| Elephant | 256 | 1.3% |
| Robot | 72 | 0.4% |
| Skeleton | 57 | 0.3% |
| Visitor | 18 | 0.1% |
| Dissected | 5 | 0.03% |

### Trait Categories (22 total)

Organized by body section:

- **Head:** hair_style, hair_color, hat, hat_color
- **Face:** beard, beard_color, glasses, glasses_color, earring
- **Upper Body:** necklace, shirt, shirt_color, overshirt, overshirt_color
- **Lower Body:** pants, pants_color, shoes, shoes_color
- **Extras:** tattoo, tattoo_motif, jersey_number
- **Identity:** type, gender

### Useful Links

- 3D Viewer: `https://turnon.meebits.app/viewer/{id}`
- Collection page: `https://meebits.com/collection/{id}`

## Infrastructure Notes

| Domain | Purpose |
|--------|---------|
| `meebits.app` | Metadata endpoint + legacy image URLs |
| `api.meebits.app` | V2 API — all image, sprite, and 3D endpoints |
| `files.meebits.app` | CDN — sprites, VRM files, images |

- **ID format:** The metadata endpoint (`/meebit/{id}`) uses unpadded IDs. The v2 API accepts both padded (`00001`) and unpadded (`1`).
- **No authentication** required for any public endpoint listed above.
- **No known rate limits** — use conservative fetch patterns for batch operations.
- **Known gaps:** Meebit IDs 247 and 8785 may be absent from some image formats.
- The main site `meebits.app` redirects to `meebits.com`.

## Guardrails

- Respect license terms at [licenseterms.meebits.app](https://licenseterms.meebits.app/).
- Prefer the metadata-first approach — fetch metadata, use the embedded URLs rather than hardcoding endpoint paths.
- Handle missing or failed assets gracefully with fallback images.
- Do not attempt to access token-gated endpoints without owner authorization.
- Never hardcode API secrets or attempt to bypass token gating.
- VRM file metadata may contain outdated authorship fields — The Meebit Company is the current IP owner.

See `references/api-reference.md` for full endpoint details including legacy URL parameters and domain mapping.
