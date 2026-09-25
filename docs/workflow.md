# Blender to Three.js Product Asset Workflow

This document describes how the four skills work as one staged pipeline:

```text
High-poly
→ Geometry Optimization
→ Geometry Checkpoint
→ UV / Base Color
→ Lighting
→ Runtime Lighting OR Light Bake
→ glTF / Three.js Delivery
```

## Core rule

> Geometry first, UV second, lighting and baking last.

Do not create final UVs or final baked textures while geometry is still expected to change.

Geometry edits can delete faces, replace objects, remove holes, dissolve CAD tessellation, change seams, join parts, split animation components, or rebuild repeated elements. Any of these changes can invalidate final UV islands and baked pixels. Premature UV or bake work therefore causes:

- broken or outdated UV maps after geometry revisions
- repeated unwrap and bake operations
- textures that no longer match the approved geometry checkpoint
- inconsistent source and target assets
- higher revision cost and a greater chance of export defects

## Stage 1 — Audit and geometry optimization

Invoke `$threejs-product-lowpoly` on a protected copy of the source model.

The geometry audit records object and mesh counts, evaluated triangles, materials, textures, modifiers, transforms, hierarchy, pivots, dimensions, hidden geometry, and the most expensive assemblies. The optimization then prioritizes:

1. Removing fully hidden or visually irrelevant structures.
2. Suppressing small CAD features and rebuilding the surrounding surface.
3. Dissolving coplanar tessellation and redundant support edges.
4. Rebuilding simple mechanical parts with low-sided primitives.
5. Sharing mesh data across repeated identical parts.
6. Applying local Decimate only to suitable secondary curved meshes.
7. Consolidating objects while preserving parts that must move or remain selectable.

Small holes and recesses require complete defeaturing. Removing only the visible opening while retaining the circular boundary, supporting loops, radial edges, or triangle fan does not provide a clean result.

## Stage 2 — Geometry checkpoint

Save a dedicated checkpoint and compare it with the source from front, rear, left, right, top, and three-quarter views. Confirm:

- product proportions and dimensions
- silhouette and major curvature
- recognizable housings, tools, frames, wheels, and articulation
- mechanical relationships and intersections
- object hierarchy, origins, and animation pivots
- normals, hard edges, planar shading, and round-part smoothness
- triangle and object budgets

Do not proceed to final UV work until the geometry is accepted.

## Stage 3 — Final UV and Base Color

Invoke `$threejs-product-basecolor` after the geometry checkpoint is stable.

Preserve the source color UV explicitly and create a separate target UV. Source Image Texture nodes must continue to read the source UV while the target atlas uses the new UV. Pack islands with useful texel density and enough pixel padding for filtering and mipmaps.

The Base Color bake uses:

```text
Diffuse Bake
Color:    ON
Direct:   OFF
Indirect: OFF
```

This produces product color without scene lighting, shadow, AO, specular, reflection, or environment illumination.

## Stage 4 — Neutral multi-angle lighting

Invoke `$threejs-product-lighting` to establish the approved product-lighting reference.

The rig scales from the product bounding box and is evaluated from all intended orbit angles. Large, soft sources and restrained world fill should keep the model readable without flattening its form or clipping bright materials.

Because Blender Area lights do not transfer as equivalent area lights through standard glTF, document the intended Three.js environment, directional lights, tone mapping, output color space, and exposure.

## Stage 5A — Runtime lighting delivery

Choose runtime lighting when the model, light, camera, or assembly can change.

```text
Low-poly geometry
+ Base Color atlas
+ PBR-compatible materials
+ Three.js runtime lighting
→ glTF / GLB
```

This route supports moving components, free orbit, changing light direction, and view-dependent material response. Validate the result in the actual Three.js viewer because Blender and Three.js lighting are not pixel-identical.

## Stage 5B — Pre-baked lighting delivery

Invoke `$threejs-product-light-bake` when stable appearance and low runtime lighting cost are more important than dynamic response.

```text
Diffuse Bake
Color:    ON
Direct:   ON
Indirect: ON
```

The resulting atlas contains fixed diffuse illumination and shadows. It is appropriate for an Unlit glTF material or Three.js `MeshBasicMaterial` when exact baked appearance is required. If a PBR material remains in use, keep runtime lighting deliberately weak and test for double lighting.

Repeated parts need an explicit decision. Shared UVs are efficient but cannot represent different lighting for each instance. Unique per-instance shadows require unique mesh/UV data and more atlas space.

## Final delivery checks

Before publishing a GLB, verify:

- The source files remain unchanged.
- Geometry, UV, material, and texture versions match the same checkpoint.
- No hidden high-poly source, backup mesh, camera, or helper is exported unintentionally.
- UVs have no unintended overlap, zero-area islands, or out-of-range coordinates.
- Atlas borders remain clean with linear filtering and mipmaps.
- Texture color space and alpha modes are correct.
- Materials use glTF-compatible nodes and behaviors.
- Scale, orientation, hierarchy, origins, and animation pivots are correct.
- `GLTFLoader` loads the GLB without missing images or invalid attributes.
- The viewer uses either the documented runtime-lighting setup or the documented pre-lit strategy.
- Download size, texture memory, draw calls, object count, and triangles fit the target device.
