---
name: povray
description: "Write, fix, and render POV-Ray scenes (syntax 2.2-3.8)."
version: 1.0.1
author: ChrisL2000, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [povray, raytracer, 3d, scene-files, graphics]
    related_skills: []
---

# POV-Ray Skill

Complete reference corpus for the POV-Ray persistent-of-vision raytracer.
Use it whenever writing, fixing, or rendering POV-Ray scene files (`.pov`),
tuning render settings, or porting old scenes (2.2 → 3.x). It bundles clean
text of the official 3.7 documentation, the full 3.8 documentation, the
official 3.7/3.8 distribution scenes (800+), the POV-Ray wiki FAQ/HowTo
collection, the 3.8 changelog, and 2.2 legacy notes.

## When to Use

- User asks to create a POV-Ray scene / render an image with POV-Ray
- Fixing a scene that errors, looks wrong, or "disappears"
- Choosing camera, lighting, texture, pattern, or media settings
- POV-Ray command-line or INI render options
- Porting old (2.x) scenes to modern syntax, or using `#version` correctly
- Don't use for: Blender, Maya, Three.js, or other raytracers/engines

## File Map (references/)

| File | Use it for |
|---|---|
| `ref-syntax.md` | Scene file basics + all directives (#declare, #macro, #if, #while, splines, ...) — 3.7 |
| `ref-global-settings.md` | Global settings (Ambient_Light, Max_Trace_Level, Noise_generator, ...) |
| `ref-camera-lighting.md` | Camera (placement, projection, focal blur, perturbation), atmosphere, light types |
| `ref-objects.md` | All object primitives + CSG + object modifiers |
| `ref-textures.md` | Textures: pigment, normal, finish, interiors |
| `ref-patterns.md` | Patterns: marble, wood, checker, booleans, function patterns |
| `ref-media.md` | Media (fog, density) + include files |
| `ref-render-options.md` | All command-line/INI options (quality, size, threads, output) |
| `quickstart.md` | Getting-started tutorial (scene skeleton, first render) |
| `advanced-shapes.md` | Spline shapes: lathe, revolution, prism, sphere_sweep, bicubic_patch, text |
| `unix-notes.md` | POV-Ray on Unix: install, run, platform notes |
| `changelog-38.md` | What changed in 3.8 (mesh, mesh_camera, defaults, AA mode 3) |
| `faq.md` | Wiki FAQ: language Q&A, all HowTo articles, knowledgebase |
| `keyword-index.md` | Quick keyword meaning lookup |
| `legacy-notes.md` | POV-Ray 2.2 catalog + 2.2 → 3.x syntax deltas |
| `3.8/` | Full 3.8 docs: options, SDL, scene-file-basics, object-zoo, embellishments, special-effects, extras |

## Examples (examples/)

- `examples/3.7/` — official 3.7 distribution scenes by category: objects,
  textures/patterns/finishes, lights, camera, radiosity, subsurface, interior,
  bsp, gamma, language, animations, portfolio.
- `examples/3.8/` — same, plus new 3.8 scenes: `objects/torus2.pov`,
  `camera/user_defined.pov` (mesh_camera), `textures/patterns/aoi.pov`,
  `pavement.pov`, `potential.pov`, `output/dither_showcase.pov`,
  `output/gamma_showcase.pov`, `textures/finishes/finish_fresnel.pov`.
- `examples/2.2/` — classic POVDOC demo scenes (legacy syntax).
- `examples/INDEX.md` — every scene with its first comment line.

## Quick Scene Skeleton

```pov
#version 3.7
global_settings { assumed_gamma 1.0 }

camera {
  location <0, 5, -10>
  look_at <0, 2, 0>
  angle 40
}

light_source { <5, 10, -5> color rgb <1, 1, 1> }

background { color rgb 0.8 }

plane { y, 0
  texture { pigment { color rgb <0.2, 0.5, 0.2> } finish { specular 0.5 } }
}

sphere { <0, 1, 0>, 1
  texture {
    pigment { color rgb <0.9, 0.3, 0.2> }
    finish { specular 0.8 roughness 0.05 }
  }
}
```

## Render Commands

- Basic: `povray scene.pov` (reads INI defaults)
- One-liner: `povray +W640 +H480 +Q5 scene.pov`
- Threads: `povray +FT8 scene.pov`
- Output: `povray +Oout.ppm scene.pov` (also png, jpg, bmp, tga, 16-bit)
- INI file: `povray -Isettings.ini scene.pov`
- Quality: `+Q0..10` (AA + refinement), preview: `+P`; no file output: `+NF`
- Full option list: `references/ref-render-options.md` (3.7) /
  `references/3.8/3.8-options.md` (3.8)

## Refreshing the corpus (only if re-pulling sources)

- Official 3.x docs + distribution scenes: POV-Ray/povray GitHub repo, pinned per release
  tag (3.7.1, 3.8.0); 2.2 POVDOC from the classic 2.2 archive. Clone the tag, convert HTML
  to clean text, dedupe changed files across version trees.
- Wiki: live wiki.povray.org blocks plain curl (Cloudflare on the MediaWiki api.php) and
  the old povray.org/wikis/ path is dead (404). Re-pull via Wayback CDX `id_` raw
  snapshots, the way `references/faq.md` and `references/keyword-index.md` were.

## Pitfalls

- Put `#version 3.7` (or 3.8) at the top — without it, defaults are the
  old 2.2-era ones (gamma, ambient 0.1, right-vector 1.33).
- Old scenes look wrong under 3.1+ gamma: File_Gamma default is now 1.0;
  see `references/faq.md` (HowTo: fix old scenes) and `references/legacy-notes.md`.
- Texture inside object (2.2 style) vs `texture { object }` (3.1+ style) —
  both valid; mixing styles is a common source of "invisible object" bugs.
- `ambient` in a texture vs the global `Ambient_Light` setting — check both.
- CSG `hierarchy on/off` changes shadows/reflections, not shape.
- `smooth_triangle` needs normals at all three corners or edges look faceted.
- 3.8 `mesh` triangles can carry per-triangle `texture` / `texture_list`;
  requires `#version 3.8`.
- `+P` preview renders at low quality to the display only (no file unless +O).

## Verification

Render with a small size first (`povray +W128 +H96 +Q1 scene.pov`) and check
the exit message for errors/warnings; then a full-quality render and confirm
the output file exists with non-zero size.
