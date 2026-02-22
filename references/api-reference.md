# MeebCo API — Full Endpoint Reference

Detailed reference for all known Meebits API endpoints. See `SKILL.md` for quick start and game development patterns.

## Domain Map

| Domain | Role | Notes |
|--------|------|-------|
| `meebits.app` | Main site + metadata | Redirects to `meebits.com` for browser requests; serves metadata at `/meebit/{id}` |
| `api.meebits.app` | V2 API | All current image, sprite, and 3D model endpoints |
| `files.meebits.app` | CDN | Static asset hosting — sprites, VRM, images. Returns 404 at root. |
| `images.meebits.app` | Image subdomain | Legacy image paths like `/meebitimages/00001/full.jpg` |

## Reserved DNS Paths

These paths are reserved on `meebits.app` and cannot be used by the website:

| Path | Purpose |
|------|---------|
| `/v1/*` | Version 1 API endpoints |
| `/v2/*` | Version 2 API endpoints (current) |
| `/meebit/*` | Marketplace metadata |
| `/meebitimages/*` | Legacy image endpoints |
| `/api/*` | Administrative endpoints and auth |

## ID Format Rules

| Domain/Endpoint | ID Format | Example |
|----------------|-----------|---------|
| `meebits.app/meebit/{id}` | Unpadded | `/meebit/1`, `/meebit/8900` |
| `api.meebits.app/v2/.../{id}` | Both padded and unpadded work | `/larvalabs_full/1` or `/larvalabs_full/00001` |
| `files.meebits.app/sprites/{id}.png` | Unpadded | `/sprites/1.png` |
| `files.meebits.app/vrm/{id}.vrm` | Unpadded | `/vrm/1.vrm` |
| `images.meebits.app/meebitimages/{id}/...` | Zero-padded to 5 digits | `/meebitimages/00001/full.jpg` |

The API accepts both formats and handles translation internally.

## Public Endpoints — Full Detail

### 1. Marketplace Metadata

```
GET https://meebits.app/meebit/{id}
```

- **Content-Type:** `application/json`
- **ID format:** unpadded
- **Auth:** none
- **Response:** JSON with asset URLs, traits, and licensing info. See SKILL.md for full schema.

### 2. LarvaLabs Full Body Image

```
GET https://api.meebits.app/v2/images/larvalabs_full/{id}
```

- **Content-Type:** `image/jpeg`
- **Legacy URL:** `https://meebits.app/meebitimages/characterimage?index={id}&type=full&imageType=jpg`
- **CDN subdomain:** `https://images.meebits.app/meebitimages/{padded_id}/full.jpg`
- **Auth:** none

### 3. LarvaLabs Portrait

```
GET https://api.meebits.app/v2/images/larvalabs_portrait/{id}
```

- **Content-Type:** `image/jpeg`
- **Legacy URL:** `https://meebits.app/meebitimages/characterimage?index={id}&type=portrait&imageType=jpg`
- **CDN subdomain:** `https://images.meebits.app/meebitimages/{padded_id}/portrait.jpg`
- **Auth:** none

### 4. LarvaLabs Outfit

```
GET https://api.meebits.app/v2/images/larvalabs_outfit/{id}
```

- **Content-Type:** `image/jpeg`
- **Legacy URL:** `https://meebits.app/meebitimages/characterimage?index={id}&type=outfit&imageType=jpg`
- **CDN subdomain:** `https://images.meebits.app/meebitimages/{padded_id}/outfit.jpg`
- **Auth:** none

### 5. LarvaLabs Shoes

```
GET https://api.meebits.app/v2/images/larvalabs_shoes/{id}
```

- **Content-Type:** `image/jpeg`
- **Legacy URL:** `https://meebits.app/meebitimages/characterimage?index={id}&type=shoes&imageType=jpg`
- **CDN subdomain:** `https://images.meebits.app/meebitimages/{padded_id}/shoes.jpg`
- **Auth:** none

### 6. YugaLabs Full Body Image

```
GET https://api.meebits.app/v2/images/yugalabs_full/{id}
```

- **Content-Type:** `image/png`
- **CDN:** `https://files.meebits.app/image/{id}.png`
- **Auth:** none

### 7. YugaLabs Portrait

```
GET https://api.meebits.app/v2/images/yugalabs_portrait/{id}
```

- **Content-Type:** `image/png`
- **CDN:** `https://files.meebits.app/mb1_portrait/{id}.png`
- **Auth:** none

### 8. Sprite Sheet

```
GET https://api.meebits.app/v2/sprites/sheet/{id}
```

- **Content-Type:** `image/png`
- **Size:** 512x512 pixels
- **CDN:** `https://files.meebits.app/sprites/{id}.png`
- **Auth:** none

### 9. VRM 3D Model

```
GET https://api.meebits.app/v2/3d/larvalabs_vrm/{id}
```

- **Content-Type:** `model/vrm` (glTF binary with VRM extensions)
- **CDN:** `https://files.meebits.app/vrm/{id}.vrm`
- **Auth:** none
- **Details:** 24-bone humanoid skeleton, 16 materials, PBR rendering. Compatible with Three.js (`@pixiv/three-vrm`), VTube Studio, VSeeFace, and other VRM-compatible viewers.

## Legacy Query Parameter URL Format

The legacy image endpoint uses query parameters instead of path segments:

```
https://meebits.app/meebitimages/characterimage?index={id}&type={type}&imageType={format}
```

| Parameter | Values | Notes |
|-----------|--------|-------|
| `index` | 1–20000 | Meebit ID (unpadded) |
| `type` | `full`, `portrait`, `outfit`, `shoes` | Image view type |
| `imageType` | `jpg` | Image format |

These legacy URLs still work but the V2 API paths are preferred.

## Token-Gated Asset Types

The following asset types exist but require token-based authentication (Meebit ownership verification). They return HTTP 401 without authorization.

**3D Models:**
- Original FBX (rigged 3D model)
- Original GLB (rigged glTF binary)
- Modular FBX (game-ready)
- Modular GLB (game-ready)
- Modular Blend (rigged Blender file)
- VOX (MagicaVoxel format)
- VOX T-Pose Solid (filled)
- VOX T-Pose Hollow (cored)
- VOX Print-Optimized

**Images:**
- MB1.1 Posters
- Isometric views

## Known Data Gaps

- Meebit IDs 247 and 8785 may be absent from some image formats.
- VRM file metadata contains an outdated author attribution — The Meebit Company is the current IP owner regardless of embedded file metadata.
- No public API documentation is published by The Meebit Company. This reference is based on observed endpoint behavior.
