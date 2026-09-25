---
name: threejs-product-lowpoly
description: Optimize product models in Blender into clean, silhouette-preserving low-poly assets for real-time Three.js or WebGL display. Use for geometry audits, CAD defeaturing, topology simplification, repeated-part rebuilding, object cleanup, hard-surface shading, triangle reduction, or web-ready GLB preparation. Do not use this skill for final UV or texture baking while topology is still changing.
---

# Three.js Product Low-poly

Turn a CAD, scan, or high-poly product model into a compact real-time asset. Preserve product proportion, silhouette, macro curvature, and recognizable functional parts. Remove manufacturing detail that has no visible value at the intended viewing distance.

## Establish the target

Before editing, record:

- Intended viewing distance, maximum zoom, animation needs, and target devices.
- Triangle, object, material, texture, and download-size budgets.
- Parts that define product identity or must remain movable.
- Required checkpoints and approval gates.

Treat budgets as constraints rather than reasons to damage the silhouette. For a single desktop/mobile web product, 20,000–60,000 triangles is a useful starting range, but set the real target from screen coverage and device performance.

Never overwrite the source file. Save a working copy and keep the source available as geometry, material, UV, and texture reference.

## Audit before editing

Use Blender MCP or the available Blender automation interface to inspect the open file. Record:

- File path and size.
- Object and mesh-object counts.
- Vertices, edges, faces, and evaluated triangles.
- Materials, image textures, modifiers, linked data, hidden objects, and collections.
- Triangle ranking by object and logical assembly.
- Dimensions, units, transforms, origins, pivots, and ground height.

Inspect the model from front, rear, left, right, top, front three-quarter, and rear three-quarter views. Locate:

- Hidden or enclosed geometry.
- Bolts, washers, welds, hoses, internal parts, and other micro detail.
- Small holes, counterbores, slots, recesses, support loops, radial edges, and triangle fans.
- Coplanar CAD tessellation and long thin triangles.
- Excessively segmented cylinders, tubes, wheels, and fillets.
- Simple shapes whose topology is more expensive to repair than rebuild.
- Repeated parts that can share mesh data.

Save the audit before modifying geometry so reductions can be measured accurately.

## Simplify in this order

1. Delete fully hidden parts and detail invisible at the target viewing distance.
2. Suppress low-value CAD features and rebuild the surrounding macro surface.
3. Dissolve coplanar tessellation and redundant support edges.
4. Rebuild simple parts from low-sided primitives.
5. Replace repeated complex parts with one or a few masters and linked instances.
6. Apply local decimation only to secondary curved meshes that remain expensive.
7. Consolidate objects and materials where independent movement is unnecessary.

Never apply one global Decimate ratio to the whole product.

## Perform complete defeaturing

When removing a small hole, slot, recess, or counterbore, remove the complete feature:

- Delete its walls, bottom, bevel, circular boundary, and supporting loops.
- Remove radial edges, triangle fans, and long thin triangles caused by the feature.
- Dissolve the remaining feature-shaped topology.
- Rebuild the large surrounding plane or curve with the minimum useful faces.

The finished surface should look as though the feature never existed. A filled opening with its circular vertex loop and fan still present is not complete defeaturing.

Use a large n-gon on a truly planar region when it triangulates safely. Use a few quads or triangles for folded surfaces. On curved covers or tubes, reconstruct the macro curvature with sparse regular loops rather than inserting a flat patch.

Keep openings that affect silhouette, product identity, articulation, or an obvious mechanical connection. Reduce their circumference segments to the lowest visually acceptable count.

## Rebuild simple and repeated parts

Prefer reconstruction for tires, rims, pins, shafts, cylinders, rods, guards, plates, brackets, boxes, and tubes when the source mesh is dense or irregular.

Match source dimensions, position, orientation, proportions, connection points, and pivot. Suggested starting values are:

- Small pins or shafts: 6–8 sides.
- Ordinary cylinders: 8–12 sides.
- Large prominent cylinders: 12–16 sides.
- Prominent wheels or circular silhouettes: 12–24 segments according to screen size.

Avoid geometric tread, knurling, tiny grooves, and manufacturing bevels unless they remain visible and identify the product. Express such detail in color texture when useful.

For repeated identical parts, use linked duplicates or shared mesh data. Create separate masters only when dimensions or silhouette differ. Preserve separate objects for wheels, doors, arms, tools, controls, or other parts that may animate or be selected independently.

## Preserve silhouette and shading

Check every destructive change from multiple views. Protect:

- Overall proportions and wheelbase.
- Large housings, frame lines, cabins, panels, and working tools.
- Major openings, articulation, and visible hydraulic structures.
- Macro curvature, thickness, and end profiles of recognizable parts.

After geometry cleanup:

- Recalculate normals and inspect face orientation.
- Use smooth shading by angle, sharp edges, and weighted-normal techniques where appropriate.
- Keep large planes visually flat and hard transitions crisp.
- Fix shading with normals and topology first; do not restore dense geometry merely to hide artifacts.
- Apply transforms where safe, preserve meaningful origins, and keep scale consistent.

## Verify and checkpoint

Recount evaluated triangles and objects. Compare source and low-poly from the seven audit views with the same framing. Inspect silhouette, dimensions, intersections, open boundaries, non-manifold regions, flipped normals, shading artifacts, and animation pivots.

Save a geometry checkpoint before any final UV or bake work if topology may still change. If the user requested geometry approval, stop at that checkpoint and wait. Do not spend time repairing final UVs before geometry is accepted.

Report:

- Source and result counts and file sizes.
- Triangle reduction overall and for the most expensive assemblies.
- Deleted internal structures and suppressed feature classes.
- Rebuilt, dissolved, decimated, joined, and shared-mesh objects.
- Preserved identity features and any visible compromises.
- Remaining topology, shading, hierarchy, or glTF risks.
