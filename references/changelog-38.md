# POV-Ray 3.8 Changelog & What's New vs 3.7

Source: official povray 3.8.0 repository (changes.txt, v3.7.1-rc.1 → v3.8.0-beta.1 digest;
the v3.8.0 release line was redesignated from v3.7.1). Scene language highlights verified
against the 3.8 reference docs (see 3.8/*.md next to this file).

## Headline changes
- New `#version 3.8` directive gates the new behaviour (and some defaults).
  `#version 3.71` now warns.
- New primitive: `mesh` (efficient triangle/supershaped-triangle meshes with
  per-triangle texture, uv_vectors, hierarchy). Also `mesh2` and `polygon`
  patch primitives, `mesh_camera` (camera projected through a mesh), `lemon`
  primitive, extended `ovus`.
- Default changes under #version 3.8: pigment defaults to white, `ambient`
  defaults to 0.0, camera `right` vector defaults to image aspect ratio.
- AA mode 3 (generic oversampling: moire suppression, jittered-light/SSS noise).
- Stochastic rendering improvements; new `stochastic_seed` option.
- Multi-threading, API and VFE updates (see Performance/Development sections).

## Changed Behaviour
- Any new behaviour formerly activated by `#version 3.71` now requires
    `#version 3.8` (or higher); specifying `#version 3.71` will trigger a
    corresponding warning.
  - Some defaults have been changed (requires `#version 3.8` or a corresponding
    command line / INI setting):
      - The pigment now defaults to plain white.
      - `ambient` now defaults to 0.0 instead of 0.1.
      - The camera `right` vector length now defaults to the output image aspect
        ratio (presuming square pixels) instead of 1.33.
    Switching back and forth between defaults via `#version` is possible until
    the first `default` statement is encountered; after that, `#version` will
    cause a warning instead of changing defaults.
  - Minor changes have been made to the benchmark scene. New benchmark version
    is 2.03.
  - Token counting in conditional blocks (e.g. in `#if ... #end`) has changed.
    Whenever such a block is skipped, the token count is now incremented only
    by directives (stuff that begins with `#`).
  - UV mapping of cylinder, cone and lemon primitives has been disabled again
    for now, due to their orientation being poorly defined.
  - An age-old bug in the inbuilt `f_enneper` isosurface function has been
    fixed; the function now results in the originally intended shape.
  - Contrary to earlier claims and intentions, v3.7.1-beta.1 failed to lift the
    requirement that array elements must be of the same type. It has been
    decided to not fix the change to work as originally intended, and instead
    only allow type mixing if the array has explicitly been declared as
    `mixed`. See the documentation for details.
  - The dithering implementation has been modified, and may produce slightly
    different results for otherwise identical scenes.
  - The PGM (greyscale variant of PPM) output gamma handling now matches that
    of regular PPM, honoring `File_Gamma` defaulting to ITU-R BT.709. To get
    linear greyscale output, explicitly specify `File_Gamma=1.0`.
  - Greyscale output no longer automatically forces bit depth to 16 bpc. To get
    16 bpp greyscale output, explicitly specify `Bits_Per_Color=16`.
  - Preview now reflects greyscale setting.
  - POV-Ray will now warn when using v3.7 or v3.8 features without matching
    `#version` directive.
  - For consistency, POV-Ray will no longer throw an error whenever
    encountering anything macro-related in v3.0 scenes, and instead just
    warn when encountering `#macro` in such scenes.

## New Features
- The `ovus` primitive has been extended in multiple ways. See the
    documentation (currently being maintained on <http://wiki.povray.org>) for
    details.
  - Anti-aliasing now supports a new mode 3, which can actually serve as a
    generic oversampling mechanism not only suited to perform edge
    anti-aliasing, but also to successfully suppress moire patterns as well as
    reduce image noise from stochastic mechanisms (e.g. jittered area lights,
    subsurface light transport or micronormals). The mathematical background
    and parameterization is similar to that of adaptive focal blur.
  - The `bicubic_patch` primitive now allows for a trailing comma at the end of
    the list of control points.
  - The `matrix` syntax now allows allows for a trailing comma at the end of
    the list of coefficients.
  - File formats supporting variable bit depths (PNG and PPM/PGM) now allow for
    bit depths as low as 1 bit per colour channel. (1-bit greyscale PPM/PGM will
    still be written as PGM, not PBM.)
  - Command-line option `+F` now allows specifying both the `G` greyscale flag
    and the bit depth.
  - Support for blue noise dithering has been added, plus a couple more error
    diffusion dithering filters.

## Performance Improvements
- Significantly improved parsing speed of skipped conditional blocks (e.g. in
    `#if(false) ... #end`), especially for blocks containing few directives
    (stuff that begins with `#`).

## Fixed or Mitigated Bugs (3.8.0-beta.1)
Reported via GitHub:

  - #65 ("Normal for the prism's bezier spline mode is reversed from other
    spline types & lathe uses.")
  - #292 ("Port of FS227 - Fixed Vector Limitations")
  - #317 ("Boost 1.65.0 incompatibility")
  - #319 ("Need -lboost_date_time in non-optimized Unix builds")
  - #341 ("macOS build failure for 3.7.0.5", configure script failing to use
    `-lboost_system` with Boost 1.66)
  - #342 ("AX_FIX_INCORRECT_PATH is broken", configure script failing to
    properly handle `.` directory in `C_INCLUDE_PATH` or `CPLUS_INCLUDE_PATH`)
  - #403 ("povray 3.7.0.8 does not build on macOS 11.0 Big Sur").

Reported via the Newsgroups:

  - <web.59fefd5f68f4d962ca73ee9d0@news.povray.org>
    (2017-11-05, povray.newusers, "orthographic camera and conic_sweep object")
    Sides of a `conic_sweep` prism become invisible when viewed head-on using
    an orthographic camera.
  - <5a4f8ddc$1@news.povray.org>
    (2018-01-05, povray.beta-test, "3.8.0 block pattern, density list parsing issue.")
  - <web.5b8350d429c104f2458c7afe0@news.povray.org>
    (2018-08-27, povray.advanced-users, "Re: It gets even weirder.")
    Trying to `#declare Foo[A][B]=...` with `Foo` being an array of arrays and
    `Foo[A]` not yet initialized causes a hard crash instead of a parse error.

Reported by Coverity static code analysis:
  
  - CID 986462,986463 (Wrapper object use after free)
  - CID 967358-967362 (Uninitialized scalar variable)
  - CID 1372542-1372544 (Result is not floating-point)
  - CID 1372618 (Uninitialized scalar variable)
  - CID 1372629 (Uninitialized pointer read)
  - CID 1372630-1372632 (Uninitialized scalar variable)

Miscellaneous:

  - Fix `interior_texture` for text objects (as mentioned in GitHub issue #65).
  - Eliminated use of deprecated C++ `register` keyword (except in 3rd party
    libraries bundled with the POV-Ray source code).
  - Fix long-standing bug in Julia fractal primitive using hypercomplex numbers.

## Other Noteworthy


## New/changed elements seen in the 3.8 reference (vs 3.7)
- Objects: mesh, mesh2, polygon (finite patch); lemon; ovus extensions;
  superquadric ellipsoid naming.
- Camera: mesh_camera.
- Options: +SST stochastic seed; output/preview refinements.
- Textures/patterns/media: see 3.8-embellishments.md and 3.8-special-effects.md
  for the 3.8 wording (identical in most parts to 3.7).
