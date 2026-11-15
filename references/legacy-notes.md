# POV-Ray 2.2 Legacy Notes

Source: POV-Ray 2.2 POVDOC archive (doc/POVRAY.DOC, TEXTURES.DOC, INCLUDE/*, DEMO/*) —
the classic scene language still used by thousands of old scene files.

## What 2.2 supported (from the POVDOC catalog)
- 20 object types: 7 solid finite primitives (blob, box, cone, cylinder,
  height_field, sphere, torus), 4 finite patch primitives (isosurface, polygon,
  prism, lathe/revolution-type), 5 infinite polynomial primitives (plane, quadric,
  cubic, quartic, polynomial), 3 CSG types (union, intersection, difference),
  plus light_source. (superellipsoid and text were added in 2.1 and are listed
  alongside in the 2.2 docs.)
- Texture = pigment + normal + finish, declared *inside* each object.
- Includes: COLORS.INC, SHAPES.INC, SHAPES2.INC, SHAPESQ.INC, CHARS.INC,
  FOV.INC, IOR.INC, STONES.INC, TEXTURES.INC (13 files total in INCLUDE/).
- Demo scenes in DEMO/: AREALIT1-3, CHARS, COLORS, NORMAL, PIGMENT,
  PRIMITIV, SHAPES, SHAPES2, TEXTURE1-6 (copied to examples/2.2/).

## Key 2.2 -> 3.x syntax deltas (when fixing old scenes)
- `#version 2.2` (or none) keeps old behaviour; 3.x still parses scenes as old
  as POV-Ray 1.0, so old files usually render with zero edits.
- Colors: 2.2 used bare floats 0..1; 3.0 added `rgb()`, `rgbft()`, `color`
  functions, `color_map`, function patterns. Old `color <r,g,b>` in textures
  still works; `rgbft` alpha channel is 3.0+.
- Gamma: 2.2 assumed file gamma 0.4545 (1/2.2). 3.1+ new gamma system uses
  File_Gamma = 1.0 default — old scenes look darker/washed; see wiki HowTo
  "Fix old scenes to work with the new gamma system" (in references/faq.md).
- `sky_sphere` (2.2) vs `background` (3.1+); both accepted.
- `ambient` default was 0.1 (2.2/3.7); 3.8 defaults it to 0.0 (needs #version 3.8).
- Camera: `camera { location ... look_at ... }` unchanged; 3.1+ added `right`,
  `up`, `direction` forms, orthographic, projection controls, focal blur.
- Textures: 3.1 added `texture { object }` (object texture modifier),
  `interior {}`, layered `texture { texture { ... } }`, `texture_list` for
  triangle smoothing; 3.7 added cutaway textures.
- CSG: `hierarchy on/off` and CSG modifiers available since 2.2 (behavior
  refinements in 3.x).
- Object modifiers `no_shadow`, `no_reflect`, `no_image`, `double_illuminate`
  present in 2.2 (renamed forms `no_shadows` etc. accepted in 3.x).
- Radiosity, photons: 2.2 had photons (`+PT`); radiosity came in 3.5.
- For the full 3.7 scene language rules, see references/ref-syntax.md
  (section 3.3). For 3.8, references/3.8/3.8-sdl.md.
