# Facet

A software 3D rasteriser. No WebGL and no GPU — matrices, clipping, a z-buffer
and perspective-correct interpolation, every pixel written into an ImageData
buffer by JavaScript.

Live: https://bxzex.github.io/facet/

## The pipeline, in order

1. **Transform.** Model, view and projection matrices built by hand, composed
   into one MVP. Vertices carry a position, a normal and a UV.
2. **Near plane clipping.** A triangle with any vertex behind the eye cannot
   simply be divided by w — the result wraps around and smears across the
   screen. Each triangle is clipped against `w > ε` in clip space first and the
   resulting polygon re-triangulated as a fan. Fly the camera inside the sphere
   and the counter shows several hundred triangles going through the clipper on
   every frame.
3. **Perspective divide and viewport transform** to screen coordinates.
4. **Backface culling** from the sign of the signed area in screen space. Turn
   it off and the fragment count rises as the inside surfaces start drawing.
5. **Rasterisation** with edge functions, which give the barycentric weights
   directly. Attributes are interpolated as `attribute/w` and divided by the
   interpolated `1/w`, which is what keeps a checker texture from swimming as
   the surface turns away.
6. **Depth test** against a Float32 z-buffer, with the rejection count reported.

Shading modes: Phong with a Blinn half vector, Lambert, quantised flat, normals
as colour, and the raw depth buffer. All five meshes are generated — sphere,
torus, cube, a noise terrain and a lathe-turned vase — so there is no model file
anywhere in the repository.

## Verification

Each pipeline stage is checked by its own counters. Backface culling removes
roughly half of a closed mesh and switching it off raises the fragment count.
The depth test's rejection counter drops to zero when the test is disabled.
Pushing the camera inside the sphere sends 634 triangles through the near
clipper. That test is also how I found a real bug: the terrain grid was wound
the opposite way round from the swept meshes, so 84% of it was being culled and
the surface was barely drawing. Fixed, it culls 16% and draws 70,265 fragments
where it drew 4,482.

## Notes

One HTML file. No libraries, no build step, no GPU. Drag to orbit, scroll to
dolly, shift and drag to move the light.

Built by [bxzex](https://bxzex.com).
