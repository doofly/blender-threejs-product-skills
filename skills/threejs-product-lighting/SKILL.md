---
name: threejs-product-lighting
description: Create balanced all-angle product lighting in Blender and a matching Three.js lighting recipe. Use when a real-time product viewer needs readable form from every orbit angle, controlled highlights, soft shadows, stable exposure, and no blown-out surfaces while preserving the existing geometry and Base Color.
---

# Three.js Product Lighting

Build a neutral product-lighting rig that keeps form readable from all orbit angles. Preserve geometry, UVs, and Base Color. This workflow prepares real-time lighting; it does not bake lighting into the texture.

## Inspect before lighting

Save a new working copy. Record model bounds, units, origin, ground height, material roughness/metallic values, world settings, render engine, color management, and current exposure.

Remove or disable accidental test lights only after recording them. Do not compensate for incorrect material values or normals by adding extreme light energy.

## Build a scale-aware rig

Derive light size, distance, and energy from the model bounding box rather than fixed scene units. Start with a neutral, broad rig:

- Large key Area light above and in front.
- Softer fill from the opposite side.
- Rear and side lights to keep dark silhouettes readable while orbiting.
- Broad top light and weak lower fill when the underside contains important structure.
- Low-strength neutral world fill for shadow control.

For fully orbitable products, six to eight large Area lights arranged around the model are often easier to balance than one strong key. Use wide emitters for soft transitions. Keep colors neutral unless brand or environment requirements specify otherwise.

Aim for even readability rather than identical luminance on every face. Preserve enough gradient to show curvature and depth. Reduce individual light energy and enlarge sources before adding exposure compensation when highlights clip.

## Control tone and materials

Use the project's intended Blender view transform, such as AgX, and record it. Calibrate exposure after the rig is balanced. Check white or yellow painted surfaces, glass, polished metals, black rubber, and cavities separately.

- Avoid clipped highlights and crushed dark parts.
- Keep roughness realistic enough to reveal shape without producing moving hot spots.
- Use contact shadows sparingly and consistently.
- Keep the background and ground neutral unless the product context requires otherwise.

## Evaluate every orbit angle

Use the same camera lens, exposure, background, and framing for front, rear, left, right, top, front three-quarter, and rear three-quarter checks. Also inspect the lowest and highest viewer elevations allowed by the application.

Look for:

- Large areas that become unreadably dark.
- Washed-out paint or clipped highlights.
- Flat-looking surfaces with no depth cue.
- Distracting shadow bands, duplicated shadows, or reflections.
- Transparent parts that disappear against the background.

Adjust the smallest number of lights needed to correct each issue, then repeat all views because every change affects the orbit.

## Prepare the Three.js equivalent

Blender Area lights do not transfer as area lights through standard glTF. glTF punctual-light export covers point, spot, and directional lights, and many product viewers intentionally provide their own rig.

Document a Three.js recipe that matches the Blender intent, typically:

- A low-intensity `HemisphereLight` or neutral environment for broad fill.
- One soft directional key.
- One weaker directional fill or rim from the opposite side.
- The same output color space, tone mapping, and exposure as closely as practical.

If the viewer supports environment maps, prefer a neutral studio environment with controlled intensity. Three.js has no universal default scene light, so include the required viewer light setup in the handoff rather than assuming the GLB will look identical by itself.

Keep Blender lights in a clearly named collection and retain a preview camera when useful. Export lights only when the application explicitly consumes them.

## Deliver and report

Save the lit Blender scene separately. Provide comparison renders or viewport captures from the standard views and report:

- Light types, positions relative to the bounds, sizes, colors, and energies.
- World strength, view transform, exposure, and background.
- Three.js light/environment and tone-mapping settings.
- Known differences between Blender and Three.js.
- Any angles, materials, or devices that remain at risk.
