# Burn Rate — Visual Upgrade Research: 3D Isometric Cartoon Style

**Date:** 2025-02-25  
**Goal:** Upgrade browser-based "Burn Rate" PoC from emoji/CSS to a 3D isometric cartoon look (Theme Hospital / Two Point Hospital meets Silicon Valley HBO)  
**Constraint:** Must run in browser. Single-page or minimal files preferred.

---

## Table of Contents

1. [Browser-Based Isometric Game Engines](#1-browser-based-isometric-game-engines)
2. [Art Asset Options](#2-art-asset-options)
3. [Visual Style References](#3-visual-style-references)
4. [Specific Asset Needs for Burn Rate](#4-specific-asset-needs-for-burn-rate)
5. [Technical Approach Recommendation](#5-technical-approach-recommendation)
6. [Animation Approach](#6-animation-approach)
7. [Implementation Plan](#7-implementation-plan)

---

## 1. Browser-Based Isometric Game Engines

### Engine Comparison Matrix

| Engine | Type | Isometric Support | Bundle Size | Learning Curve | Best For |
|--------|------|-------------------|-------------|----------------|----------|
| **Phaser 3** | 2D game framework | Via plugin (WIP) | ~1.2 MB | Medium | Full game framework with physics, audio, scenes |
| **PixiJS** | 2D renderer | Manual or via libs | ~450 KB | Low-Medium | Raw rendering performance, custom game logic |
| **Three.js** | 3D engine | Native (OrthographicCamera) | ~600 KB | Medium-High | True 3D with lighting, shadows, future-proofing |
| **Babylon.js** | 3D engine | Native | ~4.5 MB min | High | Complex 3D scenes, physics, large projects |
| **Kaboom.js/Kaplay** | 2D game framework | Manual transforms | ~200 KB | Low | Quick prototypes, simple games |
| **Pure CSS** | CSS transforms | Manual | 0 KB | Low | Static displays, not real games |
| **Canvas API** | Native 2D | Fully manual | 0 KB | Medium | Maximum control, no dependencies |

### Detailed Analysis

#### Phaser 3 + Isometric Plugin
- **Maturity:** The isometric plugin exists in multiple forks (lewster32 → sebashwa → BrennanTanner for Phaser 3.70+), but all are labeled "WIP"
- **Pros:** Full game framework (physics, audio, input, scenes, cameras), huge community, well-documented, Tiled map editor integration
- **Cons:** Isometric plugin is not first-class; the plugin forks lag behind Phaser releases. You'd be fighting the framework for isometric features
- **Verdict:** ⚠️ Good framework, but isometric is a second-class citizen. Risk of plugin incompatibility with future Phaser updates

#### PixiJS
- **Maturity:** Very mature renderer (v8+), isometric done through manual coordinate transforms or community libs (`pixi-isometric-tilemaps`)
- **Pros:** Fastest pure 2D renderer (~2x faster than Phaser for rendering). Very lightweight (450KB). WebGPU support in v8. Maximum flexibility
- **Cons:** Not a game framework — no built-in physics, audio, scenes. You build everything yourself. Isometric community libs are sparse and often unmaintained
- **Verdict:** ✅ Excellent if you want a custom engine feel. Best raw rendering performance for 2D sprites

#### Three.js ⭐ RECOMMENDED
- **Maturity:** The most popular 3D JS library. Massive ecosystem, 90K+ GitHub stars
- **Isometric approach:** Use `OrthographicCamera` positioned at isometric angle (typically 35.264° elevation, 45° rotation). Objects exist in true 3D space
- **Pros:**
  - **Real depth sorting** — no z-order hacks, the GPU handles it
  - **Real lighting** — dynamic shadows, ambient occlusion come free
  - **Camera flexibility** — zoom, rotate, pan trivially
  - **Future-proof** — can add 3D elements, particle effects, shader effects without rewrites
  - **GLTF ecosystem** — Blender → .glb → Three.js pipeline is mature
  - **InstancedMesh** — batch rendering for repeated objects (desks, chairs) = excellent performance
  - Proven in production: browser-based isometric RPGs exist using Three.js
- **Cons:**
  - Managing a full 3D scene for a 2D-looking game (over-engineering concern)
  - Slightly higher complexity for simple sprite work
  - Need to handle frustum culling, object pooling for performance
- **Performance:** 60 FPS achievable even on mobile with proper culling and instancing
- **Verdict:** ✅✅ **Best choice for this project.** True 3D solves depth sorting nightmares. Real lighting gives the cartoon look. GLTF pipeline enables rapid asset creation.

#### Babylon.js
- **Maturity:** Very mature, backed by Microsoft
- **Pros:** More built-in features than Three.js (physics, GUI, particles). Better TypeScript support
- **Cons:** Much larger bundle (4.5MB+ minimum vs ~600KB). Overkill for isometric management sim. Smaller community than Three.js. Tree-shaking is harder
- **Verdict:** ❌ Too heavy for this use case. Three.js does everything needed at 1/7th the size

#### Kaboom.js / Kaplay
- **Pros:** Extremely simple API, tiny bundle
- **Cons:** Not designed for isometric. Limited ecosystem. Better for jam games, not production management sims
- **Verdict:** ❌ Too simple

#### Pure CSS Isometric
- **Approach:** CSS `transform: rotateX(60deg) rotateZ(45deg)` on containers
- **Pros:** Zero dependencies, works with DOM elements
- **Cons:** Poor performance with many elements, no real game loop, z-index nightmares, no shader effects
- **Verdict:** ❌ Only suitable for landing pages or static mockups, not interactive games

#### HTML5 Canvas (Manual)
- **Pros:** Zero dependencies, full control
- **Cons:** You'd be reinventing everything — depth sorting, camera systems, sprite batching. Enormous effort for no benefit over PixiJS/Three.js
- **Verdict:** ❌ Unless you want to learn fundamentals, use a library

### Performance Considerations for Management Sims
- Management sims have **lots of static objects** and **few moving entities** — perfect for instanced rendering
- Depth sorting is the #1 headache in 2D isometric engines (painter's algorithm + multi-tile objects = pain). True 3D (Three.js) eliminates this entirely
- Target: 60 FPS with 200+ objects on screen. All top engines achieve this with proper techniques

---

## 2. Art Asset Options

### 2.1 Free Isometric Asset Packs

| Source | Notable Packs | Quality | License | Office-Relevant? |
|--------|---------------|---------|---------|-------------------|
| **Kenney.nl** | Isometric Prototype Tiles (walls, floors, objects, character w/ 8 directions + 3 anims), Isometric Blocks, Furniture Kit | High (clean, consistent) | CC0 (public domain) | ✅ Prototype tiles + Furniture Kit |
| **OpenGameArt** | Furniture Kit (3D models + isometric sprites — chairs, sofas, tables, bookcases, kitchen, bathroom) | Medium-High | Various (check each) | ✅ Furniture directly usable |
| **itch.io** | 1,008 Isometric Floor Tiles (free), various packs | Mixed | Various | Partial |
| **Kenney Asset Forge** | Tool for creating 3D models/2D sprites from 900+ pre-made blocks ($19.99) | Create-your-own | CC0 output | ✅ Can build custom office furniture |

**Key finding:** Kenney's free packs + the Furniture Kit from OpenGameArt provide excellent prototyping materials. The Kenney Isometric Prototype Tiles pack includes an 8-direction character with 3 animation states — exactly what we need for initial testing.

### 2.2 AI-Generated Isometric Sprites

**Current state (2025):**
- **Midjourney v6+** excels at isometric art. Prompts like "isometric office desk with computer, cartoon style, white background, game asset" produce very usable results
- **DALL-E 3** can generate isometric assets but less consistent angle/style across generations
- **Stable Diffusion + ControlNet** — most control over consistency, requires more setup

**Challenges:**
- **Consistency** is the main issue — each generation varies in perspective angle, color palette, and style
- **Background removal** needed for game-ready sprites
- **Exact tile alignment** requires post-processing (Photoshop/GIMP)
- Works best for **concept art and individual objects**, not complete tilesets

**Best approach with AI:**
1. Generate individual assets with Midjourney using style-locked prompts (same style ref, same angle spec)
2. Post-process: background removal, color correction, alignment to grid
3. Use AI for concept/ideation, then trace/refine programmatically or manually

**Verdict:** AI generation is viable for rapid prototyping and concept exploration. For production-quality consistent tilesets, it needs human post-processing. Budget 30-60 min per asset for AI generation + cleanup.

### 2.3 SVG-Based Isometric (Programmatic)

- **Icograms Designer** — web tool for creating isometric illustrations from pre-made icons. Can export SVG + PNG. Could work for static scenes but not animated games
- **SVGMaker.io** — AI-powered SVG generation with isometric preset
- **Programmatic SVG** — you can generate isometric shapes with code (cubes, prisms) using CSS/SVG transforms

**Pros:** Infinitely scalable, small file size, can be themed/colored programmatically
**Cons:** Limited detail/character, doesn't suit cartoon style, performance issues with many SVG elements in DOM
**Verdict:** ⚠️ Good for UI elements (buttons, icons, panels) but not for the game world itself

### 2.4 Voxel Art (MagicaVoxel) ⭐ STRONG CONTENDER

**MagicaVoxel is a free voxel editor with built-in isometric export:**
- **ISO export** — exports 4-direction isometric sprites as PNG automatically
- **2D export** — exports flat sprites
- Can be rotated manually for 8 directions (or scripted)
- Built-in rendering with lighting, shadows, and materials
- Export to OBJ for use in Three.js directly (or render to sprites)

**Why this is compelling for Burn Rate:**
1. **Voxel art looks inherently cartoon-like** — perfect for Two Point Hospital aesthetic
2. **Very fast to create assets** — build a desk in 20 minutes vs hours in Blender
3. **Consistent style guaranteed** — same tool, same voxel size, same render settings
4. **Two pipelines available:**
   - Export as sprites (PNG) → use in 2D engine
   - Export as OBJ/GLTF → load directly in Three.js for true 3D
5. **Low skill floor** — anyone can stack voxels; no modeling/texturing expertise needed

**MagicaVoxel → Three.js pipeline:**
1. Model in MagicaVoxel
2. Export as OBJ (or convert to GLTF via Blender)
3. Load into Three.js scene
4. Apply cartoon shader (toon shading / cel shading) for the Two Point Hospital look

**Verdict:** ✅✅ Fastest path to consistent, good-looking isometric assets. Especially with Three.js for direct 3D rendering.

### 2.5 Pre-Rendered 3D Sprites (Blender Pipeline)

**Classic approach used by many isometric games (Diablo, StarCraft, etc.):**
1. Model in Blender (3D)
2. Set up orthographic camera at isometric angle
3. Render 8 rotations × N animation frames → spritesheet
4. Use in 2D engine

**Pros:** Professional quality, total control, proven pipeline
**Cons:** Requires Blender proficiency, slower iteration than voxels, large spritesheet files
**Time estimate:** 2-4 hours per complex object, 8-16 hours per animated character

**Verdict:** ✅ Best for final production quality but too slow for MVP/prototyping

### 2.6 Commissioned Art

**Fiverr pricing (as of 2025):**
- Simple isometric tile/object: $5-$30
- Detailed isometric building sprite: $30-$100
- Animated character spritesheet (8-dir): $100-$300
- Complete tileset (20-30 objects): $200-$500

**Professional studio (e.g., RetroStyle Games):**
- Full isometric tileset: $2,000-$5,000
- Character set with animations: $1,000-$3,000
- Complete art package for indie game: $5,000-$15,000

**Verdict:** Viable for polish phase, not for prototyping

### Summary: Fastest Path to Good Visuals

| Approach | Time to First Result | Consistency | Quality | Cost |
|----------|----------------------|-------------|---------|------|
| Kenney free packs | 1 hour (download + integrate) | ✅ High | Prototype-level | Free |
| MagicaVoxel → Three.js | 1-2 days (learn + create core assets) | ✅ High | Good (charming voxel look) | Free |
| AI generation + cleanup | 2-3 days | ⚠️ Medium | Medium-High | $20-50/month for Midjourney |
| Blender → sprites | 1-2 weeks | ✅ High | Professional | Free (your time) |
| Fiverr commission | 1-2 weeks (delivery) | Varies | Varies | $200-$500 for MVP set |

**Recommendation:** Start with **Kenney free packs** for immediate prototyping, then transition to **MagicaVoxel → Three.js** for the custom Burn Rate style.

---

## 3. Visual Style References

### Two Point Hospital — What Makes It Work

- **Engine:** Unity (full 3D rendered from isometric camera angle)
- **Art style:** "Claymation-looking" — deliberately handmade feel with smooth, rounded shapes
- **Key design decisions:**
  - Bright, saturated colors with clear contrast
  - Characters with oversized heads (chibi proportions ~3:1 head-to-body)
  - Smooth, soft lighting (no harsh shadows)
  - Objects are slightly exaggerated/cartoonish
  - "Future-proof" style that doesn't date (avoids hyper-realism)
  - Everything is slightly rounded — no sharp corners
  - Limited color palette per object for clarity
  - Clear visual hierarchy: floors are muted, furniture is vivid, characters are bright

### Theme Hospital (Original, 1997)
- **Style:** Pre-rendered 2D isometric sprites
- **Key elements:** Pixel art charm, exaggerated character proportions, clear room boundaries
- **What carries forward:** The humor in visual design (silly illness effects, goofy animations)

### Game Dev Tycoon
- **Style:** Simplified isometric, flat colors, minimal detail
- **Approach:** Room-based layout, character indicators on desks, focus on UI over visual detail
- **Lesson:** You don't need AAA art to make a compelling management sim — gameplay clarity matters more

### Startup Company (Steam)
- **Tags:** Isometric, management sim, office-based — literally the closest reference game
- **Style:** Clean isometric office view, furniture on grid, employees at desks
- **Approach:** 2D isometric sprites, room-based building, standard office furniture set
- **Key takeaway:** This game proves the concept works. Our differentiator needs to be the Silicon Valley humor/satire layer

### Overcooked
- **Style:** Full 3D, low-poly cartoon
- **Relevant because:** Shows how a management/action game can use bright, cartoon 3D with isometric-ish camera
- **Lesson:** Bold colors + simple shapes = highly readable even in chaos

### Defining Elements of "Isometric Management Sim" Visual Style

1. **Grid-based floor plan** visible at all times
2. **Clear room boundaries** (walls with transparency or cutaway when selected)
3. **Top-down 3/4 view** (2:1 isometric ratio standard)
4. **Characters with readable silhouettes** — each role visually distinct at small sizes
5. **Furniture on grid cells** — snaps to grid, clear placement
6. **Status indicators** floating above characters/rooms
7. **Muted floors, vivid objects** — visual hierarchy
8. **UI panels** around the edges, transparent or semi-transparent

---

## 4. Specific Asset Needs for Burn Rate

### Complete Asset Inventory

#### A. Environment (Tiles & Structures)
| Asset | Variants | Priority |
|-------|----------|----------|
| Floor tiles | Carpet (3 colors), hardwood, concrete, startup-chic polished | MVP |
| Walls | Standard, glass/window, half-wall (open office) | MVP |
| Doors | Standard, glass door, server room door | MVP |
| Room dividers | Cubicle walls, plant dividers | Post-MVP |
| Outdoor/patio tiles | Rooftop, balcony | Post-MVP |

#### B. Furniture (Core Office)
| Asset | Variants | Priority |
|-------|----------|----------|
| Regular desk | Standard, L-shaped | MVP |
| Standing desk | With monitor | MVP |
| Ergonomic chair | Standard, gaming chair | MVP |
| Computer/monitor | Single, dual-monitor, laptop | MVP |
| Meeting table | 4-person, 8-person, standing huddle | MVP |
| Whiteboard | Clean, covered in sticky notes | MVP |
| Filing cabinet | — | Post-MVP |

#### C. Startup Amenities (The Fun Stuff)
| Asset | Priority | Silicon Valley Humor |
|-------|----------|---------------------|
| Kombucha bar / Craft beer tap | MVP | Core startup satire |
| Ping pong table | MVP | Classic startup perk |
| Foosball table | Post-MVP | — |
| Nap pod | MVP | Google-style |
| Bean bags | MVP | "Casual culture" |
| Meditation corner / Yoga mats | Post-MVP | Wellness culture |
| Game room (arcade machine) | Post-MVP | — |
| Snack bar / Micro-kitchen | MVP | Free snacks = culture |
| Espresso machine | MVP | Fuel for engineers |
| Indoor slide | Post-MVP | Peak Silicon Valley excess |

#### D. Employee Characters
| Role | Visual Identifier | Priority |
|------|-------------------|----------|
| Developer/Engineer | Hoodie, laptop | MVP |
| Designer | Turtleneck, tablet | MVP |
| Sales/BizDev | Patagonia vest, phone | MVP |
| Product Manager | Moleskine notebook, standing | MVP |
| CEO/Founder | Allbirds, "vision" T-shirt | MVP |
| HR | Business casual, clipboard | Post-MVP |
| Data Scientist | Glasses, dual monitors | Post-MVP |
| DevOps/SRE | Dark circles, coffee mug | Post-MVP |
| Intern | Backpack, eager expression | Post-MVP |
| Investor (visitor) | Suit, Patagonia vest combo | Post-MVP |

**Character animations needed per role:**
| Animation | Frames (minimum) | Priority |
|-----------|-------------------|----------|
| Idle (at desk) | 4 frames | MVP |
| Working (typing) | 6 frames | MVP |
| Walking | 8 frames × 4 directions (minimum) | MVP |
| Talking (in meeting) | 4 frames | MVP |
| On phone | 4 frames | Post-MVP |
| Celebrating | 6 frames | Post-MVP |
| Stressed/frustrated | 4 frames | Post-MVP |
| Sleeping at desk | 4 frames | Post-MVP |

#### E. Office Decorations
| Asset | Priority |
|-------|----------|
| Motivational poster ("Move fast and break things") | MVP |
| Plants (potted, hanging) | MVP |
| Company logo on wall | MVP |
| Sticky notes / Kanban board | MVP |
| Awards/trophies on shelf | Post-MVP |
| Neon sign ("HUSTLE") | Post-MVP |
| Startup swag shelf | Post-MVP |

#### F. UI Elements
| Element | Priority |
|---------|----------|
| Resource panel (burn rate, runway, MRR) | MVP |
| Employee status bubbles | MVP |
| Build/furniture placement palette | MVP |
| Research/upgrade tree panel | MVP |
| Notification toasts | MVP |
| Mini-map | Post-MVP |
| Financial charts overlay | Post-MVP |

### Minimum Asset Count for MVP

| Category | Count |
|----------|-------|
| Floor tiles | 4 |
| Wall types | 3 |
| Furniture | 12 |
| Amenities | 6 |
| Characters (roles) | 5 |
| Character animations | 4 per role × 5 = 20 anim sets |
| Decorations | 5 |
| UI elements | 5 panels/overlays |
| **Total unique visual assets** | **~55** |

If using MagicaVoxel → Three.js (3D), each asset is one model (no directional sprites needed — camera rotation handles views). This **halves the effective workload** compared to 2D spritesheets.

---

## 5. Technical Approach Recommendation

### ⭐ RECOMMENDED: Three.js + MagicaVoxel Pipeline

This is the **fastest path to impressive isometric visuals** in a browser that also scales to production quality.

#### Why This Combination Wins

1. **Three.js solves the hardest problems automatically:**
   - Depth sorting (z-buffer, not painter's algorithm)
   - Lighting and shadows (cartoon shading via MeshToonMaterial)
   - Camera manipulation (zoom, rotate, pan)
   - Object picking (raycasting for click/hover)
   - Performance (InstancedMesh, frustum culling)

2. **MagicaVoxel solves the art problem:**
   - Free tool, minimal learning curve (~2 hours to proficiency)
   - Built-in isometric export for testing
   - OBJ export → Blender → GLTF for Three.js
   - Voxel aesthetic inherently matches the "charming cartoon" target
   - Consistent style guaranteed (same tool = same look)

3. **Together they enable rapid iteration:**
   - Make voxel model (20-30 min per object)
   - Export OBJ → convert to GLTF (5 min)
   - Load in Three.js scene → instant preview
   - Adjust lighting/camera → immediately see result

#### Alternative Approaches Considered

**Pre-rendered 3D sprites (Blender → PNG → Phaser/PixiJS):**
- Works but slower pipeline
- Every asset change requires re-rendering all directions × all frames
- Depth sorting remains a 2D problem (painful for multi-tile objects)
- More total assets to manage (8 directions × N frames per animation)

**Existing isometric tileset + customization:**
- Fastest to first visual, but you'll hit a ceiling fast
- Hard to match the "Silicon Valley satire" aesthetic with generic tilesets
- Mixing free packs creates visual inconsistency

**Pure PixiJS with 2D sprites:**
- Better raw 2D rendering performance
- But depth sorting for multi-tile objects (ping pong table, L-shaped desk) is very hard
- No real lighting = flat look that doesn't match target aesthetic
- Good fallback if Three.js proves too complex

#### How Two Point Hospital Does It (for reference)

Two Point Hospital uses **Unity 3D** with:
- Full 3D models rendered from an isometric camera angle
- Custom "claymation" shader for the handmade look
- Real-time lighting with soft shadows
- Characters with skeletal animation (not spritesheets)
- Grid-based placement system

Our Three.js approach mirrors this architecture at the right scale for a browser game.

---

## 6. Animation Approach

### For Three.js + 3D Models: Skeletal Animation

**Since we're recommending Three.js (true 3D), use skeletal animation — not spritesheets:**

1. **Create character in MagicaVoxel** → export OBJ
2. **Import in Blender** → add armature (skeleton) → animate
3. **Export as GLTF/GLB** (includes model + animations)
4. **Load in Three.js** → `AnimationMixer` handles playback

**Advantages over spritesheets:**
- **No directional sprites needed** — the 3D model faces any direction
- **Smooth interpolation** — skeleton tweens between keyframes automatically
- **Fewer assets** — one model + one animation vs 8-direction × N-frame spritesheets
- **Dynamic** — can blend animations, respond to events in real-time
- **Smaller file size** — skeleton data + keyframes << hundreds of sprite frames

### Animation Requirements (Three.js Skeletal)

| Animation | Keyframes | Duration | Notes |
|-----------|-----------|----------|-------|
| Idle | 2-4 keys | 2-3 sec loop | Subtle breathing/shifting |
| Working/Typing | 4-6 keys | 1-2 sec loop | Hands on keyboard |
| Walking | 6-8 keys | 0.5-1 sec cycle | Locomotion blend |
| Talking | 4-6 keys | 1-2 sec loop | Head/hand gestures |
| Celebrating | 6-8 keys | 2-3 sec | Arms up, jump |
| Stressed | 4-6 keys | 2-3 sec loop | Head in hands |

### Simplified Animation (MVP Shortcut)

For MVP, you can skip full skeletal animation and use simple transforms:
1. **Idle:** Slight Y-axis bob (sine wave, 1 line of code)
2. **Working:** Same + slight scale pulse on hands/arms
3. **Walking:** Translate + bob + slight lean
4. **Talking:** Head rotation oscillation

This "minimal animation" approach looks surprisingly good with cartoon voxel models and costs zero art time.

### Fallback: Spritesheet Approach (if using 2D engine)

If for any reason Three.js doesn't work out and you go 2D:
- **Minimum directions:** 4 (NE, NW, SE, SW) — "true isometric" only needs 4
- **Comfortable directions:** 8 (adds N, S, E, W)
- **Frames per walk cycle:** 6-8 minimum for smooth look
- **Frames per idle:** 2-4
- **Total per character (4-dir MVP):** 4 states × 4 directions × 6 avg frames = **96 sprite frames**
- **Total per character (8-dir):** 4 states × 8 directions × 6 frames = **192 sprite frames**
- **Spritesheet size:** ~2048×2048 per character at 64px sprites

Tools for spritesheet animation:
- **Spine 2D** ($69-$329): Industry standard skeletal 2D animation, exports to PixiJS/Phaser
- **DragonBones** (free): Open-source alternative to Spine, Phaser integration
- **Aseprite** ($20): Pixel art + spritesheet tool

---

## 7. Implementation Plan

### Phase 0: Proof of Concept (1-2 days)
**Goal:** Validate Three.js isometric rendering with placeholder assets

- [ ] Set up Three.js with OrthographicCamera at isometric angle
- [ ] Render a simple grid of colored cubes (floor tiles)
- [ ] Add camera controls (zoom, pan)
- [ ] Place a few box primitives as "furniture"
- [ ] Add MeshToonMaterial for cartoon shading
- [ ] Add basic ambient + directional light with soft shadows
- [ ] Demonstrate: clicking to place/remove objects on grid

**Effort:** 1-2 days for someone with JS experience, no Three.js needed yet  
**AI tools:** Claude/Copilot can scaffold the entire Three.js setup

### Phase 1: Core Art Assets (3-5 days)
**Goal:** Replace placeholder cubes with actual voxel models

- [ ] Install MagicaVoxel, learn basics (2 hours)
- [ ] Create core floor tiles (4 types)
- [ ] Create walls (3 types) + windows
- [ ] Create desk, chair, computer (the "engineer workstation")
- [ ] Create 1 character model (simple voxel person)
- [ ] Set up MagicaVoxel → Blender → GLTF pipeline
- [ ] Import all into Three.js scene

**Effort:** 3-5 days (mostly asset creation)  
**AI tools:** AI image generators for reference/inspiration; Claude for GLTF loading code

### Phase 2: Grid System + Placement (2-3 days)
**Goal:** Working room/furniture placement on isometric grid

- [ ] Implement tile grid data structure
- [ ] Raycasting for mouse → grid cell mapping
- [ ] Build mode: select furniture from palette, place on grid
- [ ] Room detection (enclosed walls = room)
- [ ] Visual feedback (valid/invalid placement highlighting)
- [ ] Save/load grid state

**Effort:** 2-3 days  
**AI tools:** Claude can generate the grid math and raycasting code

### Phase 3: Characters + Animation (3-5 days)
**Goal:** Animated characters walking around the office

- [ ] Create 5 character models in MagicaVoxel (role variants)
- [ ] Add simple armature in Blender (head, torso, arms, legs)
- [ ] Create walk + idle + working animations
- [ ] Implement pathfinding (A* on grid)
- [ ] Characters assigned to desks, walk between points
- [ ] Visual role indicators (color coding, accessories)

**Effort:** 3-5 days (Blender rigging is the bottleneck)  
**AI tools:** Mixamo can auto-rig humanoid characters; Claude for pathfinding code

### Phase 4: Amenities + Decorations (2-3 days)
**Goal:** Build out the Silicon Valley office aesthetic

- [ ] Create amenity models (kombucha bar, ping pong, bean bags, nap pod)
- [ ] Create decoration models (whiteboard, plants, posters)
- [ ] Implement amenity effects (morale boost, productivity modifiers)
- [ ] Character interactions with amenities (sitting on bean bag, playing ping pong)

**Effort:** 2-3 days  
**AI tools:** MagicaVoxel is fast enough that most objects take 20-30 min each

### Phase 5: UI Overlay (2-3 days)
**Goal:** Game UI with resource panels, build palette, notifications

- [ ] HTML/CSS overlay on Three.js canvas (not in-scene UI)
- [ ] Resource bar (burn rate, runway, MRR, headcount)
- [ ] Build palette (sidebar with furniture categories)
- [ ] Employee info popup (click character → see stats)
- [ ] Toast notifications ("New hire arrived!", "Investor visiting!")
- [ ] Financial mini-charts

**Effort:** 2-3 days  
**AI tools:** Standard web dev — Claude handles this easily

### Phase 6: Polish + Effects (2-3 days)
**Goal:** Visual polish that makes it feel alive

- [ ] Particle effects (coffee steam, computer glow, ping pong ball)
- [ ] Day/night lighting cycle
- [ ] Camera smooth transitions
- [ ] Sound effects (keyboard typing, coffee machine, ping pong)
- [ ] Background office ambient sound
- [ ] Hover highlights and selection effects

**Effort:** 2-3 days  
**AI tools:** Three.js particle systems, Web Audio API — Claude can scaffold

### Total Estimated Timeline

| Phase | Duration | Cumulative |
|-------|----------|------------|
| Phase 0: Three.js PoC | 1-2 days | 1-2 days |
| Phase 1: Core Art | 3-5 days | 4-7 days |
| Phase 2: Grid + Placement | 2-3 days | 6-10 days |
| Phase 3: Characters + Anim | 3-5 days | 9-15 days |
| Phase 4: Amenities | 2-3 days | 11-18 days |
| Phase 5: UI | 2-3 days | 13-21 days |
| Phase 6: Polish | 2-3 days | 15-24 days |
| **Total** | **~3-5 weeks** | — |

### What AI Tools Handle vs What Needs a Human

| Task | AI-Assisted | Human Required |
|------|-------------|----------------|
| Three.js boilerplate + setup | ✅ 100% | — |
| Grid system + game logic | ✅ 90% | Review/debug |
| UI/HTML/CSS | ✅ 95% | Design taste |
| Pathfinding algorithms | ✅ 100% | — |
| Shader code | ✅ 80% | Tuning |
| MagicaVoxel models | ✅ Can guide | ✅ Must create manually |
| Blender rigging + animation | ⚠️ Can guide | ✅ Must do manually (or Mixamo) |
| Art direction / style consistency | ⚠️ Reference generation | ✅ Human eye needed |
| Sound design | ⚠️ Can find free assets | ✅ Selection/editing |

### Priority Order for Visual Upgrade

1. **Three.js rendering** (makes everything else possible)
2. **Floor + walls** (defines the space)
3. **Core furniture** (desk, chair, computer — most of the game happens here)
4. **Characters** (brings the office to life)
5. **Amenities** (the humor/personality layer)
6. **UI overlay** (makes it playable)
7. **Decorations + polish** (makes it lovable)

---

## Summary & Key Decisions

### Architecture
> **Three.js + OrthographicCamera + MeshToonMaterial** = isometric cartoon look in browser

### Art Pipeline
> **MagicaVoxel → Blender (optional rig) → GLTF → Three.js** = fastest consistent asset creation

### Fallback
> If Three.js complexity is too high: **PixiJS + pre-rendered MagicaVoxel sprites** (ISO export)

### MVP Scope
> ~55 unique visual assets, 5 character types, 4 animation states = **achievable in 3-5 weeks** with one developer using AI assistance for code

### Key Insight
> Going true 3D (Three.js) rather than faking isometric in 2D is *actually simpler* for a management sim. Depth sorting, multi-tile objects, lighting, and camera flexibility all come free. The "Two Point Hospital approach" works because it's genuinely 3D — we should follow the same pattern at browser scale.

---

*Research compiled by Legolas, 2025-02-25*  
*Sources: GitHub repos, dev.to articles, Reddit r/gamedev, Kenney.nl, itch.io, MagicaVoxel wiki, Steam store pages, MCV/Develop interviews*
