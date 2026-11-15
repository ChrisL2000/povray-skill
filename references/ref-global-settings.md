## 3.4.1 Global Settings

## 3.4.1 Global Settings

The `global_settings` statement is a catch-all statement that
gathers together a number of global parameters. The statement may appear
anywhere in a scene as long as it is not inside any other statement. You may
have multiple `global_settings` statements in a scene. Whatever
values were specified in the last `global_settings` statement
override any previous settings.

Note: Some items which were language directives in earlier versions of
POV-Ray have been moved inside the `global_settings` statement so
that it is more obvious to the user that their effect is global. The old
syntax is permitted but generates a warning.

 The new syntax is:

```

GLOBAL_SETTINGS:
 global_settings { [GLOBAL_SETTINGS_ITEMS...] }
GLOBAL_SETTINGS_ITEM:
 adc_bailout Value | ambient_light COLOR | assumed_gamma GAMMA_VALUE |
 hf_gray_16 [Bool] | irid_wavelength COLOR | charset GLOBAL_CHARSET |
 max_intersections Number | max_trace_level Number |
 mm_per_unit Number | number_of_waves Number | noise_generator Number |
 radiosity { RADIOSITY_ITEMS... } | subsurface { SUBSURFACE_ITEMS } |
 photon { PHOTON_ITEMS... }
GLOBAL_CHARSET:
 ascii | utf8 | sys
GAMMA_VALUE:
 Value | srgb
```

Global setting default values:

```

charset : ascii
adc_bailout : 1/255
ambient_light : <1,1,1>
assumed_gamma : 1.0 (undefined for legacy scenes)
hf_gray_16 : deprecated
irid_wavelength : <0.25,0.18,0.14>
max_trace_level : 5
max_intersections : 64
mm_per_unit : 10
number_of_waves : 10
noise_generator : 2

Radiosity:
adc_bailout : 0.01
always_sample : off
brightness : 1.0
count : 35 (supports adaptive mode)
error_bound : 1.8
gray_threshold : 0.0
low_error_factor : 0.5
max_sample : non-positive value
maximum_reuse : 0.2
minimum_reuse : 0.015
nearest_count : 5 (max = 20; supports adaptive mode)
normal : off
pretrace_start : 0.08
pretrace_end : 0.04
recursion_limit : 2
subsurface : off

Subsurface:
radiosity : off
samples : 50,50
```

Each item is optional and may appear in any order. If an item is specified
more than once, the last setting overrides previous values. Details on each
item are given in the following sections.

### 3.4.1.1 ADC_Bailout

In scenes with many reflective and transparent surfaces, POV-Ray can get
bogged down tracing multiple reflections and refractions that contribute very
little to the color of a particular pixel. The program uses a system called
Adaptive Depth Control (ADC) to stop computing additional reflected
or refracted rays when their contribution is insignificant.

You may use the global setting `adc_bailout` keyword followed by
float value to specify the point at which a ray's contribution is
considered insignificant. For example:

```

global_settings { adc_bailout 0.01 }
```

The default value is 1/255, or approximately 0.0039, since a change
smaller than that could not be visible in a 24 bit image. Generally this
setting is perfectly adequate and should be left alone. Setting
`adc_bailout` to 0 will disable ADC, relying completely on
`max_trace_level` to set an upper limit on the number of rays spawned.

See the section Max_Trace_Level for details on how ADC and `max_trace_level` interact.

### 3.4.1.2 Ambient_Light

Ambient light is used to simulate the effect of inter-diffuse reflection
that is responsible for lighting areas that partially or completely lie in
shadow. POV-Ray provides the `ambient_light` keyword to let you
easily change the brightness of the ambient lighting without changing every
ambient value in all finish statements. It also lets you create interesting
effects by changing the color of the ambient light source. The syntax is:

```

global_settings { ambient_light COLOR }
```

The default is a white ambient light source set at `rgb
<1,1,1>`. Only the rgb components are used. The actual ambient
used is: Ambient = Finish_Ambient * Global_Ambient.

See the section Ambient for more information.

### 3.4.1.3 Assumed_Gamma

The `assumed_gamma` statement specifies a dsiplay gamma for which all color literals in the scene are presumed to be pre-corrected; at the same time it also defines the working gamma space in which POV-Ray will perform all its color computations.

Note: Using any value other than 1.0 will produce physically inaccurate results. Furthermore, if you decide to go for a different value for convenience, it is highly recommended to set this value to the same as your `Display_Gamma`. Using this parameter for artistic purposes is strongly discouraged.

Note: As of POV-Ray 3.7 this keyword is considered mandatory (except in legacy scenes) and consequently enables the experimental gamma handling feature. Future versions of POV-Ray may treat the absence of this keyword in non-legacy scenes as an error.

See section Gamma Handling for more information about gamma.

### 3.4.1.4 HF_Gray_16

Grayscale output can be used to generate heightfields for use in other POV-Ray scenes, and may be specified via `Grayscale_Output=true` as an INI option, or `+Fxg` (for output type 'x') as a command-line option. For example, `+Fng` for PNG and `+Fpg` for PPM (effectively PGM) grayscale output. By default this option is off.

Note: In version 3.7 the `hf_gray_16` keyword in the `global_settings` block has been deprecated. If encountered, it has no effect on the output type and will additionally generate a warning message.

With `Grayscale_Output=true`, the output file will be in the form of a heightfield, with the height at any point being dependent on the brightness of the pixel. The brightness of a pixel is calculated in the same way that color images are converted to grayscale images:`height = 0.3 * red + 0.59 * green + 0.11 * blue`.

Setting the `Grayscale_Output=true` option will cause the preview display, if used, to be grayscale rather than color. This is to allow you to see how the heightfield will look because some file formats store heightfields in a way that is difficult to understand afterwards. See the section Height Field for a description of how POV-Ray heightfields are stored for each file type.

Caveat: Grayscale output implies the maximum bit-depth the format supports is 16, it is not valid to specify bits per color channel with 'g' (e.g. `+Fng16` is not allowed, and nor for that matter is `+Fn16g`). If bits per channel is provided via an INI option, it is ignored.

Currently PNG, and PPM are the only file formats that support grayscale output.

### 3.4.1.5 Irid_Wavelength

Iridescence calculations depend upon the dominant wavelengths of the
primary colors of red, green and blue light. You may adjust the values using
the global setting `irid_wavelength` as follows...

```

global_settings { irid_wavelength COLOR }
```

The default value is `rgb <0.70,0.52,0.48>` and any
filter or transmit values are ignored. These values are proportional to the
wavelength of light but they represent no real world units.

In general, the default values should prove adequate but we provide this
option as a means to experiment with other values.

### 3.4.1.6 Charset

This allows you to specify the assumed character set of all text strings.
If you specify `ascii` only standard ASCII character codes in the
range from 0 to 127 are valid. You can easily find a table of ASCII
characters on the internet. The option `utf8` is a special Unicode
text encoding and it allows you to specify characters of nearly all languages
in use today. We suggest you use a text editor with the capability to export
text to UTF8 to generate input files. You can find more information,
including tables with codes of valid characters on the
Unicode website
The last possible option is to use a system specific character set. For
details about the `sys` character set option refer to the platform
specific documentation.

### 3.4.1.7 Max_Trace_Level

In scenes with many reflective and transparent surfaces POV-Ray can get
bogged down tracing multiple reflections and refractions that contribute very
little to the color of a particular pixel. The global setting `max_trace_level` defines the integer maximum number of recursive levels
that POV-Ray will trace a ray.

```

global_settings { max_trace_level Level }
```

This is used when a ray is reflected or is passing through a transparent
object and when shadow rays are cast. When a ray hits a reflective surface,
it spawns another ray to see what that point reflects. That is trace level
one. If it hits another reflective surface another ray is spawned and it goes
to trace level two. The maximum level by default is five.

One speed enhancement added to POV-Ray in version 3.0 is Adaptive Depth
Control (ADC). Each time a new ray is spawned as a result of reflection
or refraction its contribution to the overall color of the pixel is reduced
by the amount of reflection or the filter value of the refractive surface. At
some point this contribution can be considered to be insignificant and there
is no point in tracing any more rays. Adaptive depth control is what tracks
this contribution and makes the decision of when to bail out. On scenes that
use a lot of partially reflective or refractive surfaces this can result in a
considerable reduction in the number of rays fired and makes it safer to use
much higher `max_trace_level` values.

This reduction in color contribution is a result of scaling by the
reflection amount and/or the filter values of each surface, so a perfect
mirror or perfectly clear surface will not be optimizable by ADC. You can see
the results of ADC by watching the `Rays Saved` and `Highest
Trace Level` displays on the statistics screen.

The point at which a ray's contribution is considered insignificant is
controlled by the `adc_bailout` value. The default is 1/255 or
approximately 0.0039 since a change smaller than that could not be visible in
a 24 bit image. Generally this setting is perfectly adequate and should be
left alone. Setting `adc_bailout` to 0 will disable ADC, relying
completely on `max_trace_level` to set an upper limit on the
number of rays spawned.

If `max_trace_level` is reached before a non-reflecting surface
is found and if ADC has not allowed an early exit from the ray tree the
color is returned as black. Raise `max_trace_level` if you see
black areas in a reflective surface where there should be a color.

The other symptom you could see is with transparent objects. For instance,
try making a union of concentric spheres with a clear texture on them. Make
ten of them in the union with radius's from 1 to 10 and render the scene.
The image will show the first few spheres correctly, then black. This is
because a new level is used every time you pass through a transparent
surface. Raise `max_trace_level` to fix this problem.

Note: Raising `max_trace_level` will use more memory and time
and it could cause the program to crash with a stack overflow error, although
ADC will alleviate this to a large extent.

Values for `max_trace_level` can be set up to a maximum of 256.
If there is no `max_trace_level` set and during rendering the default value is reached, a warning is issued.

### 3.4.1.8 Max_Intersections

POV-Ray uses a set of internal stacks to collect ray/object intersection points. The usual maximum number of entries in these I-Stacks is 64. Complex scenes may cause these stacks to overflow. POV-Ray does not stop but it may incorrectly render your scene. When POV-Ray finishes rendering, a number of statistics are displayed. If you see `I-Stack overflows` reported in the statistics you should increase the stack size. Add a global setting to your scene as follows:

```

global_settings { max_intersections Integer }
```

If the `I-Stack Overflows` remain increase this value until they stop.

### 3.4.1.9 Mm_Per_Unit

See the section Subsurface Light Transport for more information about the role of `mm_per_unit` in the global settings block.

### 3.4.1.10 Number_Of_Waves

The `waves` and `ripples`
patterns are generated by summing a series of waves, each with a slightly different center and size. By default, ten waves are summed but this amount can be globally controlled by changing the `number_of_waves` setting.

```

global_settings { number_of_waves Integer }
```

Changing this value affects both waves and ripples alike on all patterns in the scene.

### 3.4.1.11 Noise_generator

 There are three noise generators implemented.

`noise_generator 1` the noise that was used in POV_Ray 3.1
`noise_generator 2` 'range corrected' version of the old noise, it does not show the plateaus seen with `noise_generator 1`
`noise_generator 3` generates Perlin noise

The default is `noise_generator 2`

Note: The noise_generators can also be used within the pigment/normal/etc. statement.

### 3.4.1.12 Subsurface

See the section Subsurface Light Transport for more information about the role of `subsurface` in the global settings block.
