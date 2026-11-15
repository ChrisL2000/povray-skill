## 3.4.7 Patterns

## 3.4.7 Pattern

POV-Ray uses a method called three-dimensional solid texturing to
define the color, bumpiness and other properties of an object. You specify
the way that the texture varies over a surface by specifying a
pattern. Patterns are used in pigments, normals and texture maps as well
as media density.

All patterns in POV-Ray are three dimensional. For every point in space,
each pattern has a unique value. Patterns do not wrap around a surface like
putting wallpaper on an object. The patterns exist in 3d and the objects are
carved from them like carving an object from a solid block of wood or
stone.

Consider a block of wood. It contains light and dark bands that are
concentric cylinders being the growth rings of the wood. On the end of the
block you see these concentric circles. Along its length you see lines that
are the veins. However the pattern exists throughout the entire block. If you
cut or carve the wood it reveals the pattern inside. Similarly an onion
consists of concentric spheres that are visible only when you slice it.
Marble stone consists of wavy layers of colored sediments that harden into
rock.

These solid patterns can be simulated using mathematical functions. Other
random patterns such as granite or bumps and dents can be generated using a
random number system and a noise function.

In each case, the x, y, z coordinate of a point on a surface is used to
compute some mathematical function that returns a float value. When used with
color maps or pigment maps, that value looks up the color of the pigment to
be used. In normal statements the pattern function result modifies or
perturbs the surface normal vector to give a bumpy appearance. Used with a
texture map, the function result determines which combinations of entire
textures to be used. When used with media density it specifies the density of
the particles or gasses.

The following sections describe each pattern. See the sections Pigment, Normal, Patterned Textures and Density for more details on how to use patterns. Unless mentioned otherwise, all patterns use the `ramp_wave` wave type by default but may use any wave type and may be used with `color_map`,
`pigment_map`, `normal_map`, `slope_map`, `texture_map`, `density`, and `density_map`.

Note: Some patterns have a built in default color_map that does not result in a
grey-scale pattern. This may lead to unexpected results when one of these
patterns is used without a user specified color_map, for example in
functions or media.

 These patterns are:

`agate`
`bozo`
`brick`
`checker`
`hexagon`
`mandel`
`marble`
`radial`
`square`
`triangular`
`wood`

See the following sections for more pattern and pattern related topics:

 General Patterns
 Discontinuous Patterns
 Normal-Dependent Patterns
 Special Patterns
 Pattern Modifiers

### 3.4.7.1 General Patterns

Many patterns can be used in textures, normals and media. These patterns are `agate`, `boxed`, `bozo`, `brick`, `bumps`, `cubic`, `cylindrical`, `density_file`, `dents`, `facets`, `fractal`, `function`, `gradient`, `granite`, `hexagon`,
`leopard`, `marble`, `onion`, `pavement`, `pigment_pattern`, `planar`, `quilted`, `radial`, `ripples`, `spherical`, `spiral1`, `spiral2`,
`spotted`, `square`, `tiling`, `waves`, `wood`, and `wrinkles`.

3.4.7.1.1 Agate Pattern

The `agate` pattern is a banded pattern similar to marble but
it uses a specialized built-in turbulence function that is different from the
traditional turbulence. The traditional turbulence can be used as well but it
is generally not necessary because agate is already very turbulent. You may
control the amount of the built-in turbulence by adding the optional `agate_turb` keyword followed by a float value. For example:

```

pigment {
 agate
 agate_turb 0.5
 color_map {MyMap}
 }
```

The `agate` pattern has a default color_map built in that results
in a brown and white pattern with smooth transitions.

Agate as used in a normal:

```

normal {
 agate [Bump_Size]
 [MODIFIERS...]
 }
```

3.4.7.1.2 Boxed Pattern

The `boxed` pattern creates a 2x2x2 unit cube centered at the
origin. It is computed by: value =1.0- min(1, max(abs(X), abs(Y),
abs(Z))) It starts at 1.0 at the origin and decreases to a minimum value
of 0.0 as it approaches any plane which is one unit from the origin. It
remains at 0.0 for all areas beyond that distance. This pattern was
originally created for use with `halo` or `media` but
it may be used anywhere any pattern may be used.

3.4.7.1.3 Bozo Pattern

The `bozo` pattern is a very smooth, random noise function that
is traditionally used with some turbulence to create clouds. The `spotted` pattern is identical to `bozo` but in early
versions of POV-Ray spotted did not allow turbulence to be added. Turbulence
can now be added to any pattern so these are redundant but both are retained
for backwards compatibility. The `bumps` pattern is also identical
to `bozo` when used anywhere except in a `normal`
statement. When used as a normal pattern, `bumps` uses a slightly
different method to perturb the normal with a similar noise function.

The `bozo` noise function has the following properties:

 1. It is defined over 3D space i.e., it takes x, y, and z and returns
the noise value there.

2. If two points are far apart, the noise values at those points are
relatively random.

3. If two points are close together, the noise values at those points are
close to each other.

You can visualize this as having a large room and a thermometer that
ranges from 0.0 to 1.0. Each point in the room has a temperature. Points that
are far apart have relatively random temperatures. Points that are close
together have close temperatures. The temperature changes smoothly but
randomly as we move through the room.

Now let's place an object into this room along with an artist. The
artist measures the temperature at each point on the object and paints that
point a different color depending on the temperature. What do we get? A
POV-Ray bozo texture!

The `bozo` pattern has a default color_map built in that results
in a green, blue, red and white pattern with sharp transitions.

Note: The appearance of the bozo pattern depends on the noise generator used.
The default type is 2. This may be changed using the `noise_generator` keyword. See the section Pattern Modifiers: noise_generator.

3.4.7.1.4 Brick Pattern

The `brick` pattern generates a pattern of bricks. The bricks
are offset by half a brick length on every other row in the x- and
z-directions. A layer of mortar surrounds each brick. The syntax is given
by

```

pigment {
 brick COLOR_1, COLOR_2
 [brick_size <Size>] [mortar Size]
 }
```

where COLOR_1 is the color of the mortar and COLOR_2 is
the color of the brick itself. If no colors are specified a default deep red
and dark gray are used. The default size of the brick and mortar together is
<8, 3, 4.5> units. The default thickness of the mortar is 0.5 units.
These values may be changed using the optional `brick_size` and
`mortar` pattern modifiers. You may also use pigment statements in
place of the colors. For example:

```

pigment {
 brick pigment{Jade}, pigment{Black_Marble}
 }
```

This example uses normals:

```

normal { brick 0.5 }
```

The float value is an optional bump size. You may also use full normal
statements. For example:

```

normal {
 brick normal{bumps 0.2}, normal{granite 0.3}
 }
```

When used with textures, the syntax is

```

texture {
 brick texture{T_Gold_1A}, texture{Stone12}
 }
```

This is a block pattern which cannot use wave types, `color_map`, or `slope_map` modifiers.

The `brick` pattern has a default color_map built in that
results in red bricks and grey mortar.

3.4.7.1.5 Bumps Pattern

The `bumps` pattern was originally designed only to be used as
a normal pattern. It uses a very smooth, random noise function that creates
the look of rolling hills when scaled large or a bumpy orange peel when
scaled small. Usually the bumps are about 1 unit apart.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `bumps`
pattern is identical to `bozo` or `spotted` and is
similar to normal bumps but is not identical as are most normals when
compared to pigments.

Note: The appearance of the bumps pattern depends on the noise generator used.
The default type is 2. This may be changed using the `noise_generator` keyword. See the section Pattern Modifiers: noise_generator.

3.4.7.1.6 Cubic Pattern

The `cubic` pattern takes six texture elements and maps each one to each of the six pyramids centered at each half-axis, effectively mapping each texture element to each side of a origin-centered cube.

| The cubic pattern and the order of texture elements The first group of elements map to the positive half-axis, in the X, Y and Z axes respectively. The same order is applied to the last group of elements, except on the negative half-axis. | |

The syntax is:

```

texture {
 cubic
 TEXTURE_ELEMENT_1
 ...
 TEXTURE_ELEMENT_6
 }
```

3.4.7.1.7 Cylindrical Pattern

The `cylindrical` pattern creates a one unit radius cylinder
along the Y axis. It is computed by: value = 1.0-min(1, sqrt(X^2 +
Z^2)) It starts at 1.0 at the origin and decreases to a minimum value of
0.0 as it approaches a distance of 1 unit from the Y axis. It remains at 0.0
for all areas beyond that distance. This pattern was originally created for
use with `halo` or `media` but it may be used anywhere
any pattern may be used.

3.4.7.1.8 Density File Pattern

The `density_file` pattern is a 3-D bitmap pattern that
occupies a unit cube from location <0,0,0> to <1,1,1>. The data
file is a raw binary file format created for POV-Ray called `df3`
format. The syntax provides for the possibility of implementing other formats
in the future. This pattern was originally created for use with `halo` or `media` but it may be used anywhere any pattern may
be used. The syntax is:

```

pigment {
 density_file df3 "filename.df3"
 [interpolate Type] [PIGMENT_MODIFIERS...]
 }
```

where `"filename.df3"` is a file name of the
data file.

As a normal pattern, the syntax is

```

normal {
 density_file df3 "filename.df3" [, Bump_Size]
 [interpolate Type]
 [NORMAL_MODIFIERS...]
 }
```

The optional float `Bump_Size` should follow the file
name and any other modifiers follow that.

The density pattern occupies the unit cube regardless of the dimensions in voxels.
It remains at 0.0 for all areas beyond the unit cube. The data in the range of 0 to 255,
in case of 8 bit resolution, are scaled into a float value in the range 0.0 to 1.0.

 The `interpolate` keyword may be specified to add interpolation
of the data. The default value of zero specifies no interpolation. A value of
one specifies tri-linear interpolation, a value of two specifies tri-cubic
interpolation

 See the sample scenes for data file `include\spiral.df3`,and
the scenes which use it: `~scenes\textures\patterns\densfile.pov`,
`~scenes\interior\media\galaxy.pov` for examples.

3.4.7.1.8.1 df3 file format

Header:
 The `df3` format consists of a 6 byte header of three
16-bit integers with high order byte first. These three values give the
x,y,z size of the data in pixels (or more appropriately called voxels
).
Data:
 The header is followed by x*y*z unsigned integer bytes of data with a
resolution of 8, 16 or 32 bit. The data are written with high order byte
first (big-endian). The resolution of the data is determined by the size
of the df3-file. That is, if the file is twice (minus header, of course)
as long as an 8 bit file then it is assumed to contain 16 bit ints and
if it is four times as long 32 bit ints.

3.4.7.1.9 Dents Pattern

The `dents` pattern was originally designed only to be used as
a normal pattern. It is especially interesting when used with metallic
textures. It gives impressions into the metal surface that look like dents
have been beaten into the surface with a hammer. Usually the dents are about
1 unit apart.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `dents`
pattern is similar to normal dents but is not identical as are most normals
when compared to pigments.

3.4.7.1.10 Facets Pattern

```

normal {
 facets [coords SCALE_VALUE | size FACTOR]
 [NORMAL_ITEMS...]
 }
```

The `facets` pattern is designed to be used as a normal,
it is not suitable for use as a pigment: it will cause an error.

 There are two forms of the facets pattern. One is most suited for use with rounded surfaces,
and one is most suited for use with flat surfaces.

If `coords` is specified, the facets pattern creates facets with a size on the same order
as the specified SCALE_VALUE. This version of facets is most suited for use with flat surfaces, but will
also work with curved surfaces. The boundaries of the facets coincide with the boundaries of the cells in
the standard crackle pattern. The coords version of this pattern may be quite similar to a crackle normal
pattern with solid specified.

If `size` is specified, the facets texture uses a different function that creates facets
only on curved surfaces. The FACTOR determines how many facets are created, with smaller values
creating more facets, but it is not directly related to any real-world measurement. The same factor
will create the same pattern of facets on a sphere of any size.

This pattern creates facets by snapping normal vectors to the closest vectors in a perturbed grid
of normal vectors. Because of this, if a surface has normal vectors that do not vary along one or more
axes, there will be no facet boundaries along those axes.

3.4.7.1.11 Fractal Pattern

Fractal patterns supported in POV-Ray:

The Mandelbrot set with exponents up to 33. The formula for these is: `z(n+1) = z(n)^p + c`, where `p` is the correspondent exponent.
The equivalent Julia sets.
The magnet1 and magnet2 fractals (which are derived from some magnetic renormalization transformations; see the fractint help for more details). Both 'Mandelbrot' and 'Julia' versions of them are supported.

For the Mandelbrot and Julia sets, higher exponents will be slower for two reasons:

 For the exponents 2,3 and 4 an optimized algorithm is used. Higher exponents use a generic algorithm for raising a complex number to an integer exponent, and this is a bit slower than an optimized version for a certain exponent.
 The higher the exponent, the slower it will be. This is because the amount of operations needed to raise a complex number to an integer exponent is directly proportional to the exponent. This means that exponent 10 will be (very) roughly twice as slow as exponent 5.

| Mandelbrot and Julia fractal patterns of exponents 2 to 5 |

The syntax is:

```

MANDELBROT:
 mandel ITERATIONS [, BUMP_SIZE]
 [exponent EXPONENT]
 [exterior EXTERIOR_TYPE, FACTOR]
 [interior INTERIOR_TYPE, FACTOR]

JULIA:
 julia COMPLEX, ITERATIONS [, BUMP_SIZE]
 [exponent EXPONENT]
 [exterior EXTERIOR_TYPE, FACTOR]
 [interior INTERIOR_TYPE, FACTOR]

MAGNET MANDEL:
 magnet MAGNET_TYPE mandel ITERATIONS [, BUMP_SIZE]
 [exterior EXTERIOR_TYPE, FACTOR]
 [interior INTERIOR_TYPE, FACTOR]

MAGNET JULIA:
 magnet MAGNET_TYPE julia COMPLEX, ITERATIONS [, BUMP_SIZE]
 [exterior EXTERIOR_TYPE, FACTOR]
 [interior INTERIOR_TYPE, FACTOR]
```

Where:

`ITERATIONS` is the number of times to iterate (up to 2^32-1) the algorithm.

`COMPLEX` is a 2D vector denoting a complex number.

`MAGNET_TYPE` is either 1 or 2.

`exponent` is an integer between 2 and 33. If not given, the default is 2.

`interior` and `exterior` specify special coloring algorithms. You can specify one of them or both at the same time. They only work with the fractal patterns.

`EXTERIOR_TYPE` and `INTERIOR_TYPE` are integer
values between 0 and 6 (inclusive). When not specified, the default value of INTERIOR_TYPE
is 0 and for EXTERIOR_TYPE 1.

`FACTOR` is a float. The return value of the pattern is multiplied by
`FACTOR` before returning it. This can be used to scale the value range
of the pattern when using interior and exterior coloring (this is often needed to get the
desired effect). The default value of FACTOR is 1.

| Magnet mandel and julia type 1 and 2 fractal patterns |

The different values of `EXTERIOR_TYPE` and `INTERIOR_TYPE` have the following
meaning:

0: Returns just 1
1: For exterior: The number of iterations until bailout divided by ITERATIONS.

Note: This is not scaled by FACTOR (since it is internally
scaled by 1/ITERATIONS instead).    For interior: The absolute value of the smallest point in the orbit of the calculated point
2: Real part of the last point in the orbit
3: Imaginary part of the last point in the orbit
4: Squared real part of the last point in the orbit
5: Squared imaginary part of the last point in the orbit
6: Absolute value of the last point in the orbit
7: For exterior only: the number of iterations modulo FACTOR and divided by FACTOR.

Note: This is of course not scaled by FACTOR. The covered range is 0 to FACTOR-1/FACTOR.
8: For exterior only: the number of iterations modulo FACTOR+1 and divided by FACTOR.

Note: This is of course not scaled by FACTOR. The covered range is 0 to 1.

Example:

```

box {<-2, -2, 0>, <2, 2, 0.1>
 pigment {
 julia <0.353, 0.288>, 30
 interior 1, 1
 color_map {
 [0 rgb 0]
 [0.2 rgb x]
 [0.4 rgb x+y]
 [1 rgb 1]
 [1 rgb 0]
 }
 }
 }
```

| Different exterior and interior coloring types of fractal patterns |

3.4.7.1.12 Function Pattern

Allows you to use a function { } block as pattern.

```

pigment {
 function { USER_DEFINED_FUNCTIONS }
 [PIGMENT_MODIFIERS...]
 }
```

Declaring a function:

By default a function takes three parameters (x,y,z) and you do not have
to explicitly specify the parameter names when declaring it. When using
the identifier, the parameters must be specified.

```

#declare Foo = function { x + y + z}

pigment {
 function { Foo(x, y, z) }
 [PIGMENT_MODIFIERS...]
 }
```

On the other hand, if you need more or less than three parameters when
declaring a function, you also have to explicitly specify the parameter
names.

```

#declare Foo = function(x,y,z,t) { x + y + z + t}

pigment {
 function { Foo(x, y, z, 4) }
 [PIGMENT_MODIFIERS...]
 }
```

Using function in a normal:

```

#declare Foo = function { x + y + z}

normal {
 function { Foo(x, y, z) } [Bump_Size]
 [MODIFIERS...]
 }
```

3.4.7.1.12.1 What can be used

All float expressions and operators. See the section User-Defined Functions for what is legal in POV-Ray. Of special interest here is the `pattern` option, that makes it possible to use patterns as functions

```

#declare FOO = function {
 pattern {
 checker
 }
 }
```

User defined functions (like equations).

Since pigments can be declared as functions, they can also be used in
functions. They must be declared first. When using the identifier, you
have to specify which component of the color vector should be used. To
do this, the dot notation is used: Function(x,y,z).red

```

#declare FOO = function {pigment { checker } }
 pigment {
 function { FOO(x,y,z).green }
 [PIGMENT_MODIFIERS...]
 }
```

POV-Ray has a large amount of pre-defined functions. These are mainly
algebraic surfaces but there is also a mesh function and noise3d
function. See section Internal Functions for a complete list and some
explanation on the parameters to use. These internal functions can be
included through the functions.inc include file.

```

#include "functions.inc"
#declare FOO = function {pigment { checker } }
 pigment {
 function { FOO(x,y,z).green & f_noise3d(x*2, y*3,z)}
 [PIGMENT_MODIFIERS...]
 }
```

3.4.7.1.12.2 Function Image

Syntax :

`function Width, Height { FUNCTION_BODY }`

Not a real pattern, but listed here for convenience. This keyword defines
a new 'internal' bitmap image type. The pixels of the image are derived
from the Function_Body, with Function_Body either being a regular
function, a pattern function or a pigment function. In case of a pigment
function the output image will be in color, in case of a pattern or regular
function the output image will be grayscale. All variants of grayscale
pigment functions are available using the regular function syntax, too.
In either case the image will use 16 bit per component

Note: Functions are evaluated on the x-y plane. This is different from
the pattern image type for the reason that it makes using uv functions
easier.

Width and Height specify the resolution of the resulting 'internal' bitmap image.
The image is taken from the square region `<0,0,0>, <1,1,0>`

The `function` statement can be used wherever an image specifier
like `tga` or `png` may be used. Some uses include
creating heightfields from procedural textures or wrapping a slice of a 3d
texture or function around a cylinder or extrude it along an axis.

Examples:

```

plane {y, -1
 pigment {
 image_map {
 function 10,10 {
 pigment { checker 1,0 scale .5 }
 }
 }
 rotate x*90
 }
 }
```

```

height_field {
 function 200,200 {
 pattern {
 bozo
 }
 }
 translate -0.5
 scale 10
 pigment {rgb 1}
 }
```

Note: For height fields and other situations where color is not needed
it is easier to use `function n,n {pattern{...}}` than `function n,n {pigment{...}}`.
The pattern functions are returning a scalar, not a color vector, thus a pattern is grayscale.

3.4.7.1.13 Gradient Pattern

One of the simplest patterns is the `gradient` pattern. It is
specified as

```

pigment {
 gradient <Orientation>
 [PIGMENT_MODIFIERS...]
 }
```

where `<Orientation>` is a vector pointing in
the direction that the colors blend. For example

```

pigment { gradient x } // bands of color vary as you move
 // along the "x" direction.
```

produces a series of smooth bands of color that look like layers of colors
next to each other. Points at x=0 are the first color in the color map. As
the x location increases it smoothly turns to the last color at x=1. Then it
starts over with the first again and gradually turns into the last color at
x=2. In POV-Ray versions older than 3.5 the pattern reverses for negative values of x.
As per POV-Ray 3.5 this is not the case anymore. Using `gradient
y` or `gradient z` makes the colors blend along the y- or
z-axis. Any vector may be used but x, y and z are most common.

As a normal pattern, gradient generates a saw-tooth or ramped wave
appearance. The syntax is

```

normal {
 gradient <Orientation> [, Bump_Size]
 [NORMAL_MODIFIERS...]
 }
```

where the vector `<Orientation>` is a required
parameter but the float `Bump_Size` which follows is
optional.

Note: The comma is required especially if Bump_Size is
negative.

If only the range -1 to 1 was used of the old gradient, for example in a
`sky_sphere`, it can be replaced by the `planar` or `marble`
pattern and revert the color_map. Also rotate the pattern for other orientations than `y`.
A more general solution is to use `function{abs(x)}` as a pattern instead
of `gradient x` and similar for `gradient y` and `gradient z`.

3.4.7.1.14 Granite Pattern

The `granite` pattern uses a simple 1/f fractal noise function
to give a good granite pattern. This pattern is used with creative color maps
in `stones.inc` to create some gorgeous layered stone
textures.

As a normal pattern it creates an extremely bumpy surface that looks like a
gravel driveway or rough stone.

Note: The appearance of the granite pattern depends on the noise generator used.
The default type is 2. This may be changed using the `noise_generator` keyword. See the Pattern Modifiers section: noise_generator.

3.4.7.1.15 Leopard Pattern

Leopard creates regular geometric pattern of circular spots. The formula
used is: value = Sqr((sin(x)+sin(y)+sin(z))/3)

3.4.7.1.16 Marble Pattern

The `marble` pattern is very similar to the `gradient
x` pattern. The gradient pattern uses a default `ramp_wave`
wave type which means it uses colors from the color map from 0.0 up to 1.0 at
location x=1 but then jumps back to the first color for x > 1 and repeats
the pattern again and again. However the `marble` pattern uses
the `triangle_wave` wave type in which it uses the color map from
0 to 1 but then it reverses the map and blends from 1 back to zero. For
example:

```

pigment {
 gradient x
 color_map {
 [0.0 color Yellow]
 [1.0 color Cyan]
 }
 }
```

This blends from yellow to cyan and then it abruptly changes back to
yellow and repeats. However replacing `gradient x` with `marble` smoothly blends from yellow to cyan as the x coordinate goes
from 0.0 to 0.5 and then smoothly blends back from cyan to yellow by
x=1.0.

Earlier versions of POV-Ray did not allow you to change wave types. Now that
wave types can be changed for most any pattern, the distinction between
`marble` and `gradient x` is only a matter of default
wave types.

When used with turbulence and an appropriate color map, this pattern looks
like veins of color of real marble, jade or other types of stone. By default,
marble has no turbulence.

The `marble` pattern has a default color_map built in that results
in a red, black and white pattern with smooth and sharp transitions.

3.4.7.1.17 Onion Pattern

The `onion` is a pattern of concentric spheres like the layers
of an onion. Value = mod(sqrt(Sqr(X)+Sqr(Y)+Sqr(Z)), 1.0) Each
layer is one unit thick.

3.4.7.1.18 Pavement Pattern

The `pavement` is a pattern which paves the x-z plane with a single polyform tile. A polyform is a plane figure constructed by joining together identical basic polygons. The `number_of_sides` is used to choose that basic polygon: an equilateral triangle (3), a square (4) or a hexagon (6). The `number_of_tiles` is used to choose the number of basic polygons in the tile while `pattern` is used to choose amongst the variants.

The syntax is:

```

pigment {
 pavement
 [PAVEMENT_MODIFIERS...]
 }

PAVEMENT_MODIFIERS:
 number_of_sides SIDES_VALUE | number_of_tiles TILES_VALUE | pattern PATTERN_VALUE |
 exterior EXTERIOR_VALUE | interior INTERIOR_VALUE | form FORM_VALUE |
 PATTERN_MODIFIERS
```

A table of the number of patterns:

| Sides | Tiles |
| 1 | 2 | 3 | 4 | 5 | 6 |
| 3 | 1 | 1 | 1 | 3 | 4 | 12 |
| 4 | 1 | 1 | 2 | 5 | 12 | 35 |
| 6 | 1 | 1 | 3 | 7 | 22 | |

| The various patterns with 6 squares. |

There is no nomenclature for pattern, they are just numbered from 1 to the maximum relevant value.

`form`
0, 1 or 2, a special 3 is allowed for square only which copy the look of `interior` for some additional variations.

`interior`
0, 1 or 2

`exterior`
0, 1 or 2; Not used for hexagon.

The `form`, `exterior` and `interior` specify the look of angle used for respectively slow convex (turning side), quick convex (pointy tile) and concave angle (interior angle between many tiles).

0 is a normal pointy angle. (a right angle for square)
1 is the same as 0, but the pointy angle is broken in two. For square, the two corners are broken so as to share middle angle.
2 is a smooth negotiation of the angle, without pointy part.

Note: The case of paving the plane with tiles made of 6 hexagons is not supported because not all such tiles would pave the plane. For example, the ring made of six hexagons is not able to pave the plane.

3.4.7.1.19 Pigment Pattern

Use any pigment as a pattern. Instead of using the pattern directly on the object, a
pigment_pattern converts the pigment to gray-scale first. For each point, the gray-value
is checked against a list and the corresponding item is then used for the texture at
that particular point. For values between listed items, an averaged texture is calculated.

Texture items can be color, pigment, normal or texture and are specified in a
color_map, pigment_map, normal_map or texture_map.

It takes a standard pigment specification.

Syntax:

```

PIGMENT:
 pigment {
 pigment_pattern { PIGMENT_BODY }
 color_map { COLOR_MAP_BODY } |
 colour_map { COLOR_MAP_BODY } |
 pigment_map { PIGMENT_MAP_BODY }
 }

NORMAL:
 normal {
 pigment_pattern { PIGMENT_BODY } [Bump_Size]
 normal_map { NORMAL_MAP_BODY }
 }

TEXTURE:
 texture {
 pigment_pattern { PIGMENT_BODY }
 texture_map { TEXTURE_MAP_BODY }
 }

ITEM_MAP_BODY:
 ITEM_MAP_IDENTIFIER | ITEM_MAP_ENTRY...
 ITEM_MAP_ENTRY:
 [ GRAY_VALUE ITEM_MAP_ENTRY... ]
```

This pattern is also useful when parent and children patterns need to be
transformed independently from each other. Transforming the pigment_pattern
will not affect the child textures. When any of the child textures should be
transformed, apply it to the specific MAP_ENTRY.

This can be used with any pigments, ranging from a simple checker to very
complicated nested pigments. For example:

```

pigment {
 pigment_pattern {
 checker White, Black
 scale 2
 turbulence .5
 }
 pigment_map {
 [ 0, checker Red, Green scale .5 ]
 [ 1, checker Blue, Yellow scale .2 ]
 }
 }
```

Note: This pattern uses a pigment to get the gray values. If you want to
get the pattern from an image, you should use the image_pattern.

3.4.7.1.20 Planar Pattern

The `planar` pattern creates a horizontal stripe plus or minus
one unit above and below the X-Z plane. It is computed by: value =1.0-
min(1, abs(Y)) It starts at 1.0 at the origin and decreases to a minimum
value of 0.0 as the Y values approaches a distance of 1 unit from the X-Z
plane. It remains at 0.0 for all areas beyond that distance. This pattern was
originally created for use with `halo` or `media` but
it may be used anywhere any pattern may be used.

3.4.7.1.21 Quilted Pattern

The `quilted` pattern was originally designed only to be used
as a normal pattern. The quilted pattern is so named because it can create a
pattern somewhat like a quilt or a tiled surface. The squares are actually
3-D cubes that are 1 unit in size.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `quilted`
pattern is similar to normal quilted but is not identical as are most normals
when compared to pigments.

The two parameters `control0` and `control1` are used
to adjust the curvature of the seam or gouge area between
the `quilts`.

The syntax is:

```

pigment {
 quilted
 [QUILTED_MODIFIERS...]
 }

QUILTED_MODIFIERS:
 control0 Value_0 | control1 Value_1 | PIGMENT_MODIFIERS
```

The values should generally be kept to around the 0.0 to 1.0 range. The
default value is 1.0 if none is specified. Think of this gouge between the
tiles in cross-section as a sloped line.

| Quilted pattern with c0=0 and different values for c1. |

| Quilted pattern with c0=0.33 and different values for c1. |

| Quilted pattern with c0=0.67 and different values for c1. |

| Quilted pattern with c0=1 and different values for c1. |

This straight slope can be made to curve by adjusting the two control
values. The control values adjust the slope at the top and bottom of the
curve. A control values of 0 at both ends will give a linear slope, as shown
above, yielding a hard edge. A control value of 1 at both ends will give an
"s" shaped curve, resulting in a softer, more rounded edge.

The syntax for use as a normal is:

```

normal {
 quilted [Bump_Size]
 [QUILTED_MODIFIERS...]
 }

QUILTED_MODIFIERS:
 control0 Value_0 | control1 Value_1 | PIGMENT_MODIFIERS
```

3.4.7.1.22 Radial Pattern

The `radial` pattern is a radial blend that wraps around the
+y-axis. The color for value 0.0 starts at the +x-direction and wraps the
color map around from east to west with 0.25 in the -z-direction, 0.5 in -x,
0.75 at +z and back to 1.0 at +x. Typically the pattern is used with a `frequency` modifier to create multiple bands that radiate from the
y-axis. For example:

```

pigment {
 radial
 color_map {
 [0.5 Black]
 [0.5 White]
 }
 frequency 10
 }
```

creates 10 white bands and 10 black bands radiating from the y axis.

The `radial` pattern has a default color_map built in that results
in a yellow, magenta and cyan pattern with smooth transitions.

3.4.7.1.23 Ripples Pattern

The `ripples` pattern was originally designed only to be used
as a normal pattern. It makes the surface look like ripples of water. The
ripples radiate from 10 random locations inside the unit cube area
<0,0,0> to <1,1,1>. Scale the pattern to make the centers closer
or farther apart.

Usually the ripples from any given center are about 1 unit apart. The `frequency` keyword changes the spacing between ripples. The `phase` keyword can be used to move the ripples outwards for realistic
animation.

The number of ripple centers can be changed with the global parameter

```

global_settings { number_of_waves Count }
```

somewhere in the scene. This affects the entire scene. You cannot change the number of wave centers on individual patterns. See the section Number Of Waves for details.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `ripples`
pattern is similar to normal ripples but is not identical as are most normals
when compared to pigments.

3.4.7.1.24 Spherical Pattern

The `spherical` pattern creates a one unit radius sphere, with its center at
the origin. It is computed by: value = 1.0-min(1, sqrt(X^2 + Y^2 +
Z^2)) It starts at 1.0 at the origin and decreases to a minimum value of 0.0
as it approaches a distance of 1 unit from the origin in any direction. It
remains at 0.0 for all areas beyond that distance. This pattern was
originally created for use with `halo` or `media` but
it may be used anywhere any pattern may be used.

3.4.7.1.25 Spiral1 Pattern

The `spiral1` pattern creates a spiral that winds around the
z-axis similar to a screw. When viewed sliced in the x-y plane, it looks like
the spiral arms of a galaxy. Its syntax is:

```

pigment {
 spiral1 Number_of_Arms
 [PIGMENT_MODIFIERS...]
 }
```

The `Number_of_Arms` value determines how may arms are
winding around the z-axis.

As a normal pattern, the syntax is

```

normal {
 spiral1 Number_of_Arms [, Bump_Size]
 [NORMAL_MODIFIERS...]
 }
```

where the `Number_of_Arms` value is a required
parameter but the float `Bump_Size` which follows is
optional.

Note: The comma is required especially if Bump_Size is
negative.

The pattern uses the `triangle_wave` wave type by default but may
use any wave type.

3.4.7.1.26 Spiral2 Pattern

The `spiral2` pattern creates a double spiral that winds around
the z-axis similar to `spiral1` except that it has two overlapping spirals
which twist in opposite directions. The result sometimes looks like a basket
weave or perhaps the skin of pineapple. The center of a sunflower also has a
similar double spiral pattern. Its syntax is:

```

pigment {
 spiral2 Number_of_Arms
 [PIGMENT_MODIFIERS...]
 }
```

The `Number_of_Arms` value determines how may arms are
winding around the z-axis. As a normal pattern, the syntax is

```

normal {
 spiral2 Number_of_Arms [, Bump_Size]
 [NORMAL_MODIFIERS...]
 }
```

where the `Number_of_Arms` value is a required
parameter but the float `Bump_Size` which follows is
optional.

Note: The comma is required especially if Bump_Size is negative. The pattern uses the `triangle_wave` wave type by default but may use any wave type.

3.4.7.1.27 Spotted Pattern

The `spotted` pattern is identical to the `bozo`
pattern. Early versions of POV-Ray did not allow turbulence to be used with
spotted. Now that any pattern can use turbulence there is no difference
between `bozo` and `spotted`. See the section Bozo for details.

3.4.7.1.28 Tiling Pattern

The `tiling` pattern creates a series tiles in the x-z plane. See the image below for examples of the twenty-seven available patterns.

The syntax is as follows:

```

pigment {
 tiling Pattern_Number
 [PATTERN_MODIFIERS...]
 }
```

| The various tiling patterns annotated by tiling pattern and tiling type respectively |

For each pattern, each individual tile of the pattern has the same beveling as the other tiles in that pattern, allowing regular caulking to be defined. For a pattern with N tile types (where N is the tiling type noted in the above image) the main color/texture of the tiles are at x/N with x going from 0 to N-1, and the extreme color/texture caulk for these tiles are at (x+1)/N. The bevel covers the range between these two values.

To begin exploring the `tiling` pattern right away, see the distribution file `~scenes/textures/pattern/tiling.pov`. It uses obvious colors to better illustrate how the feature works, and you can optionally write it's `color_map` to a text file. Once you get a feel for the break points, you can always define you own map!

3.4.7.1.29 Waves Pattern

The `waves` pattern was originally designed only to be used as
a normal pattern. It makes the surface look like waves on water. The `waves` pattern looks similar to the `ripples` pattern except
the features are rounder and broader. The effect is to make waves that look
more like deep ocean waves. The waves radiate from 10 random locations inside
the unit cube area <0,0,0> to <1,1,1>. Scale the pattern to make
the centers closer or farther apart.

Usually the waves from any given center are about 1 unit apart. The `frequency` keyword changes the spacing between waves. The `phase` keyword can be used to move the waves outwards for realistic
animation.

The number of wave centers can be changed with the global parameter

```

global_settings { number_of_waves Count }
```

somewhere in the scene. This affects the entire scene. You cannot change the number of wave centers on individual patterns. See the section Number Of Waves for details.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `waves`
pattern is similar to normal waves but is not identical as are most normals
when compared to pigments.

3.4.7.1.30 Wood Pattern

The `wood` pattern consists of concentric cylinders centered on
the z-axis. When appropriately colored, the bands look like the growth rings
and veins in real wood. Small amounts of turbulence should be added to make
it look more realistic. By default, wood has no turbulence.

Unlike most patterns, the `wood` pattern uses the `triangle_wave` wave type by default. This means that like marble, wood
uses color map values 0.0 to 1.0 then repeats the colors in reverse order
from 1.0 to 0.0. However you may use any wave type.

The `wood` pattern has a default color_map built in that results
in a light and dark brown pattern with sharp transitions.

3.4.7.1.31 Wrinkles Pattern

The `wrinkles` pattern was originally designed only to be used
as a normal pattern. It uses a 1/f noise pattern similar to granite but the
features in wrinkles are sharper. The pattern can be used to simulate
wrinkled cellophane or foil. It also makes an excellent stucco texture.

When used as a normal pattern, this pattern uses a specialized normal
perturbation function. This means that the pattern cannot be used with `normal_map`, `slope_map` or wave type modifiers in a `normal` statement.

When used as a pigment pattern or texture pattern, the `wrinkles`
pattern is similar to normal wrinkles but is not identical as are most
normals when compared to pigments.

Note: The appearance of the wrinkles pattern depends on the noise generator used.
The default type is 2. This may be changed using the `noise_generator` keyword. See the section Pattern Modifiers: noise_generator.

### 3.4.7.2 Discontinuous Patterns

Some patterns are discontinuous, meaning their slope is infinite. These patterns are not suitable for use as object norms, as objects with discontinuous norms may look odd. These patterns work best for textures and media. They are `cells`, `checker`, `crackle`, `hexagon`, `object`, `square` and `triangular`.

Note: The cell and crackle patterns are mixed cases, that is, they are discontinuous at their respective boundaries. However, there is no limit to the different number of values, in the range of 0 to 1, that they can generate.

3.4.7.2.1 Cells Pattern

The `cells` pattern fills 3d space with unit cubes. Each cube gets a
random value from 0 to 1.

`cells` is not very suitable as a normal as it has no smooth
transitions of one grey value to another.

3.4.7.2.2 Checker Pattern

The `checker` pattern produces a checkered pattern consisting
of alternating squares of two colors. The syntax is:

```

pigment { checker [COLOR_1 [, COLOR_2]] [PATTERN_MODIFIERS...] }
```

If no colors are specified then default blue and green colors are
used.

The checker pattern is actually a series of cubes that are one unit in size.
Imagine a bunch of 1 inch cubes made from two different colors of modeling
clay. Now imagine arranging the cubes in an alternating check pattern and
stacking them in layer after layer so that the colors still alternate in
every direction. Eventually you would have a larger cube. The pattern of
checks on each side is what the POV-Ray checker pattern produces when applied
to a box object. Finally imagine cutting away at the cube until it is carved
into a smooth sphere or any other shape. This is what the checker pattern
would look like on an object of any kind.

You may also use pigment statements in place of the colors. For example:

```

pigment { checker pigment{Jade}, pigment{Black_Marble} }
```

This example uses normals:

```

normal { checker 0.5 }
```

The float value is an optional bump size. You may also use full normal
statements. For example:

```

normal {
 checker normal{gradient x scale .2}, normal{gradient y scale .2}
 }
```

When used with textures, the syntax is

```

texture { checker texture{T_Wood_3A}, texture{Stone12} }
```

The `checker` pattern has a default color_map built in that
results in blue and green tiles.

This use of checker as a texture pattern replaces the special tiles
texture in previous versions of POV-Ray. You may still use `tiles` but it may be phased out in future versions so checker textures
are best.

This is a block pattern which cannot use wave types, `color_map`, or `slope_map` modifiers.

3.4.7.2.3 Crackle Pattern

The `crackle` pattern is a set of random tiled multifaceted cells. The crackle pattern is only semi-procedural, requiring random values to be computed and cached for subsequent queries, with a fixed amount of data per unit-cube in crackle pattern coordinate space. Scaled smaller than the density of actual ray-object-intersections computed, it will eventually lead to a separate crackle cache entry being created for each and every intersection. After the cache reaches a certain size (currently 30mb per thread), new entries for that particular block will be discarded after they are calculated. Starting a new block will allow the caching to resume working again. While discarding the data is of course inefficient, it's still preferable to chewing up 100% of the available physical RAM and then hitting the swap-file.

There is a choice between different types:

Standard Crackle

Mathematically, the set crackle(p)=0 is a 3D Voronoi diagram of a field of semi random points and crackle(p) < 0 is the distance from the set along the shortest path (a Voronoi diagram is the locus of points equidistant from their two nearest neighbors from a set of disjoint points, like the membranes in suds are to the centers of the bubbles).

With a large scale and no turbulence it makes a pretty good stone wall or floor.
With a small scale and no turbulence it makes a pretty good crackle ceramic glaze.
Using high turbulence it makes a good marble that avoids the problem of apparent
parallel layers in traditional marble.

Form

```

pigment {
 crackle form <FORM_VECTOR>
 [PIGMENT_ITEMS ...]
 }

normal {
 crackle [Bump_Size]
 form <FORM_VECTOR>
 [NORMAL_ITEMS ...]
 }
```

Form determines the linear combination of distances used to create the pattern. Form is a vector.

The first component determines the multiple of the distance to the closest point to be used in determining the value of the pattern at a particular point.
The second component determines the coefficient applied to the second-closest distance.
The third component corresponds to the third-closest distance.

The standard form is <-1,1,0> (also the default), corresponding to the difference in the distances to the closest and second-closest points in the cell array. Another commonly-used form is <1,0,0>, corresponding to the distance to the closest point, which produces a pattern that looks roughly like a random collection of intersecting spheres or cells.

Other forms can create very interesting effects, but it is best to keep the sum of the coefficients low.
If the final computed value is too low or too high, the resultant pigment will be saturated with the color at the low or high end of the `color_map`. In this case, try multiplying the form vector by a constant.

Metric

```

pigment {
 crackle metric METRIC_VALUE
 [PIGMENT_ITEMS ...]
 }

normal {
 crackle [Bump_Size]
 metric METRIC_VALUE
 [NORMAL_ITEMS ...]
 }
```

Changing the metric changes the function used to determine which cell center is closer, for purposes of determining which cell a particular point falls in. The standard Euclidean distance function has a metric of 2. Changing the metric value changes the boundaries of the cells. A metric value of 3, for example, causes the boundaries to curve, while a very large metric constrains the boundaries to a very small set of possible orientations.

The default for metric is 2, as used by the standard crackle texture.
Metrics other than 1 or 2 can lead to substantially longer render times, as the method used to calculate such metrics is not as efficient.

Offset

```

pigment {
 crackle offset OFFSET_VALUE
 [PIGMENT_ITEMS ...]
 }

normal {
 crackle [Bump_Size]
 offset OFFSET_VALUE
 [NORMAL_ITEMS ...]
 }
```

The offset is used to displace the pattern from the standard xyz space along a fourth dimension.

It can be used to round off the pointy parts of a cellular normal texture or procedural heightfield by keeping the distances from becoming zero.
It can also be used to move the calculated values into a specific range if the result is saturated at one end of the color_map.
The default offset is zero.

Solid

```

pigment {
 crackle solid
 [PIGMENT_ITEMS ...]
 }

normal {
 crackle [Bump_Size]
 solid
 [NORMAL_ITEMS ...]
 }
```

Causes the same value to be generated for every point within a specific cell. This has practical applications in making easy stained-glass windows or flagstones. There is no provision for mortar, but mortar may be created by layering or texture-mapping a
standard crackle texture with a solid one. The default for this parameter is off.

3.4.7.2.4 Hexagon Pattern

The `hexagon` pattern is a block pattern that generates a
repeating pattern of hexagons in the x-z-plane. In this instance imagine tall
rods that are hexagonal in shape and are parallel to the y-axis and grouped
in bundles like shown in the example image. Three separate colors should be
specified as follows:

```

pigment {
 hexagon [COLOR_1 [, COLOR_2 [, COLOR_3]]]
 [PATTERN_MODIFIERS...]
 }
```

| The hexagon pattern. |

The three colors will repeat the hexagonal pattern with hexagon
COLOR_1 centered at the origin, COLOR_2 in the +z-direction and
COLOR_3 to either side. Each side of the hexagon is one unit long.
The hexagonal rods of color extend infinitely in the +y- and -y-directions.
If no colors are specified then default blue, green and red colors are
used.

You may also use pigment statements in place of the colors. For example:

```

pigment {
 hexagon
 pigment { Jade },
 pigment { White_Marble },
 pigment { Black_Marble }
 }
```

This example uses normals:

```

normal { hexagon 0.5 }
```

The float value is an optional bump size. You may also use full normal
statements. For example:

```

normal {
 hexagon
 normal { gradient x scale .2 },
 normal { gradient y scale .2 },
 normal { bumps scale .2 }
 }
```

When used with textures, the syntax is...

```

texture {
 hexagon
 texture { T_Gold_3A },
 texture { T_Wood_3A },
 texture { Stone12 }
 }
```

The `hexagon` pattern has a default color_map built in that results
in red, blue and green tiles.

This is a block pattern which cannot use wave types, `color_map`, or `slope_map` modifiers.

3.4.7.2.5 Object Pattern

The `object` pattern takes an object as input. It generates a, two item,
color list pattern. Whether a point is assigned to one item or the other depends on
whether it is inside the specified object or not.

Object's used in the `object` pattern cannot have a texture and must
be solid - these are the same limitations as for `bounded_by` and
`clipped_by`.

Syntax:

```

object {
 OBJECT_IDENTIFIER | OBJECT {}
 LIST_ITEM_A, LIST_ITEM_B
 }
```

Where OBJ_IDENTIFIER is the target object (which must be declared), or use the
full object syntax. LIST_ITEM_A and LIST_ITEM_B are the colors, pigments, or whatever
the pattern is controlling. LIST_ITEM_A is used for all points outside the object,
and LIST_ITEM_B is used for all points inside the object.

Example:

```

pigment {
 object {
 myTextObject
 color White
 color Red
 }
 turbulence 0.15
 }
```

Note: This is a block pattern which cannot use wave types, `color_map`, or `slope_map` modifiers.

3.4.7.2.6 Square Pattern

| | The square pattern is a block pattern that generates a repeating pattern of squares in the x-z plane. In this instance imagine tall rods that are square in shape and are parallel to the y-axis and grouped in bundles like shown in the example image. Four separate colors should be specified as follows: |
| The square pattern. | |

```

pigment {
 square [COLOR_1 [, COLOR_2 [, COLOR_3 [, COLOR_4]]]]
 [PATTERN_MODIFIERS...]
 }
```

Each side of the square is one unit long. The square rods of color extend infinitely in the +y and -y directions. If no colors are specified then default blue, green, red and yellow colors are used.

You may also use pigment statements in place of the colors. For example:

```

pigment {
 square
 pigment { Aquamarine },
 pigment { Turquoise },
 pigment { Sienna },
 pigment { SkyBlue }
}
```

When used with textures, the syntax is...

```

texture {
 square
 texture{ T_Wood1 },
 texture{ T_Wood2 },
 texture{ T_Wood4 },
 texture{ T_Wood8 }
}
```

The `square` pattern has a default color map built in that results in red, blue, yellow and green tiles.

This is a block pattern so, use of wave types, `color_map`, or `slope_map` modifiers is not allowed.

3.4.7.2.7 Triangular Pattern

| | The triangular pattern is a block pattern that generates a repeating pattern of triangles in the x-z plane. In this instance imagine tall rods that are triangular in shape and are parallel to the y-axis and grouped in bundles like shown in the example image. Six separate colors should be specified as follows: |
| The triangular pattern. | |

```

pigment {
 triangular [COLOR_1 [, COLOR_2 [, COLOR_3 [, COLOR_4 [, COLOR_5 [, COLOR_6]]]]]]
 [PATTERN_MODIFIERS...]
 }
```

Each side of the triangle is one unit long. The triangular rods of color extend infinitely in the +y and -y directions. If no colors are specified then default blue, green, red, magenta, cyan and yellow colors are used.

You may also use pigment statements in place of the colors. For example:

```

pigment {
 triangular
 pigment { Aquamarine },
 pigment { Turquoise },
 pigment { Sienna },
 pigment { Aquamarine },
 pigment { Turquoise },
 pigment { SkyBlue }
}
```

When used with textures, the syntax is...

```

texture {
 triangular
 texture{ T_Wood1 },
 texture{ T_Wood2 },
 texture{ T_Wood4 },
 texture{ T_Wood8 },
 texture{ T_Wood16 },
 texture{ T_Wood10 }
}
```

The `triangular` pattern has a default color map built in that results in red, blue, cyan, magenta, yellow and green tiles.

This is a block pattern so, use of wave types, `color_map`, or `slope_map` modifiers is not allowed.

### 3.4.7.3 Normal-Dependent Patterns

Some patterns depend on the normal vector in addition to a position vector. As such, these patterns are suitable for object normals only. They are `aoi` and `slope`.

3.4.7.3.1 Aoi Pattern

The `aoi` pattern can be used with `pigment`, `normal` and `texture` statements. The syntax is as follows:

```

pigment {
 aoi
 pigment_map {
 [0.0 MyPigmentA]
 ...
 [1.0 MyPigmentZ]
 }
 }

normal {
 aoi
 normal_map {
 [0.0 MyNormalA]
 ...
 [1.0 MyNormalZ]
 }
 }

texture {
 aoi
 texture_map {
 [0.0 MyTextureA]
 ...
 [1.0 MyTextureZ]
 }
 }
```

It gives a value proportional to the angle between the ray and the surface; for consistency with the slope pattern, values range from 0.5 where ray is tangent to the surface, to 1.0 where perpendicular; in practice, values below 0.5 may occur in conjunction with smooth triangles or meshes.

Note: This differs from the current MegaPOV implementation, where the values range from 0.5 down to 0.0 instead. If compatibility with MegaPOV is desired, it is recommended to mirror the gradient at 0.5, e.g.:

```

pigment {
 aoi
 pigment_map {
 [0.0 MyPigment3]
 [0.2 MyPigment2]
 [0.5 MyPigment1]
 [0.8 MyPigment2]
 [1.0 MyPigment3]
 }
 }
```

3.4.7.3.2 Slope Pattern

The `slope` pattern uses the normal of a surface to calculate the slope
at a given point. It then creates the pattern value dependent on the slope and optionally
the altitude. It can be used for pigments, normals and textures, but not for media densities.For pigments the syntax is:

```

pigment {
 slope {
 <Direction> [, Lo_slope, Hi_slope ]
 [ altitude <Altitude> [, Lo_alt, Hi_alt ]]
 }
 [PIGMENT_MODIFIERS...]
 }
```

The slope value at a given point is dependent on the angle between the `<Direction>` vector and the normal of the surface at that point.

For example:

 When the surface normal points in the opposite direction of the `<Direction>` vector (180 degrees), the slope is 0.0.
 When the surface normal is perpendicular to the `<Direction>` vector (90 degrees), the slope is 0.5.
 When the surface normal is parallel to the `<Direction>` vector (0 degrees), the slope is 1.0.

When using the simplest variant of the syntax:

```

slope { <Direction> }
```

the pattern value for a given point is the same as the slope value. `<Direction>` is a 3-D vector and will usually be `<0,-1,0>` for landscapes, but any direction can be used.

By specifying `Lo_slope` and `Hi_slope` you get more control:

```

slope { <Direction>, Lo_slope, Hi_slope }
```

`Lo_slope` and `Hi_slope` specifies which range of slopes are used, so you can control which slope values return which pattern values. `Lo_slope` is the slope value that returns 0.0 and `Hi_slope` is the slope value that returns 1.0.

For example, if you have a height_field and `<Direction>` is set to `<0,-1,0>`, then the slope values would only range from 0.0 to 0.5 because height_fields cannot have overhangs. If you do not specify `Lo_slope` and `Hi_slope`, you should keep in mind that the texture for the flat (horizontal) areas must be set at 0.0 and the texture for the steep (vertical) areas at 0.5 when designing the texture_map. The part from 0.5 up to 1.0 is not used then. But, by setting `Lo_slope` and `Hi_slope` to 0.0 and 0.5 respectively, the slope range will be stretched over the entire map, and the texture_map can then be defined from 0.0 to 1.0.

By adding an optional `<Altitude>` vector:

```

slope {
 <Direction>
 altitude <Altitude>
 }
```

the pattern will be influenced not only by the slope but also by a special gradient. `<Altitude>` is a 3-D vector that specifies the direction of the gradient. When `<Altitude>` is specified, the pattern value is a weighted average of the slope value and the gradient value. The weights are the lengths of the vectors `<Direction>` and `<Altitude>`. So if `<Direction>` is much longer than `<Altitude>` it means that the slope has greater effect on the results than the gradient. If on the other hand `<Altitude>` is longer, it means that the gradient has more effect on the results than the slope.

When adding the `<Altitude>` vector, the default gradient is defined from 0 to 1 units along the specified axis. This is fine when your object is defined within this range, otherwise a correction is needed. This can be done with the optional `Lo_alt` and `Hi_alt` parameters:

```

slope {
 <Direction>
 altitude <Altitude>, Lo_alt, Hi_alt
 }
```

They define the range of the gradient along the axis defined by the <Altitude> vector.

For example, with an `<Altitude>` vector set to y and an object going from -3 to 2 on
the y axis, the `Lo_alt` and `Hi_alt` parameters should be set to -3 and 2 respectively.

Note: You should be aware of the following pitfalls when using the `slope` pattern.

You may use the turbulence keyword inside slope pattern definitions but it may cause unexpected results. Turbulence is a 3-D distortion of a pattern. Since slope is only defined on surfaces of objects, a 3-D turbulence is not applicable to the slope component. However, if you are using altitude, the altitude component of the pattern will be affected by turbulence.
If your object is larger than the range of altitude you have specified, you may experience unexpected discontinuities. In that case it is best to adjust the `Lo_alt` and `Hi_alt` values so they fit to your object.
The slope pattern does not work for the sky_sphere, because the sky_sphere is a background feature and does not have a surface. similarly, it does not work for media densities.

As of version 3.7 the `slope` pattern has been extended to specify a reference point instead of a direction; the new syntax variant is as follows:

```

slope {
 point_at <ReferencePoint> [, Lo_Slope, Hi_Slope ]
 }
```

Note: This variant currently does not allow for the `altitude` keyword to be used.

The functionality is similar to MegaPOV's `aoi <ReferencePoint>` pattern, except that the values are reversed, i.e. range from 0.0 for surfaces facing away from the point in question, to 1.0 for surfaces facing towards that point; thus, `slope { <Vector> }` and `slope { point_at <Vector>*VeryLargeNumber }` have virtually the same effect.

### 3.4.7.4 Special Patterns

Some patterns are not "real" patterns, but behave like patterns and are used in the same location as a regular pattern. They are `average` and `image`.

3.4.7.4.1 Average Pattern

Technically `average` is not a pattern type but it is listed here
because the syntax is similar to other patterns. Typically a pattern type specifies
how colors or normals are chosen from a `pigment_map`,
`texture_map`, `density_map`, or `normal_map`, however `average` tells POV-Ray to
average together all of the patterns you specify. Average was originally
designed to be used in a normal statement with a `normal_map` as a
method of specifying more than one normal pattern on the same surface.
However average may be used in a pigment statement with a `pigment_map` or in a texture statement with a `texture_map`
or media density with `density_map` to average colors too.

When used with pigments, the syntax is:

```

AVERAGED_PIGMENT:

pigment {
 pigment_map {
 PIGMENT_MAP_ENTRY...
 }
 }

PIGMENT_MAP_ENTRY:
[ [Weight] PIGMENT_BODY ]
```

Where `Weight` is an optional float value that
defaults to 1.0 if not specified. This weight value is the relative weight
applied to that pigment. Each PIGMENT_BODY is anything which can be
inside a `pigment{...}` statement. The `pigment`
keyword and `{}` braces need not be specified.

Note: The `[]` brackets are part of the actual
PIGMENT_MAP_ENTRY. They are not notational symbols denoting optional
parts. The brackets surround each entry in the `pigment_map`.

There may be from 2 to 256 entries in the map.

For example

```

pigment {
 average
 pigment_map {
 [1.0 Pigment_1]
 [2.0 Pigment_2]
 [0.5 Pigment_3]
 }
 }
```

All three pigments are evaluated. The weight values are multiplied by the
resulting color. They are then divided by the total of the weights which, in
this example is 3.5. When used with `texture_map` or `density_map` it works the same way.

When used with a `normal_map` in a normal statement, multiple
copies of the original surface normal are created and are perturbed by each
pattern. The perturbed normals are then weighted, added and normalized.

See the sections Pigment Maps and Pigment Lists, Normal Maps and Normal Lists, Texture Maps, and Density Maps and Density Lists for more information.

3.4.7.4.2 Image Pattern

Instead of placing the color of the image on the object like an `image_map`, an `image_pattern` specifies an entire texture item (color, pigment, normal or texture) based on the gray value at that point.

This gray-value is checked against a list and the corresponding item is then used for the texture at that particular point. For values between listed items, an averaged texture is calculated.

It takes a standard image specification and has one option, `use_alpha`, which works similar to `use_color` or `use_index`.

Note: See the section Using the Alpha Channel for some important information regarding the use of `image_pattern`.

Syntax:

```

PIGMENT:
 pigment {
 IMAGE_PATTERN
 color_map { COLOR_MAP_BODY } |
 colour_map { COLOR_MAP_BODY } |
 pigment_map { PIGMENT_MAP_BODY }
 }

NORMAL:
 normal {
 IMAGE_PATTERN [Bump_Size]
 normal_map { NORMAL_MAP_BODY }
 }

TEXTURE:
 texture {
 IMAGE_PATTERN
 texture_map { TEXTURE_MAP_BODY }
 }

IMAGE_PATTERN:
 image_pattern {
 BITMAP_TYPE "bitmap.ext" [gamma GAMMA] [premultiplied BOOL]
 [IMAGE_MAP_MODS...]
 }

IMAGE_MAP_MOD:
 map_type Type | once | interpolate Type | use_alpha
 ITEM_MAP_BODY:
 ITEM_MAP_IDENTIFIER | ITEM_MAP_ENTRY...
 ITEM_MAP_ENTRY:
 [ GRAY_VALUE ITEM_MAP_ENTRY... ]
```

It is also useful for creating texture masks, like the following:

```

texture {
 image_pattern { tga "image.tga" use_alpha }
 texture_map {
 [0 Mytex ]
 [1 pigment { transmit 1 } ]
 }
 }
```

Note: This pattern uses an image to get the gray values from. If you want exactly the
same possibilities but need to get gray values from a pigment, you can use the pigment_pattern.

While POV-Ray will normally interpret the image pattern input file as a container of linear data irregardless of file type, this can be overridden for any individual image pattern input file by specifying `gamma` GAMMA immediately after the file name. For example:

```

image_pattern {
 jpeg "foobar.jpg" gamma 1.8
 }
```

This will cause POV-Ray to perform gamma adjustment or -decoding on the input file data before building the image pattern. Alternatively to a numerical value, `srgb` may be specified to denote that the file format is pre-corrected or encoded using the sRGB transfer function instead of a power-law gamma function. See section Gamma Handling for more information on gamma.

### 3.4.7.5 Pattern Modifiers

Pattern modifiers are statements or parameters which modify how a pattern
is evaluated or tells what to do with the pattern. The complete syntax
is:

```

PATTERN_MODIFIER:
 BLEND_MAP_MODIFIER | AGATE_MODIFIER | DENSITY_FILE_MODIFIER |
 QUILTED_MODIFIER | BRICK_MODIFIER | SLOPE_MODIFIER |
 noise_generator Number| turbulence <Amount> |
 octaves Count | omega Amount | lambda Amount |
 warp { [WARP_ITEMS...] } | TRANSFORMATION
BLEND_MAP_MODIFIER:
 frequency Amount | phase Amount | ramp_wave | triangle_wave |
 sine_wave | scallop_wave | cubic_wave | poly_wave [Exponent]
AGATE_MODIFIER:
 agate_turb Value
BRICK_MODIFIER:
 brick_size Size | mortar Size
DENSITY_FILE_MODIFIER:
 interpolate Type
SLOPE_MODIFIERS:
 <Altitude>
 <Lo_slope,Hi_slope>
 <Lo_alt,Hi_alt>
QUILTED_MODIFIER:
 control0 Value | control1 Value
PIGMENT_MODIFIER:
 PATTERN_MODIFIER | COLOR_LIST | PIGMENT_LIST |
 color_map { COLOR_MAP_BODY } | colour_map { COLOR_MAP_BODY } |
 pigment_map{ PIGMENT_MAP_BODY } | quick_color COLOR |
 quick_colour COLOR
COLOR NORMAL_MODIFIER:
 PATTERN_MODIFIER | NORMAL_LIST |
 normal_map { NORMAL_MAP_BODY } | slope_map{ SLOPE_MAP_BODY } |
 bump_size Amount
TEXTURE_PATTERN_MODIFIER:
 PATTERN_MODIFIER | TEXTURE_LIST |
 texture_map{ TEXTURE_MAP_BODY }
DENSITY_MODIFIER:
 PATTERN_MODIFIER | DENSITY_LIST | COLOR_LIST |
 color_map { COLOR_MAP_BODY } | colour_map { COLOR_MAP_BODY } |
 density_map { DENSITY_MAP_BODY }
```

Default values for pattern modifiers:

```

dist_exp : 0
falloff : 2.0
frequency : 1.0
lambda : 2.0
major_radius : 1
map_type : 0
noise_generator : 2
octaves : 6
omega : 0.5
orientation : <0,0,1>
phase : 0.0
poly_wave : 1.0
strength : 1.0
turbulence : <0,0,0>
```

The modifiers PIGMENT_LIST, `quick_color`, and `pigment_map` apply only to pigments. See the section Pigment for details on these pigment-specific pattern modifiers.

The modifiers COLOR_LIST and `color_map` apply only to pigments and densities. See the sections Pigment and Density for details on these pigment-specific pattern modifiers.

The modifiers NORMAL_LIST, `bump_size`, `slope_map` and `normal_map` apply only to normals. See the section Normal for details on these normal-specific pattern
modifiers.

The TEXTURE_LIST and `texture_map` modifiers can only be used with patterned textures. See the section Texture Maps for details.

The DENSITY_LIST and `density_map` modifiers only work with `media{density{..}}` statements. See the section Density for details.

The `agate_turb` modifier can only be used with the `agate` pattern. See the section Agate for details.

The `brick_size` and `mortar` modifiers can only be used with the `brick` pattern. See the section Brick for details.

The `control0` and `control1` modifiers can only be used with the `quilted` pattern. See the section Quilted for details.

The `interpolate` modifier can only be used with the `density_file` pattern. See the section Density File for details.

The general purpose pattern modifiers in the following sections can be used with `pigment`, `normal`, `texture`, or `density` patterns.

3.4.7.5.1 Transforming Patterns

The most common pattern modifiers are the transformation modifiers `translate`, `rotate`, `scale`, `transform`, and `matrix`. For details on these commands see the section Transformations.

These modifiers may be placed inside pigment, normal, texture, and density
statements to change the position, size and orientation of the patterns.

Transformations are performed in the order in which you specify them.
However in general the order of transformations relative to other pattern
modifiers such as `turbulence`, `color_map` and other
maps is not important. For example scaling before or after turbulence makes
no difference. The turbulence is done first, then the scaling regardless of
which is specified first. However the order in which transformations are
performed relative to `warp` statements is important. See the section Warp for details.

3.4.7.5.2 Frequency and Phase

The `frequency` and `phase` modifiers act as a type
of scale and translate modifiers for various blend maps. They only have
effect when blend maps are used. Blend maps are `color_map`,
`pigment_map`, `normal_map`, `slope_map`,
`density_map`, and `texture_map`. This discussion uses
a color map as an example but the same principles apply to the other blend
map types.

The `frequency` keyword adjusts the number of times that a color
map repeats over one cycle of a pattern. For example `gradient` covers color map values 0 to 1 over the range from x=0 to x=1. By adding `frequency 2.0` the color map repeats twice over that same range. The same effect can be achieved using `scale 0.5*x` so the frequency keyword is not that useful for patterns like gradient.

However the radial pattern wraps the color map around the +y-axis once. If
you wanted two copies of the map (or 3 or 10 or 100) you would have to build
a bigger map. Adding `frequency 2.0` causes the color map to be
used twice per revolution. Try this:

```

pigment {
 radial
 color_map{
 [0.5 color Red]
 [0.5 color White]
 }
 frequency 6
 }
```

The result is six sets of red and white radial stripes evenly spaced
around the object.

The float after `frequency` can be any value. Values greater than
1.0 causes more than one copy of the map to be used. Values from 0.0 to 1.0
cause a fraction of the map to be used. Negative values reverses the map.

The `phase` value causes the map entries to be shifted so that the map starts and ends at a different place. In the example above if you render successive frames at `phase 0` then `phase 0.1`, `phase 0.2`, etc. you could create an animation that rotates the stripes. The same effect can be easily achieved by rotating the `radial` pigment using `rotate y*Angle` but there are other uses where phase can be handy.

Sometimes you create a great looking gradient or wood color map but you want
the grain slightly adjusted in or out. You could re-order the color map
entries but that is a pain. A phase adjustment will shift everything but
keep the same scale. Try animating a `mandel` pigment for a color
palette rotation effect.

These values work by applying the following formula

 New_Value = fmod ( Old_Value * Frequency + Phase, 1.0 ).

The `frequency` and `phase` modifiers have no effect on block patterns `checker`, `brick`, and `hexagon` nor do they effect `image_map`, `bump_map` or `material_map`. They also have no effect in normal statements when used with `bumps`, `dents`, `quilted` or `wrinkles` because these normal patterns cannot use `normal_map` or `slope_map`.

They can be used with normal patterns `ripples` and `waves` even though these two patterns cannot use `normal_map` or `slope_map` either. When used with `ripples` or `waves`, `frequency` adjusts the space between features and `phase` can be adjusted from 0.0 to 1.0 to cause the ripples or waves to move relative to their center for animating the features.

3.4.7.5.3 Waveforms

POV-Ray allows you to apply various wave forms to the pattern function
before applying it to a blend map. Blend maps are `color_map`,
`pigment_map`, `normal_map`, `slope_map`,
`density_map`, and `texture_map`.

Most of the patterns which use a blend map, use the entries in the map in
order from 0.0 to 1.0. The effect can most easily be seen when these patterns
are used as normal patterns with no maps. Patterns such as `gradient` or `onion` generate a groove or slot that looks
like a ramp that drops off sharply. This is called a `ramp_wave`
wave type and it is the default wave type for most patterns. However the
`wood` and `marble` patterns use the map from 0.0 to
1.0 and then reverses it and runs it from 1.0 to 0.0. The result is a wave
form which slopes upwards to a peak, then slopes down again in a `triangle_wave`. In earlier versions of POV-Ray there was no way to
change the wave types. You could simulate a triangle wave on a ramp wave
pattern by duplicating the map entries in reverse, however there was no way
to use a ramp wave on wood or marble.

Now any pattern that takes a map can have the default wave type overridden.
For example:

```

pigment { wood color_map { MyMap } ramp_wave }
```

Also available are `sine_wave`, `scallop_wave`,
`cubic_wave` and `poly_wave` types. These types are of
most use in normal patterns as a type of built-in slope map. The `sine_wave` takes the zig-zag of a ramp wave and turns it into a gentle
rolling wave with smooth transitions. The `scallop_wave` uses the
absolute value of the sine wave which looks like corduroy when scaled small
or like a stack of cylinders when scaled larger. The `cubic_wave`
is a gentle cubic curve from 0.0 to 1.0 with zero slope at the start and end.
The `poly_wave` is an exponential function. It is followed by an
optional float value which specifies exponent. For example `poly_wave
2` starts low and climbs rapidly at the end while `poly_wave
0.5` climbs rapidly at first and levels off at the end. If no float
value is specified, the default is 1.0 which produces a linear function
identical to `ramp_wave`.

Although any of these wave types can be used for pigments, normals,
textures, or density the effect of many of the wave types are not as
noticeable on pigments, textures, or density as they are for normals.

Wave type modifiers have no effect on block patterns `checker`,
`brick`, `object` and `hexagon` nor do they effect `image_map`, `bump_map` or `material_map`. They
also have no effect in normal statements when used with `bumps`,
`dents`, `quilted`, `ripples`, `waves`, or `wrinkles` because these normal patterns cannot
use `normal_map` or `slope_map`.

3.4.7.5.4 Noise Generators

 There are three noise generators implemented. Changing the `noise_generator` will change
the appearance of noise based patterns, like bozo and granite.

`noise_generator 1` the noise that was used in POV_Ray 3.1
`noise_generator 2` range corrected version of the old noise, it does not show
the plateaus seen with `noise_generator 1`
`noise_generator 3` generates Perlin noise

The default is `noise_generator 2`

Note: The noise_generator can also be set in `global_settings`

3.4.7.5.5 Warp

The `warp` statement is a pattern modifier that is similar to
turbulence. Turbulence works by taking the pattern evaluation point and
pushing it about in a series of random steps. However warps push the point in
very well-defined, non-random, geometric ways. The `warp`
statement also overcomes some limitations of traditional turbulence and
transformations by giving the user more control over the order in which
turbulence, transformation and warp modifiers are applied to the pattern.

The turbulence warp provides an alternative way to
specify turbulence. The others modify the pattern in geometric ways.

The syntax for using a `warp` statement is:

```

WARP:
 warp { WARP_ITEM }
WARP_ITEM:
 repeat <Direction> [REPEAT_ITEMS...] |
 black_hole <Location>, Radius [BLACK_HOLE_ITEMS...] |
 turbulence <Amount> [TURB_ITEMS...]
 cylindrical [ orientation VECTOR | dist_exp FLOAT ]
 spherical [ orientation VECTOR | dist_exp FLOAT ]
 toroidal [ orientation VECTOR | dist_exp FLOAT | major_radius FLOAT ]
 planar [ VECTOR , FLOAT ]
REPEAT_ITEMS:
 offset <Amount> |
 flip <Axis>
BLACK_HOLE_ITEMS:
 strength Strength | falloff Amount | inverse |
 repeat <Repeat> | turbulence <Amount>
TURB_ITEMS:
 octaves Count | omega Amount | lambda Amount
```

You may have as many separate warp statements as you like in each pattern.
The placement of warp statements relative to other modifiers such as `color_map` or `turbulence` is not important. However
placement of warp statements relative to each other and to transformations is
significant. Multiple warps and transformations are evaluated in the order in
which you specify them. For example if you translate, then warp or warp, then
translate, the results can be different.

3.4.7.5.5.1 Black Hole Warp

A `black_hole` warp is so named because of its similarity to
real black holes. Just like the real thing, you cannot actually see a black
hole. The only way to detect its presence is by the effect it has on things
that surround it.

Take, for example, a wood grain. Using POV-Ray's normal turbulence and
other texture modifier functions, you can get a nice, random appearance to
the grain. But in its randomness it is regular - it is regularly random!
Adding a black hole allows you to create a localized disturbance in a wood
grain in either one or multiple locations. The black hole can have the effect
of either sucking the surrounding texture into itself (like the real
thing) or pushing it away. In the latter case, applied to a wood
grain, it would look to the viewer as if there were a knothole in the wood.
In this text we use a wood grain regularly as an example, because it is
ideally suitable to explaining black holes. However, black holes may in fact
be used with any texture or pattern. The effect that the black hole has on
the texture can be specified. By default, it sucks with the
strength calculated exponentially (inverse-square). You can change this if
you like.

Black holes may be used anywhere a warp is permitted. The syntax is:

```

BLACK_HOLE_WARP:
 warp {
 black_hole <Location>, Radius
 [BLACK_HOLE_ITEMS...]
 }
BLACK_HOLE_ITEMS:
 strength Strength | falloff Amount | inverse | type Type |
 repeat <Repeat> | turbulence <Amount>
```

The minimal requirement is the `black_hole` keyword followed by
a vector `<Location>` followed by a comma and a
float `Radius`. Black holes effect all points within the
spherical region around the location and within the radius. This is
optionally followed by any number of other keywords which control how the
texture is warped.

The `falloff` keyword may be used with a float value to specify
the power by which the effect of the black hole falls off. The default is
two. The force of the black hole at any given point, before applying the
`strength` modifier, is as follows.

First, convert the distance from the point to the center to a proportion (0
to 1) that the point is from the edge of the black hole. A point on the
perimeter of the black hole will be 0.0; a point at the center will be 1.0; a
point exactly halfway will be 0.5, and so forth. Mentally you can consider
this to be a closeness factor. A closeness of 1.0 is as close as you can get
to the center (i.e. at the center), a closeness of 0.0 is as far away as you
can get from the center and still be inside the black hole and a closeness of
0.5 means the point is exactly halfway between the two.

Call this value c. Raise c to the power specified in `falloff`.
By default Falloff is 2, so this is c^2 or c squared. The resulting value is
the force of the black hole at that exact location and is used, after
applying the `strength` scaling factor as described below, to
determine how much the point is perturbed in space. For example, if c is 0.5
the force is 0.5^2 or 0.25. If c is 0.25 the force is 0.125. But if c is
exactly 1.0 the force is 1.0. Recall that as c gets smaller the point is
farther from the center of the black hole. Using the default power of 2, you
can see that as c reduces, the force reduces exponentially in an
inverse-square relationship. Put in plain English, it means that the force is
much stronger (by a power of two) towards the center than it is at the
outside.

By increasing `falloff`, you can increase the magnitude of the
falloff. A large value will mean points towards the perimeter will hardly be
affected at all and points towards the center will be affected strongly. A
value of 1.0 for `falloff` will mean that the effect is linear. A
point that is exactly halfway to the center of the black hole will be
affected by a force of exactly 0.5. A value of `falloff` of less
than one but greater than zero means that as you get closer to the outside,
the force increases rather than decreases. This can have some uses but there
is a side effect. Recall that the effect of a black hole ceases outside its
perimeter. This means that points just within the perimeter will be affected
strongly and those just outside not at all. This would lead to a visible
border, shaped as a sphere. A value for `falloff` of 0 would mean
that the force would be 1.0 for all points within the black hole, since any
number larger 0 raised to the power of 0 is 1.0.

The `strength` keyword may be specified with a float value to
give you a bit more control over how much a point is perturbed by the black
hole. Basically, the force of the black hole (as determined above) is
multiplied by the value of `strength`, which defaults to 1.0. If
you set strength to 0.5, for example, all points within the black hole will
be moved by only half as much as they would have been. If you set it to 2.0
they will be moved twice as much.

There is a rider to the latter example, though - the movement is clipped to
a maximum of the original distance from the center. That is to say, a point
that is 0.75 units from the center may only be moved by a maximum of 0.75
units either towards the center or away from it, regardless of the value of
`strength`. The result of this clipping is that you will have an
exclusion area near the center of the black hole where all points whose final
force value exceeded or equaled 1.0 were moved by a fixed amount.

If the `inverse` keyword is specified then the points
pushed away from the center instead of being pulled in.

The `repeat` keyword followed by a vector, allows you to simulate
the effect of many black holes without having to explicitly declare them.
Repeat is a vector that tells POV-Ray to use this black hole at multiple
locations. Using `repeat` logically divides your scene up into
cubes, the first being located at <0,0,0> and going to `<Repeat>`. Suppose your repeat vector was <1,5,2>. The
first cube would be from <0,0,0> to < 1,5,2>. This cube repeats,
so there would be one at < -1,-5,-2>, <1,5,2>, <2,10,4> and
so forth in all directions, ad infinitum.

When you use `repeat`, the center of the black hole does not
specify an absolute location in your scene but an offset into each block. It
is only possible to use positive offsets. Negative values will produce
undefined results.

Suppose your center was <0.5,1,0.25> and the repeat vector is
<2,2,2>. This gives us a block at < 0,0,0> and <2,2,2>,
etc. The centers of the black hole's for these blocks would be
<0,0,0> + < 0.5,1.0,0.25>, i. e. <0.5,1.0,0.25>, and <
2,2,2> + <0.5,1.0,0.25>, i. e. < 2,5,3.0,2.25>.

Due to the way repeats are calculated internally, there is a restriction on
the values you specify for the repeat vector. Basically, each black hole must
be totally enclosed within each block (or cube), with no part crossing into a
neighboring one. This means that, for each of the x, y and z dimensions, the
offset of the center may not be less than the radius, and the repeat value
for that dimension must be >=the center plus the radius since any other
values would allow the black hole to cross a boundary. Put another way, for
each of x, y and z

 Radius <= Offset or Center <= Repeat - Radius.

If the repeat vector in any dimension is too small to fit this criteria,
it will be increased and a warning message issued. If the center is less than
the radius it will also be moved but no message will be issued.

Note that none of the above should be read to mean that you cannot
overlap black holes. You most certainly can and in fact this can produce some
most useful effects. The restriction only applies to elements of the `same` black hole which is repeating. You can declare a second black
hole that also repeats and its elements can quite happily overlap the first
and causing the appropriate interactions. It is legal for the repeat value
for any dimension to be 0, meaning that POV-Ray will not repeat the black
hole in that direction.

The `turbulence` can only be used in a black hole with `repeat`. It allows an element of randomness to be inserted into the way
the black holes repeat, to cause a more natural look. A good example would be
an array of knotholes in wood - it would look rather artificial if each
knothole were an exact distance from the previous.

The `turbulence` vector is a measurement that is added to each
individual black hole in an array, after each axis of the vector is multiplied
by a different random amount ranging from 0 to 1. The resulting actual
position of the black hole's center for that particular repeat element is
random (but consistent, so renders will be repeatable) and somewhere within
the above coordinates. There is a rider on the use of turbulence, which
basically is the same as that of the repeat vector. You cannot specify a
value which would cause a black hole to potentially cross outside of its
particular block.

In summary: For each of x, y and z the offset of the center must be
>=radius and the value of the repeat must be >= center + radius +
turbulence. The exception being that repeat may be 0 for any dimension, which
means do not repeat in that direction.

Some examples are given by

```

warp {
 black_hole <0, 0, 0>, 0.5
 }

warp {
 black_hole <0.15, 0.125, 0>, 0.5
 falloff 7
 strength 1.0
 repeat <1.25, 1.25, 0>
 turbulence <0.25, 0.25, 0>
 inverse
 }

warp {
 black_hole <0, 0, 0>, 1.0
 falloff 2
 strength 2
 inverse
 }
```

3.4.7.5.5.2 Repeat Warp

The `repeat` warp causes a section of the pattern to be
repeated over and over. It takes a slice out of the pattern and makes
multiple copies of it side-by-side. The warp has many uses but was originally
designed to make it easy to model wood veneer textures. Veneer is made by
taking very thin slices from a log and placing them side-by-side on some
other backing material. You see side-by-side nearly identical ring patterns
but each will be a slice perhaps 1/32th of an inch deeper.

The syntax for a repeat warp is

```

REPEAT_WARP:
 warp { repeat <Direction> [REPEAT_ITEMS...] }
REPEAT_ITEMS:
 offset <Amount> | flip <Axis>
```

The `repeat` vector specifies the direction in which the
pattern repeats and the width of the repeated area. This vector must lie
entirely along an axis. In other words, two of its three components must be
0. For example

```

pigment {
 wood
 warp { repeat 2*x }
 }
```

which means that from x=0 to x=2 you get whatever the pattern usually is.
But from x=2 to x=4 you get the same thing exactly shifted two units over in
the x-direction. To evaluate it you simply take the x-coordinate modulo 2.
Unfortunately you get exact duplicates which is not very realistic. The
optional `offset` vector tells how much to translate the pattern
each time it repeats. For example

```

pigment {
 wood
 warp {repeat x*2 offset z*0.05}
 }
```

means that we slice the first copy from x=0 to x=2 at z=0 but at x=2 to
x=4 we offset to z=0.05. In the 4 to 6 interval we slice at z=0.10. At the
n-th copy we slice at 0.05 n z. Thus each copy is slightly different. There
are no restrictions on the offset vector.

Finally the `flip` vector causes the pattern to be flipped or
mirrored every other copy of the pattern. The first copy of the pattern in
the positive direction from the axis is not flipped. The next farther is, the
next is not, etc. The flip vector is a three component x, y, z vector but
each component is treated as a boolean value that tells if you should or
should not flip along a given axis. For example

```

pigment {
 wood
 warp {repeat 2*x flip <1,1,0>}
 }
```

means that every other copy of the pattern will be mirrored about the x-
and y- axis but not the z-axis. A non-zero value means flip and zero means do
not flip about that axis. The magnitude of the values in the flip vector
does not matter.

3.4.7.5.5.3 Turbulence Warp

Inside the `warp` statement, the keyword `turbulence` followed by a float or vector may be
used to stir up any `pigment`, `normal` or `density`. A number of
optional parameters may be used with turbulence to control how it is
computed. The syntax is:

```

TURBULENCE_ITEM:
 turbulence <Amount> | octaves Count | omega Amount | lambda Amount
```

Typical turbulence values range from the default 0.0, which is no
turbulence, to 1.0 or more, which is very turbulent. If a vector is specified
different amounts of turbulence are applied in the x-, y- and z-direction.
For example

```

turbulence <1.0, 0.6, 0.1>
```

has much turbulence in the x-direction, a moderate amount in the
y-direction and a small amount in the z-direction.

Turbulence uses a random noise function called DNoise. This is
similar to the noise used in the `bozo` pattern except that
instead of giving a single value it gives a direction. You can think of it as
the direction that the wind is blowing at that spot. Points close together
generate almost the same value but points far apart are randomly
different.

Turbulence uses DNoise to push a point around in several steps
called `octaves`. We locate the point we want to evaluate, then
push it around a bit using turbulence to get to a different point then look
up the color or pattern of the new point.

It says in effect Do not give me the color at this spot...
take a few random steps in different directions and give me that
color. Each step is typically half as long as the one before. For
example:

| The magnitude of these steps is controlled by the turbulence value. There are three additional parameters which control how turbulence is computed. They are octaves, lambda and omega. Each is optional, each is followed by a single float value, and each has no effect when there is no turbulence. | |
| | Turbulence random walk. |

3.4.7.5.5.4 Octaves

The `octaves` keyword may be followed by an integer value to
control the number of steps of turbulence that are computed. Legal values
range from 1 to <10. The default value of 6 is a fairly high value; you
will not see much change by setting it to a higher value because the extra
steps are too small. Float values are truncated to integer. Smaller numbers
of octaves give a gentler, wavy turbulence and computes faster. Higher
octaves create more jagged or fuzzy turbulence and takes longer to
compute.

3.4.7.5.5.5 Lambda

The `lambda` parameter controls how statistically different the
random move of an octave is compared to its previous octave. The default
value is 2.0 which is quite random. Values close to lambda 1.0 will
straighten out the randomness of the path in the diagram above. The zig-zag
steps in the calculation are in nearly the same direction. Higher values can
look more swirly under some circumstances.

3.4.7.5.5.6 Omega

The `omega` value controls how large each successive octave
step is compared to the previous value. Each successive octave of turbulence
is multiplied by the omega value. The default `omega 0.5` means
that each octave is 1/2 the size of the previous one. Higher omega values
mean that 2nd, 3rd, 4th and up octaves contribute more turbulence giving a
sharper, crinkly look while smaller omegas give a fuzzy kind of
turbulence that gets blurry in places.

3.4.7.5.5.7 Mapping using warps

With the `cylindrical, spherical` and `toroidal` warps you can wrap checkers,
bricks and other patterns around cylinders, spheres, tori and other objects. In essence, these
warps use the same mapping as the image maps use.

The syntax is as follows:

```

CYLINDRICAL_WARP:
 warp { cylindrical [CYLINDRICAL_ITEMS...]}
CYLINDRICAL_ITEMS:
 orientation VECTOR | dist_exp FLOAT
SPHERICAL_WARP:
 warp { spherical [SPHERICAL_ITEMS...]}
SPHERICAL_ITEMS:
 orientation VECTOR | dist_exp FLOAT
TOROIDAL_WARP:
 warp { toroidal [TOROIDAL_ITEMS...]}
TOROIDAL_ITEMS:
 orientation VECTOR | dist_exp FLOAT | major_radius FLOAT
PLANAR_WARP:
 warp { planar [ VECTOR , FLOAT ]}
CUBIC_WARP:
 warp { cubic }
```

These defaults are in affect:

```

orientation <0,0,1>
dist_exp 0
major_radius 1
```

Although these warps do 3D mapping, some concession had to be made on depth.

The distance exponent is controlled by using the `dist_exp` keyword. When using the default value of 0, imagine a box from <0,0> to <1,1> stretching to infinity along the orientation vector.

The `distance` keyword is evaluated as follows:

 `sphere`: distance from origin
 `cylinder`: distance from y-axis
 `torus`: distance from major radius

The `planar` warp was made to make a pattern act like an image_map, of infinite size and can be useful in combination with other mapping-warps. By default the pigment in the XY-plane is extruded along
the Z-axis. The pigment can be taken from an other plane, by specifying the optional vector (normal of the plane) and float (distance along the normal). The result, again, is extruded along the Z-axis.

The `cubic` warp requires no parameters, and maps an area in the x-y plane between <0,0> and <1,1> around the origin in the same way as uv-mapping an origin-centered cube-shaped box would. The `cubic` warp works with any object whereas the uv-mapping only works for the box object. See the section on box uv-mapping for details.

The following code examples produced the images below:

```

torus {
 1, 0.5
 pigment {
 hexagon
 scale 0.1
 warp {
 toroidal
 orientation y
 dist_exp 1
 major_radius 1
 }
 }
 }

sphere {
 0,1
 pigment {
 hexagon
 scale <0.5/pi,0.25/pi,1>*0.1
 warp {
 spherical
 orientation y
 dist_exp 1
 }
 }
 }

cylinder {
 -y, y, 1
 pigment {
 hexagon
 scale <0.5/pi, 1, 1>*0.1
 warp {
 cylindrical
 orientation y
 dist_exp 1
 }
 }
 }
```

| cylindrical warp | spherical warp | toroidal warp |

3.4.7.5.5.8 Turbulence versus Turbulence Warp

The POV-Ray language contains an ambiguity and limitation on the way you
specify `turbulence` and transformations such as `translate`, `rotate`, `scale`, `matrix`, and `transform` transforms. Usually the turbulence
is done first. Then all translate, rotate, scale, matrix, and transform
operations are always done after turbulence regardless of the order in which
you specify them. For example this

```

pigment {
 wood
 scale .5
 turbulence .2
 }
```

works exactly the same as

```

pigment {
 wood
 turbulence .2
 scale .5
 }
```

The turbulence is always first. A better example of this limitation is
with uneven turbulence and rotations.

```

pigment {
 wood
 turbulence 0.5*y
 rotate z*60
 }
// as compared to
pigment {
 wood
 rotate z*60
 turbulence 0.5*y
 }
```

The results will be the same either way even though you would think it
should look different.

We cannot change this basic behavior in POV-Ray now because lots of scenes
would potentially render differently if suddenly the order transformation vs.
turbulence mattered when in the past, it did not.

However, by specifying our turbulence inside warp statement you tell POV-Ray
that the order in which turbulence, transformations and other warps are
applied is significant. Here is an example of a turbulence warp.

```

warp { turbulence <0,1,1> octaves 3 lambda 1.5 omega 0.3 }
```

The significance is that this

```

pigment {
 wood
 translate <1,2,3> rotate x*45 scale 2
 warp { turbulence <0,1,1> octaves 3 lambda 1.5 omega 0.3 }
 }
```

produces different results than this...

```

pigment {
 wood
 warp { turbulence <0,1,1> octaves 3 lambda 1.5 omega 0.3 }
 translate <1,2,3> rotate x*45 scale 2
 }
```

You may specify turbulence without using a warp statement. However you
cannot control the order in which they are evaluated unless you put them in a
warp.

The evaluation rules are as follows:

First any turbulence not inside a warp statement is applied regardless
of the order in which it appears relative to warps or transformations.
Next each warp statement, translate, rotate, scale or matrix one-by-one,
is applied in the order the user specifies. If you want turbulence done in a
specific order, you simply specify it inside a warp in the proper place.

3.4.7.5.5.9 Turbulence

The `turbulence` pattern modifier is still supported for compatibility
issues, but it is better nowadays to use the warp `turbulence` feature,
which does not have turbulence's limitation in transformation order
(turbulence is always applied first, before any scale, translate or
rotate, whatever the order you specify). For a detailed discussion see
Turbulence versus Turbulence Warp

 The old-style turbulence is handled slightly differently when used with the
agate, marble, spiral1, spiral2, and wood textures.

### 3.4.7.6 Image Map

When all else fails and none of the pigment pattern types meets your needs you can use an `image_map` to wrap a 2-D bit-mapped image around your 3-D objects.

3.4.7.6.1 Specifying an Image Map

The syntax for an `image_map` is:

```

 IMAGE_MAP:
 pigment {
 image_map {
 [BITMAP_TYPE] "bitmap[.ext]" [gamma GAMMA] [premultiplied BOOL]
 [IMAGE_MAP_MODS...]
 }
 [PIGMENT_MODFIERS...]
 }
 IMAGE_MAP:
 pigment {
 image_map {
 FUNCTION_IMAGE
 }
 [PIGMENT_MODFIERS...]
 }
 BITMAP_TYPE:
 exr | gif | hdr | iff | jpeg | pgm | png | ppm | sys | tga | tiff
 IMAGE_MAP_MODS:
 map_type Type | once | interpolate Type |
 filter Palette, Amount | filter all Amount |
 transmit Palette, Amount | transmit all Amount
 FUNCTION_IMAGE:
 function I_WIDTH, I_HEIGHT { FUNCTION_IMAGE_BODY }
 FUNCTION_IMAGE_BODY:
 PIGMENT | FN_FLOAT | pattern { PATTERN [PATTERN_MODIFIERS] }
```

After the optional BITMAP_TYPE keyword is a string expression containing the name of a bitmapped image file of the specified type. If the BITMAP_TYPE is not given, the same type is expected as the type set for output.

For example:

```

plane { -z,0
 pigment {
 image_map {png "Eggs.png"}
 }
 }

plane { -z,0
 pigment {
 image_map {"Eggs"}
 }
 }
```

The second method will look for, and use "Eggs.png" if the output file type is set to be `png` (Output_File_Type=N in INI-file or +FN on command line). It is particularly useful when the image used in the `image_map` is also rendered with POV-Ray.

Several optional modifiers may follow the file specification. The modifiers are described below.

Note: Earlier versions of POV-Ray allowed some modifiers before the BITMAP_TYPE but that syntax is being phased out in favor of the syntax described here.

Note: The `sys` format is a system-specific format. See the Output File Type section for more information.

Filenames specified in the `image_map` statements will be searched for in the home (current) directory first and, if not found, will then be searched for in directories specified by any `+L` or `Library_Path` options active. This would facilitate keeping all your image maps files in a separate subdirectory and giving a `Library_Path` option to specify where your library of image maps are. See Library Paths for details.

By default, the image is mapped onto the x-y-plane. The image is projected onto the object as though there were a slide projector somewhere in the -z-direction. The image exactly fills the square area from (x,y) coordinates (0,0) to (1,1) regardless of the image's original size in pixels. If you would like to change this default you may translate, rotate or scale the pigment or texture to map it onto the object's surface as desired.

In the section Checker, the `checker` pigment pattern is explained. The checks are described as solid cubes of colored clay from which objects are carved. With image maps you should imagine that each pixel is a long, thin, square, colored rod that extends parallel to the z-axis. The image is made from rows and columns of these rods bundled together and the object is then carved from the bundle.

If you would like to change this default orientation you may translate, rotate or scale the pigment or texture to map it onto the object's surface as desired.

The file name is optionally followed by one or more BITMAP_MODIFIERS. The `filter`, `filter all`, `transmit`, and `transmit all` modifiers are specific to image maps and are discussed in the following sections. An `image_map` may also use generic bitmap modifiers `map_type`,
`once` and `interpolate` described in Bitmap Modifiers

3.4.7.6.2 The Gamma Option

The default gamma handling rules for any image input file can be overridden by specifying `gamma` GAMMA immediately after the file name. For example:

```

image_map {
 jpeg "foobar.jpg" gamma 1.8
 interpolate 2
 }
```

Alternatively to a numerical value, `srgb` may be specified to denote that the file is encoded or pre-corrected using the sRGB transfer function instead of a power-law gamma function.

See section Gamma Handling for more information on gamma.

3.4.7.6.3 The Filter and Transmit Bitmap Modifiers

To make all or part of an image map transparent you can specify `filter` and/or `transmit` values for the color palette/registers of PNG, GIF or IFF pictures (at least for the modes that use palettes). You can do this by adding the keyword `filter` or `transmit` following the filename. The keyword is followed by two numbers. The first number is the palette number value and the second is the amount of transparency. The values should be separated by a comma. For
example:

```

image_map {
 gif "mypic.gif"
 filter 0, 0.5 // Make color 0 50% filtered transparent
 filter 5, 1.0 // Make color 5 100% filtered transparent
 transmit 8, 0.3 // Make color 8 30% non-filtered transparent
 }
```

You can give the entire image a `filter` or `transmit` value using `filter all` `Amount` or `transmit all` `Amount`. For example:

```

image_map {
 gif "stnglass.gif"
 filter all 0.9
 }
```

Note: Early versions of POV-Ray used the keyword `alpha` to specify filtered transparency however that word is often used to describe non-filtered transparency. For this reason `alpha` is no longer
used.

See the section Color Expressions for details on the differences between filtered and non-filtered transparency.

3.4.7.6.4 Using the Alpha Channel

Another way to specify non-filtered transmit transparency in an image map is by using the alpha channel. POV-Ray will automatically use the alpha channel for transmittance when one is stored in the image. PNG file format allows you to store a
different transparency for each color index in the PNG file, if desired. If your paint programs support this feature of PNG you can do the transparency editing within your paint program rather than specifying transmit values for each color in the POV file. Since PNG and TGA image formats can also store full alpha channel (transparency) information you can generate image maps that have transparency which is not dependent on the color of a pixel but rather its location in the image.

Although POV uses `transmit 0.0` to specify no transparency and `1.0` to specify full transparency, the alpha data ranges from 0 to 255 in the opposite direction. Alpha data 0 means the same as `transmit 1.0` and alpha data 255 produces `transmit 0.0`.

Note: In version 3.7 alpha handling for image file output has changed. Effectively, the background now requires a `filter` or `transmit` value in order for alpha transparency to work properly.

Previous versions of POV-Ray always expected straight alpha for file input, this has been changed on a per-file-format basis as follows:

 PNG will use straight alpha as per specification.
 OpenEXR and TIFF will use associated alpha as per specifications.
 TGA and BMP 32-bit RGBA will use straight alpha, retaining file input compatibility for now, until a final decision has been made on these formats.

Additionally the `premultiplied` parameter may be used to specify the input image alpha handling. This boolean parameter specifies whether the file is stored in premultiplied associated or non-premultiplied straight alpha format, overriding the file format specific default. This keyword has no effect on files without an alpha channel. Like the `gamma`, it MUST immediately follow the filename, though the order does not matter.

Note: The following mechanism has some limitations with colored highlights.

When generating non-premultiplied alpha output to a classic low-dynamic-range file format (e.g. PNG), transparency of particularly bright areas will now be reduced, in order to better preserve highlights on transparent objects.

Note: When using an input image in a `material_map`, `bump_map`, or `image_pattern` definition, the following conditions apply.

 For material maps, no alpha premultiplication handling is done whatsoever, instead the data as stored in the file is used.
 For bump maps and image patterns, images with an alpha channel are treated as if they had a black background, unless the alpha channel itself is used.

Note: See also `background` and `sky_sphere` for additional information.

Activating alpha output via `Output_Alpha=on` or `+UA`, when used with unsupported file formats generates a warning.

### 3.4.7.7 Bitmap Modifiers

A bitmap modifier is a modifier used inside an `image_map`,
`bump_map` or `material_map` to specify how the 2-D
bitmap is to be applied to the 3-D surface. Several bitmap modifiers apply to
specific kinds of maps and they are covered in the appropriate sections. The
bitmap modifiers discussed in the following sections are applicable to all
three types of bitmaps.

3.4.7.7.1 The once Option

Normally there are an infinite number of repeating image maps, bump maps
or material maps created over every unit square of the x-y-plane like tiles.
By adding the `once` keyword after a file name you can eliminate
all other copies of the map except the one at (0,0) to (1,1). In image maps,
areas outside this unit square are treated as fully transparent. In bump
maps, areas outside this unit square are left flat with no normal
modification. In material maps, areas outside this unit square are textured
with the first texture of the texture list.

For example:

```

image_map {
 gif "mypic.gif"
 once
 }
```

3.4.7.7.2 The map_type Option

The default projection of the image onto the x-y-plane is called a
planar map type. This option may be changed by adding the `map_type` keyword followed by an integer number specifying the way to
wrap the image around the object.

A `map_type 0` gives the default planar mapping already
described.

A `map_type 1` gives a spherical mapping. It assumes that the
object is a sphere of any size sitting at the origin. The y-axis is the
north/south pole of the spherical mapping. The top and bottom edges of the
image just touch the pole regardless of any scaling. The left edge of the
image begins at the positive x-axis and wraps the image around the sphere
from west to east in a -y-rotation. The image covers the sphere exactly once.
The `once` keyword has no meaning for this mapping type.

With `map_type 2` you get a cylindrical mapping. It assumes that
a cylinder of any diameter lies along the y-axis. The image wraps around the
cylinder just like the spherical map but the image remains one unit tall from
y=0 to y=1. This band of color is repeated at all heights unless the `once` keyword is applied.

Finally `map_type 5` is a torus or donut shaped mapping. It
assumes that a torus of major radius one sits at the origin in the x-z-plane.
The image is wrapped around similar to spherical or cylindrical maps. However
the top and bottom edges of the map wrap over and under the torus where they
meet each other on the inner rim.

Types 3 and 4 are still under development.

Note: The `map_type` option may also be applied to `bump_map` and `material_map` statements.

For example:

```

sphere{<0,0,0>,1
 pigment{
 image_map {
 gif "world.gif"
 map_type 1
 }
 }
 }
```

3.4.7.7.3 The interpolate Option

Adding the `interpolate` keyword can smooth the jagged look of a bitmap. When POV-Ray checks a color for an image map or a bump amount for a bump map, it often checks a point that is not directly on top of one pixel but sort of between several differently colored pixels. Interpolations return an in-between value so that the steps between the pixels in the map will look smoother.

Although `interpolate` is legal in material maps, the color index is interpolated before the texture is chosen. It does not interpolate the final color as you might hope it would. In general, interpolation of material maps serves no useful purpose but this may be fixed in future versions.

There are currently three types of interpolation: `interpolate 2` gives bilinear interpolation, `interpolate 3` gives bicubic, and `interpolate 4` gives normalized distance.

For example:

```

image_map {
 gif "mypic.gif"
 interpolate 2
 }
```

The default is no interpolation. Normalized distance is the slowest, bilinear does a better job of picking the between color, and arguably, bicubic interpolation is a slight improvement, however it is subject to over-sharpening at some color borders. Normally bilinear is used.

If your map looks jagged, try using interpolation instead of going to a higher resolution image. The results can be very good.
