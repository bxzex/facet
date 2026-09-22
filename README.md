# Facet

A 3D renderer that runs entirely on the CPU. Every pixel is written into an ImageData buffer by JavaScript, with no WebGL involved.

https://bxzex.github.io/facet/

It has the whole pipeline: matrices, near plane clipping, backface culling, edge function rasterisation, perspective-correct texturing and a z-buffer. There are five shading modes, and the counters show each stage doing its job.

Those counters caught a real bug. The terrain mesh was wound the wrong way, so 84% of it was being culled away. After the fix, 16% is culled and it draws about 70k fragments instead of 4k.

Drag to orbit, scroll to zoom, shift-drag to move the light.
