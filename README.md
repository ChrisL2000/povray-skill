# povray-skill

A [Hermes Agent](https://github.com/NousResearch/hermes-agent) skill that
gives an AI agent a complete, deduplicated, plain-text reference corpus for
the POV-Ray raytracer — everything needed to write, debug, and render
POV-Ray scenes from memory of the docs, without web access.

## Contents

```
SKILL.md              skill definition (when to use, file map, quick scene, pitfalls)
references/           clean text from the official POV-Ray 3.7 documentation
  ref-syntax.md           scene file basics + all directives
  ref-global-settings.md  global settings
  ref-camera-lighting.md  camera, atmosphere, light types
  ref-objects.md          primitives, CSG, object modifiers
  ref-textures.md         pigments, normals, finishes, interiors
  ref-patterns.md         patterns and pattern booleans
  ref-media.md            media + include files
  ref-render-options.md   all command-line / INI options
  quickstart.md           getting-started tutorial
  advanced-shapes.md      spline-based shapes (lathe, prism, text, ...)
  unix-notes.md           POV-Ray for Unix
  changelog-38.md         3.7 -> 3.8 changes (mesh, mesh_camera, defaults)
  faq.md                  wiki FAQ + HowTo articles + knowledgebase
  keyword-index.md        quick keyword lookup
  legacy-notes.md         POV-Ray 2.2 catalog + 2.2 -> 3.x syntax deltas
  3.8/                    full 3.8 documentation (7 files)
examples/           official distribution scenes
  3.7/                396 scenes (objects, textures, lights, camera, radiosity, ...)
  3.8/                403 scenes (+8 new 3.8 scenes incl. mesh_camera demo)
  2.2/                18 classic POVDOC demo scenes
  INDEX.md          every scene + its first comment line
```

## Install (Hermes Agent)

Copy (or clone) this repo into your skills tree:

```sh
git clone https://github.com/ChrisL2000/povray-skill \
  ~/.hermes/skills/creative/povray
```

or for a repo-relative install:

```sh
git clone https://github.com/ChrisL2000/povray-skill skills/creative/povray
```

The skill loads under the `creative` category. No extra dependencies.

## Sources (all public)

- POV-Ray 3.7 Unix documentation — povray.org (3.7.1), converted HTML → text
- POV-Ray 3.8 source repository (POV-Ray/povray, tag 3.8.0) — docs + scenes
  + changes.txt (AGPL-3.0)
- POV-Ray 2.2 POVDOC archive (POVLEGAL.DOC applies to the classic files)
- POV-Ray wiki (wiki.povray.org) — FAQ/HowTo/keyword pages, cached copies
  from the Wayback Machine crawls of 2025-06-14 and 2026-04-11..15
  (the wiki is a MediaWiki instance; live API is Cloudflare-protected)

## License

Skill text and structure: MIT.
Bundled documentation excerpts and scenes: (c) the respective POV-Ray
authors/teams, redistributed for reference use under the POV-Ray license
terms / AGPL-3.0 of the source repositories — see the source repos for
exact terms.
