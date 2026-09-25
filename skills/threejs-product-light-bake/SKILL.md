---
name: threejs-product-light-bake
description: Rebuild UVs and bake a product model's actual Base Color together with diffuse direct and indirect scene lighting for a lightweight Three.js presentation. Use when lighting and shadows must be embedded into a texture for stable visual depth, especially with unlit or very weakly lit runtime materials.
---

# Three.js Product Light Bake

Create a light-baked atlas from the current product color plus the current Blender scene's diffuse direct and indirect lighting. The result is view-independent diffuse illumination with fixed shadows. It does not include specular highlights or reflections.

## Confirm the intended runtime

Before baking, establish whether the GLB will use:

- An unlit material or Three.js `MeshBasicMaterial` for an exact baked appearance.
- A Principled/PBR material with deliberately weak real-time light for limited interactive response.

Avoid full baked lighting plus a normal-strength real-time rig because it multiplies lighting and creates an overbright, double-lit result. Three.js does not add a universal default light automatically; the viewer must define any runtime lights.

Fixed baked shadows will not respond to moving lights, moving parts, or changed assembly poses. Do not use this workflow when physically responsive lighting is required.

## Preserve source color

Never overwrite the source blend or source images. Save a new working file and record hashes or file metadata for source textures when practical.

Keep the original color UV as `UV_SourceColor` and create a separate target map such as `UV_Lit_Final`. Explicitly drive source Image Texture nodes with `UV_SourceColor`; make the new UV active only for the bake target. Verify the source image and its appearance remain unchanged after the target UV is built.

Use the actual Base Color or Diffuse image and material color as the bake source. Do not replace it with approximate solid colors.

## Decide how repeated geometry receives light

Shared mesh data and overlapping UVs are efficient, but different instances may receive different shadows and illumination.

- Preserve shared UVs when repeated parts have equivalent lighting or performance matters more than unique shadows.
- Make geometry and UVs single-user per instance when each instance needs its own baked illumination.

State this tradeoff in the report. Never silently bake several differently lit instances onto one overlapping region.

## Create the target UV and atlas

Unwrap only final geometry. Keep islands inside 0–1, avoid unintended overlaps and degenerate islands, maintain useful texel density, and place seams away from prominent surfaces.

Use one atlas when practical. Start with 1024×1024 and move to 2048×2048 when close views, labels, narrow islands, hard shadow boundaries, or high-contrast color borders need it.

Leave at least 4–8 final pixels between unrelated islands, with more padding for several mip levels. Use bake margin/dilation to fill gutters and test the final exported texture with mipmaps. Repack islands before assuming resolution alone will cure bleeding.

## Bake diffuse color and lighting

Use Cycles Diffuse bake with:

- Color: ON
- Direct: ON
- Indirect: ON

Use the current approved scene lights and world. Configure enough samples and diffuse bounces for stable indirect illumination. Keep glossy/specular, normal, reflection, and emission contributions out of the requested diffuse result. Include AO only when explicitly requested; it is not a substitute for indirect light.

Prefer a floating-point linear master image such as OpenEXR when the lighting can exceed display range. Produce the delivery PNG, JPEG, or WebP using the agreed exposure and display transform. Record whether tone mapping or the Blender display transform was baked into the delivered pixels.

Do not bake transient overlays, selection outlines, wire display, cameras, helpers, or viewport-only effects.

## Build the runtime material

For the most faithful baked presentation, export or construct an unlit glTF material and use the baked atlas as its color texture. When the required pipeline needs Principled/PBR:

- Keep Base Color at white so it does not tint the baked pixels again.
- Use high roughness and low metallic unless the product requirements specify otherwise.
- Keep runtime light intensity low and validate for double lighting.

Glass, emissive displays, and strongly metallic surfaces may need separate materials because a diffuse light bake cannot reproduce their view-dependent behavior.

## Validate in Blender and Three.js

View the target under an unlit diagnostic setup to see only the baked pixels. Compare front, rear, left, right, top, and three-quarter views with the approved lit reference.

Check:

- Base colors remain recognizable and source images are unchanged.
- Direct and indirect gradients are present without clipped hot spots.
- Shadows are clean and appropriate for the fixed presentation.
- No UV overlap, color bleed, dark seams, missing islands, or noisy indirect light.
- Moving parts do not reveal implausible frozen shadows.
- The GLB loads with `GLTFLoader`, uses the intended UV/material mode, has finite attributes, and preserves scale and hierarchy.

Report source and target UV names, atlas resolution/count, margin, Cycles samples/bounces, Color/Direct/Indirect state, light/world setup, repeated-part policy, tone mapping, runtime material, GLB size, and all double-lighting or motion risks.
