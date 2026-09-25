---
name: threejs-product-basecolor
description: Rebuild UVs and bake a compact Base Color or Diffuse atlas from the actual source colors of a Blender product model for Three.js. Use when geometry is final and the output texture must preserve unlit product color without direct light, indirect light, world light, shadow, AO, specular, or highlights.
---

# Three.js Product Base Color

Create clean final UVs and an unlit color atlas after geometry is approved. Preserve the colors that the current source materials and Diffuse or Base Color images actually provide.

## Protect the source

Never overwrite the source blend or source images. Save a new working file. Inventory source materials, image textures, color spaces, UV maps, procedural nodes, object colors, alpha use, and linked mesh data.

Determine where each visible color comes from. Do not replace a real source texture with sampled average colors or material guesses unless the user explicitly asks. If the source texture already contains shadows or lighting, report that condition; do not silently claim the result is physically unlit.

## Keep source and target UVs explicit

Preserve the source color UV as a named map such as `UV_SourceColor`. Create a separate final map such as `UV_BaseColor_Final`.

In every bake-source material:

- Drive the source Image Texture through an explicit UV Map node that references `UV_SourceColor`.
- Add or select the target atlas image node for baking.
- Make `UV_BaseColor_Final` the target active-render UV.

Do not rely on whichever UV map happens to be active. This prevents the source texture from changing when the target UV is created.

## Build the final UV

Unwrap only after topology is stable. Use seams that minimize distortion on visible areas and hide cuts on undersides or hard edges when practical.

- Keep all islands inside 0–1 unless the export design deliberately uses another convention.
- Avoid unintended overlaps, zero-area UVs, flipped islands, and extreme stretch.
- Use consistent texel density based on visible importance.
- Let identical repeated parts share the same UV region when their appearance is identical.
- Prefer one atlas when it provides adequate density; use a small number of atlases when one would compromise visible detail.

Start at 1024×1024 for modest web products. Move to 2048×2048 when close inspection, labels, narrow islands, or high color contrast requires it. Choose by test output rather than habit.

## Prevent color bleeding

Island spacing must be measured in final texture pixels. Leave at least 4–8 pixels between unrelated colors at the delivered resolution, and more when several mip levels or strong contrast are expected.

- Set bake margin or dilation large enough to fill gutters.
- Do not place yellow, black, glass, or other high-contrast islands across subpixel gaps.
- Bake at final resolution or above; downsample with color-aware filtering when useful.
- Test the exported texture with linear filtering and mipmaps, because a clean Blender image may still bleed in Three.js.
- Increase padding or repack problem islands before increasing resolution alone.

## Bake color only

Use Cycles Diffuse bake with:

- Color: ON
- Direct: OFF
- Indirect: OFF

Do not include world light, lamps, shadows, AO, glossy/specular response, reflections, highlights, or color temperature. The atlas represents product color, not a rendered view.

For procedural or vertex-color sources, construct a source shader that reproduces the intended Base Color and verify it visually before baking. Bake one object or logical group at a time when that makes source-to-target selection reliable.

## Build glTF materials

Use simple Principled BSDF materials compatible with glTF 2.0. Connect the atlas to Base Color with the correct color space and alpha mode. Avoid Blender-only node networks in the export material.

Retain distinct materials only when glTF behavior differs, such as opaque paint versus transparent glass. Reuse one atlas across those materials when possible.

## Validate

Compare the source and baked model under an unlit or neutral diagnostic setup from all principal views. Check logos, warnings, glass, tires, dark parts, island borders, seams, and mipmapped distance views.

Validate the exported GLB with Three.js `GLTFLoader` and confirm:

- Correct UV set and texture assignment.
- No missing, overwritten, or unsaved images.
- No unintended overlap or color bleed.
- Correct color space, alpha behavior, scale, hierarchy, and material count.

Report atlas resolution and count, UV reuse, bake margin, Color/Direct/Indirect flags, source-image preservation, GLB size, and any residual color or glTF risk.
