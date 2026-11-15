## 3.4.6 Textures

## 3.4.6 Texture

The `texture` statement is an object modifier which describes
what the surface of an object looks like, i.e. its material. Textures are
combinations of pigments, normals, and finishes. Pigment is the color or
pattern of colors inherent in the material. Normal is a method of simulating
various patterns of bumps, dents, ripples or waves by modifying the surface
normal vector. Finish describes the reflective properties of a material.

Note: In previous versions of POV-Ray, the texture also contained
information about the interior of an object. This information has been moved
to a separate object modifier called `interior`. See
Interior for details.

There are three basic kinds of textures: plain, patterned, and layered. A
plain texture consists of a single pigment, an optional normal, and
a single finish. A patterned texture combines two or more textures
using a block pattern or blending function pattern. Patterned textures may be
made quite complex by nesting patterns within patterns. At the innermost
levels however, they are made up from plain textures. A layered
texture consists of two or more semi-transparent textures layered on top
of one another.

Note: Although we call a plain texture plain it
may be a very complex texture with patterned pigments and normals. The term
plain only means that it has a single pigment, normal, and
finish.

The syntax for `texture` is as follows:

```

TEXTURE:
 PLAIN_TEXTURE | PATTERNED_TEXTURE | LAYERED_TEXTURE
PLAIN_TEXTURE:
 texture {
 [TEXTURE_IDENTIFIER]
 [PNF_IDENTIFIER...]
 [PNF_ITEMS...]
 }
PNF_IDENTIFIER:
 PIGMENT_IDENTIFIER | NORMAL_IDENTIFIER | FINISH_IDENTIFIER
PNF_ITEMS:
 PIGMENT | NORMAL | FINISH | TRANSFORMATION
LAYERED_TEXTURE:
 NON_PATTERNED_TEXTURE...
PATTERNED_TEXTURE:
 texture {
 [PATTERNED_TEXTURE_ID]
 [TRANSFORMATIONS...]
 } |
 texture {
 PATTERN_TYPE
 [TEXTURE_PATTERN_MODIFIERS...]
 } |
 texture {
 tiles TEXTURE tile2 TEXTURE
 [TRANSFORMATIONS...]
 } |
 texture {
 material_map {
 BITMAP_TYPE "bitmap.ext"
 [MATERIAL_MODS...] TEXTURE... [TRANSFORMATIONS...]
 }
 }
TEXTURE_PATTERN_MODIFIER:
 PATTERN_MODIFIER | TEXTURE_LIST |
 texture_map { TEXTURE_MAP_BODY }
```

In the PLAIN_TEXTURE, each of the items are optional but if they are present the TEXTURE_IDENTIFIER must be first. If no texture identifier is given, then POV-Ray creates a copy of the default texture.

Next are optional pigment, normal, and/or finish identifiers which fully override any pigment, normal and finish already specified in the previous texture identifier or default texture. Typically this is used for backward compatibility to allow things like:

```

texture { MyPigment }
```

where `MyPigment` is a pigment identifier.

Finally we have optional `pigment`, `normal` or `finish` statements which modify any pigment, normal and finish already specified in the identifier. If no texture identifier is specified the `pigment`, `normal` and `finish` statements modify the current default values. This is the typical plain texture:

```

texture {
 pigment { MyPigment }
 normal { MyNormal }
 finish { MyFinish }
 scale SoBig
 rotate SoMuch
 translate SoFar
 }
```

The TRANSFORMATIONS may be interspersed between the pigment, normal and finish statements but are generally specified last. If they are interspersed, then they modify only those parts of the texture already specified. For example:

```

texture {
 pigment { MyPigment }
 scale SoBig //affects pigment only
 normal { MyNormal }
 rotate SoMuch //affects pigment and normal
 finish { MyFinish }
 translate SoFar //finish is never transformable no matter what.
 //Therefore affects pigment and normal only
 }
```

Texture identifiers may be declared to make scene files more readable and to parameterize scenes so that changing a single declaration changes many values. An identifier is declared as follows.

```

TEXTURE_DECLARATION:
 #declare IDENTIFIER = TEXTURE |
 #local IDENTIFIER = TEXTURE
```

Where IDENTIFIER is the name of the identifier up to 40 characters long and TEXTURE is any valid `texture` statement. See #declare vs. #local for information on identifier scope.

The sections below describe all of the options available for Pigment, Normal, and Finish. They are the main part of plain
textures. There are also separate sections for Patterned Textures
and Layered Textures which are made up of plain textures.

Note: The `tiles` and
`material_map` versions of patterned textures are obsolete and are only supported for backwards compatibility.

### 3.4.6.1 Pigment

The color or pattern of colors for an object is defined by a `pigment` statement. All plain textures must have a pigment. If you do not specify one the default pigment is used. The color you define is the way you want the object to look if fully illuminated. You pick the basic color inherent in the object and POV-Ray brightens or darkens it depending on the lighting in the scene. The parameter is called `pigment` because we are defining the basic color the object actually is rather than how it looks.

The syntax for pigment is:

```

PIGMENT:
 pigment {
 [PIGMENT_IDENTIFIER]
 [PIGMENT_TYPE]
 [PIGMENT_MODIFIER...]
 }
PIGMENT_TYPE:
 PATTERN_TYPE | COLOR |
 image_map {
 BITMAP_TYPE "bitmap.ext" [IMAGE_MAP_MODS...]
 }
PIGMENT_MODIFIER:
 PATTERN_MODIFIER | COLOR_LIST | PIGMENT_LIST |
 color_map { COLOR_MAP_BODY } | colour_map { COLOR_MAP_BODY } |
 pigment_map { PIGMENT_MAP_BODY } | quick_color COLOR |
 quick_colour COLOR
```

Each of the items in a pigment are optional but if they are present, they must be in the order shown. Any items after the PIGMENT_IDENTIFIER modify or override settings given in the identifier. If no identifier is specified then the items modify the pigment values in the current default texture. The PIGMENT_TYPE fall into roughly four categories. Each category is discussed the sub-sections which follow. The four categories are solid color and `image_map` patterns which are specific to `pigment` statements or color list patterns, color mapped patterns which use POV-Ray's wide selection of general patterns. See Patterns for details about specific patterns.

The pattern type is optionally followed by one or more pigment modifiers. In addition to general pattern modifiers such as transformations, turbulence, and warp modifiers, pigments may also have a COLOR_LIST, PIGMENT_LIST, `color_map`, `pigment_map`, and `quick_color` which are specific to pigments. See Pattern Modifiers for information on general modifiers. The pigment-specific modifiers are described in sub-sections which follow. Pigment modifiers of any kind apply only to the pigment and not to other parts of the texture. Modifiers must be specified last.

A pigment statement is part of a `texture` specification. However it can be tedious to use a `texture` statement just to add a color to an object. Therefore you may attach a pigment directly to an object without explicitly specifying that it as part of a texture. For example instead of this:

```

object { My_Object texture {pigment { color Red } } }
```

you may shorten it to:

```

object { My_Object pigment {color Red } }
```

Doing so creates an entire `texture` structure with default `normal` and `finish` statements just as if you had explicitly typed the full `texture {...}` around it.

Note: an explicit texture statement is required, if you want to layer pigments.

Pigment identifiers may be declared to make scene files more readable and to parameterize scenes so that changing a single declaration changes many values. An identifier is declared as follows.

```

PIGMENT_DECLARATION:
 #declare IDENTIFIER = PIGMENT |
 #local IDENTIFIER = PIGMENT
```

Where IDENTIFIER is the name of the identifier up to 40 characters long and PIGMENT is any valid `pigment` statement. See #declare vs. #local for information on identifier scope.

3.4.6.1.1 Solid Color Pigments

The simplest type of pigment is a solid color. To specify a solid color you simply put a color specification inside a `pigment` statement. For example:

```

pigment { color Orange }
```

A color specification consists of the optional keyword `color` followed by a color identifier or by a specification of the amount of red, green, blue, filtered and unfiltered transparency in the surface. See section Specifying Colors for more details about colors. Any pattern modifiers used with a solid color are ignored because there is no pattern to modify.

3.4.6.1.4 Color List Pigments

There are four color list patterns: `checker`, `hexagon`, `brick` and `object`. The result is a pattern of solid colors with distinct edges rather than a blending of colors as with color
mapped patterns. Each of these patterns is covered in more detail in a later section. The syntax is:

```

COLOR_LIST_PIGMENT:
 pigment {brick [COLOR_1, [COLOR_2]] [PIGMENT_MODIFIERS...] }|
 pigment {checker [COLOR_1, [COLOR_2]] [PIGMENT_MODIFIERS...]}|
 pigment {
 hexagon [COLOR_1, [COLOR_2, [COLOR_3]]] [PIGMENT_MODIFIERS...]
 }|
 pigment {object OBJECT_IDENTIFIER | OBJECT {} [COLOR_1, COLOR_2]}
```

Each COLOR_n is any valid color specification. There should be a comma between each color or the `color` keyword should be used as a separator so that POV-Ray can determine where each color specification
starts and ends. The `brick` and `checker` pattern expects two colors and `hexagon` expects three. If an insufficient number of colors is specified then default colors are used.

3.4.6.1.5 Quick Color

When developing POV-Ray scenes it is often useful to do low quality test runs that render faster. The `+Q` command line switch or `Quality` INI option can be used to turn off some time consuming color pattern and lighting calculations to speed things up. See Quality Settings for details. However all settings of `+Q5` or `Quality=5` or lower turns off pigment calculations and creates gray objects.

By adding a `quick_color` to a pigment you tell POV-Ray what solid color to use for quick renders instead of a patterned pigment. For example:

```

pigment {
 gradient x
 color_map {
 [0.0 color Yellow]
 [0.3 color Cyan]
 [0.6 color Magenta]
 [1.0 color Cyan]
 }
 turbulence 0.5
 lambda 1.5
 omega 0.75
 octaves 8
 quick_color Neon_Pink
 }
```

This tells POV-Ray to use solid `Neon_Pink` for test runs at quality `+Q5` or lower but to use the turbulent gradient pattern for rendering at `+Q6` and higher. Solid color pigments such as

```

pigment {color Magenta}
```

automatically set the `quick_color` to that value. You may override this if you want. Suppose you have 10 spheres on the screen and all are yellow. If you want to identify them individually you could give each a different `quick_color`. Foe example:

```

sphere {
 <1,2,3>,4
 pigment { color Yellow quick_color Red }
 }

sphere {
 <-1,-2,-3>,4
 pigment { color Yellow quick_color Blue }
 }
```

and so on. At `+Q6` or higher they will all be yellow but at `+Q5` or lower each would be different colors so you could identify them.

The alternate spelling `quick_colour` is also supported.

3.4.6.1.2 Color Map

Most of the color patterns do not use abrupt color changes of just two or three colors like those in the brick, checker or hexagon patterns. They instead use smooth transitions of many colors that gradually change from one point to the next. The colors are defined in a pigment modifier called a `color_map` that describes how the pattern blends from one color to the next.

Each of the various pattern types available is in fact a mathematical function that takes any x, y, z location and turns it into a number between 0.0 and 1.0 inclusive. That number is used to specify what mix of colors to use from the color map.

The syntax for `color_map` is as follows:

```

COLOR_MAP:
 color_map { COLOR_MAP_BODY } | colour_map { COLOR_MAP_BODY }
COLOR_MAP_BODY:
 COLOR_MAP_IDENTIFIER | COLOR_MAP_ENTRY...
COLOR_MAP_ENTRY:
 [ Value COLOR ] |
 [ Value_1, Value_2 color COLOR_1 color COLOR_2 ]
```

Where each `Value_n` is a float values between 0.0 and 1.0 inclusive and each COLOR_n, is color specifications.

Note: The `[]` brackets are part of the actual
COLOR_MAP_ENTRY. They are not notational symbols denoting optional parts. The brackets surround each entry in the color map.

 There may be from 2 to 256 entries in the map. The alternate spelling `colour_map` may be used.

Here is an example:

```

sphere {
 <0,1,2>, 2
 pigment {
 gradient x //this is the PATTERN_TYPE
 color_map {
 [0.1 color Red]
 [0.3 color Yellow]
 [0.6 color Blue]
 [0.6 color Green]
 [0.8 color Cyan]
 }
 }
 }
```

The pattern function `gradient x` is evaluated and the result is a value from 0.0 to 1.0. If the value is less than the first entry (in this case 0.1) then the first color (red) is used. Values from 0.1 to 0.3 use a blend of red and yellow using linear interpolation of the two colors. Similarly values from 0.3 to 0.6 blend from yellow to blue.

The 3rd and 4th entries both have values of 0.6. This causes an immediate abrupt shift of color from blue to green. Specifically a value that is less than 0.6 will be blue but exactly equal to 0.6 will be green. Moving along, values from 0.6 to 0.8 will be a blend of green and cyan. Finally any value greater than or equal to 0.8 will be cyan.

If you want areas of unchanging color you simply specify the same color for two adjacent entries. For example:

```

color_map {
 [0.1 color Red]
 [0.3 color Yellow]
 [0.6 color Yellow]
 [0.8 color Green]
 }
```

In this case any value from 0.3 to 0.6 will be pure yellow.

The first syntax version of COLOR_MAP_ENTRY with one float and one color is the current standard. The other double entry version is obsolete and should be avoided. The previous example would look as follows using the old syntax.

```

color_map {
 [0.0 0.1 color Red color Red]
 [0.1 0.3 color Red color Yellow]
 [0.3 0.6 color Yellow color Yellow]
 [0.6.0.8 color Yellow color Green]
 [0.8 1.0 color Green color Green]
 }
```

You may use `color_map` with any patterns except `brick`, `checker`, `hexagon`, `object` and `image_map`. You may declare and use `color_map` identifiers. For example:

```

#declare Rainbow_Colors=
color_map {
 [0.0 color Magenta]
 [0.33 color Yellow]
 [0.67 color Cyan]
 [1.0 color Magenta]
 }
object {
 My_Object
 pigment {
 gradient x
 color_map { Rainbow_Colors }
 }
 }
```

3.4.6.1.3 Pigment Map

In addition to specifying blended colors with a color map you may create a blend of pigments using a `pigment_map`. The syntax for a pigment map is identical to a color map except you specify a pigment in each map
entry (and not a color).

The syntax for `pigment_map` is as follows:

```

PIGMENT_MAP:
 pigment_map { PIGMENT_MAP_BODY }
PIGMENT_MAP_BODY:
 PIGMENT_MAP_IDENTIFIER | PIGMENT_MAP_ENTRY...
PIGMENT_MAP_ENTRY:
 [ Value PIGMENT_BODY ]
```

Where `Value` is a float value between 0.0 and 1.0 inclusive and each PIGMENT_BODY is anything which can be inside a `pigment{...}` statement. The `pigment` keyword and
`{}` braces need not be specified.

Note: The `[]` brackets are part of the actual PIGMENT_MAP_ENTRY. They are not notational symbols denoting optional parts. The brackets surround each entry in the pigment map.

There may be from 2 to 256 entries in the map.

For example:

```

sphere {
 <0,1,2>, 2
 pigment {
 gradient x //this is the PATTERN_TYPE
 pigment_map {
 [0.3 wood scale 0.2]
 [0.3 Jade] //this is a pigment identifier
 [0.6 Jade]
 [0.9 marble turbulence 1]
 }
 }
 }
```

When the `gradient x` function returns values from 0.0 to 0.3 the scaled wood pigment is used. From 0.3 to 0.6 the pigment identifier Jade is used. From 0.6 up to 0.9 a blend of Jade and a turbulent marble is used. From 0.9 on up only the turbulent marble is used.

Pigment maps may be nested to any level of complexity you desire. The pigments in a map may have color maps or pigment maps or any type of pigment you want. Any entry of a pigment map may be a solid color however if all entries are solid colors you should use a `color_map` which will render slightly faster.

Entire pigments may also be used with the block patterns such as `checker`, `hexagon` and `brick`. For example:

```

pigment {
 checker
 pigment { Jade scale .8 }
 pigment { White_Marble scale .5 }
 }
```

Note: In the case of block patterns the `pigment` wrapping is required around the pigment information.

A pigment map is also used with the `average` pigment type. See
Average for details.

You may not use `pigment_map` or individual pigments with an `image_map`. See section Texture Maps for an alternative way to do this.

You may declare and use pigment map identifiers but the only way to declare a pigment block pattern list is to declare a pigment identifier for the entire pigment.

### 3.4.6.2 Normal

Ray-tracing is known for the dramatic way it depicts reflection, refraction and lighting effects. Much of our perception depends on the reflective properties of an object. Ray tracing can exploit this by playing tricks on our perception to make us see complex details that are not really there.

Suppose you wanted a very bumpy surface on the object. It would be very difficult to mathematically model lots of bumps. We can however simulate the way bumps look by altering the way light reflects off of the surface. Reflection calculations depend on a vector called a surface normal vector. This is a vector which points away from the surface and is perpendicular to it. By artificially modifying (or perturbing) this normal vector you can simulate bumps. This is done by adding an optional `normal` statement.

Note: Attaching a normal pattern does not really modify the surface. It only affects the way light reflects or refracts at the surface so that it looks bumpy.

 The syntax is:

```

NORMAL:
 normal { [NORMAL_IDENTIFIER] [NORMAL_TYPE] [NORMAL_MODIFIER...] }
NORMAL_TYPE:
 PATTERN_TYPE Amount |
 bump_map { BITMAP_TYPE "bitmap.ext" [BUMP_MAP_MODS...]}
NORMAL_MODIFIER:
 PATTERN_MODIFIER | NORMAL_LIST | normal_map { NORMAL_MAP_BODY } |
 slope_map{ SLOPE_MAP_BODY } | bump_size Amount |
 no_bump_scale Bool | accuracy Float
```

Each of the items in a normal are optional but if they are present, they must be in the order shown. Any items after the NORMAL_IDENTIFIER modify or override settings given in the identifier. If no identifier is specified then the items modify the normal values in the current default texture. The PATTERN_TYPE may optionally be followed by a float value that controls the apparent depth of the bumps. Typical values range from 0.0 to 1.0 but any value may be used. Negative values invert the
pattern. The default value if none is specified is 0.5.

There are four basic types of NORMAL_TYPEs. They are block pattern normals, continuous pattern normals, specialized normals and bump maps. They differ in the types of modifiers you may use with them. The pattern type is optionally followed by one or more normal modifiers. In addition to general pattern modifiers such as transformations, turbulence, and warp modifiers, normals may also have a NORMAL_LIST, `slope_map`, `normal_map`, and `bump_size` which are specific to normals. See Pattern Modifiers for information on general modifiers. The normal-specific modifiers are described in sub-sections which follow. Normal modifiers of any kind apply only to the normal and not to other parts of the texture. Modifiers must be specified last.

Originally POV-Ray had some patterns which were exclusively used for pigments while others were exclusively used for normals. Since POV-Ray 3.0 you can use any pattern for either pigments or normals. For example it is now valid to use `ripples` as a pigment or `wood` as a normal type. The patterns `bumps`, `dents`, `ripples`, `waves`, `wrinkles`, and `bump_map` were once exclusively normal patterns which could not be used as pigments. Because these six types use specialized normal modification calculations they cannot have `slope_map`, `normal_map` or wave shape modifiers. All other normal pattern types may use them. Because block patterns `checker`, `hexagon`, `object` and `brick` do not return a continuous series of values, they cannot use these modifiers either. See Patterns for details about specific patterns.

A `normal` statement is part of a `texture` specification. However it can be tedious to use a `texture` statement just to add bumps to an object. Therefore you may attach a normal directly to an object without explicitly specifying that it as part of a texture. For example instead of this:

```

object {My_Object texture { normal { bumps 0.5 } } }
```

you may shorten it to:

```

object { My_Object normal { bumps 0.5 } }
```

Doing so creates an entire `texture` structure with default `pigment` and `finish`
statements just as if you had explicitly typed the full `texture {...}` around it. Normal identifiers may be declared to make scene files more readable and to parameterize scenes so that changing a single declaration changes many values. An identifier is declared as follows.

```

NORMAL_DECLARATION:
 #declare IDENTIFIER = NORMAL |
 #local IDENTIFIER = NORMAL
```

Where IDENTIFIER is the name of the identifier up to 40 characters long and NORMAL is any valid `normal` statement. See #declare vs. #local for information on identifier scope.

3.4.6.2.4 Scaling normals

When scaling a normal, or when scaling an object after a normal is applied to it, the depth of the normal is affected by the scaling. This is not always wanted. If you want to turn off bump scaling for a texture or normal, you can do this by adding the keyword `no_bump_scale` to the texture's or normal's modifiers. This modifier will get passed on to all textures or normals contained in that texture or normal. Think of this like the way no_shadow gets passed on to objects contained in a CSG.

It is also important to note that if you add `no_bump_scale` to a normal or texture that is contained within another pattern (such as within a `texture_map` or `normal_map`), then the only scaling that will be ignored is the scaling of that texture or normal. Scaling of the parent texture or normal or of the object will affect the depth of the bumps, unless `no_bump_scale` is specified at the top-level of the texture (or normal, if the normal is not wrapped in a texture).

Note: See the section Using the Alpha Channel for some important information regarding the use of `bump_map`.

3.4.6.2.1 Normal Map

Most of the time you will apply single normal pattern to an entire surface but you may also create a pattern or blend of normals using a `normal_map`. The syntax for a `normal_map` is identical to a `pigment_map` except you specify a `normal` in each map entry. The syntax for `normal_map` is as follows:

```

NORMAL_MAP:
 normal_map { NORMAL_MAP_BODY }
NORMAL_MAP_BODY:
 NORMAL_MAP_IDENTIFIER | NORMAL_MAP_ENTRY...
NORMAL_MAP_ENTRY:
 [ Value NORMAL_BODY ]
```

Where `Value` is a float value between 0.0 and 1.0 inclusive and each NORMAL_BODY is anything which can be inside a `normal{...}` statement. The `normal` keyword and `{}` braces need not be specified.

Note: The `[]` brackets are part of the actual NORMAL_MAP_ENTRY. They are not notational symbols denoting optional parts. The brackets surround each entry in the normal map.

 There may be from 2 to 256 entries in the map.

For example:

```

normal {
 gradient x //this is the PATTERN_TYPE
 normal_map {
 [0.3 bumps scale 2]
 [0.3 dents]
 [0.6 dents]
 [0.9 marble turbulence 1]
 }
 }
```

When the `gradient x` function returns values from 0.0 to 0.3 then the scaled bumps normal is used. From 0.3 to 0.6 dents pattern is used. From 0.6 up to 0.9 a blend of dents and a turbulent marble is used. From 0.9 on up only the turbulent marble is used.

Normal maps may be nested to any level of complexity you desire. The normals in a map may have slope maps or normal maps or any type of normal you want.

A normal map is also used with the `average` normal type. See Average for details.

Entire normals in a normal list may also be used with the block patterns such as `checker`, `hexagon` and `brick`. For example:

```

normal {
 checker
 normal { gradient x scale .2 }
 normal { gradient y scale .2 }
 }
```

Note: In the case of block patterns the `normal` wrapping is required around the normal information.

You may not use `normal_map` or individual normals with a `bump_map`. See section Texture Maps for an alternative way to do this.

You may declare and use normal map identifiers but the only way to declare a normal block pattern list is to declare a normal identifier for the entire normal.

3.4.6.2.2 Slope Map

A `slope_map` is a normal pattern modifier which gives the user a great deal of control over the exact shape of the bumpy features. Each of the various pattern types available is in fact a mathematical function that takes any x, y, z location and turns it into a number between 0.0 and 1.0 inclusive. That number is used to specify where the various high and low spots are. The `slope_map` lets you further shape the contours. It is best illustrated with a gradient normal pattern. For example:

```

plane{ z, 0
 pigment{ White }
 normal { gradient x }
 }
```

Gives a ramp wave pattern that looks like small linear ramps that climb from the points at x=0 to x=1 and then abruptly drops to 0 again to repeat the ramp from x=1 to x=2. A slope map turns this simple linear ramp into almost any wave shape you want. The syntax is as follows:

```

SLOPE_MAP:
 slope_map { SLOPE_MAP_BODY }
SLOPE_MAP_BODY:
 SLOPE_MAP_IDENTIFIER | SLOPE_MAP_ENTRY...
SLOPE_MAP_ENTRY:
 [ Value, <Height, Slope> ]
```

Note: The `[]` brackets are part of the actual SLOPE_MAP_ENTRY. They are not notational symbols denoting optional parts. The brackets surround each entry in the slope map.

There may be from 2 to 256 entries in the map.

Each `Value` is a float value between 0.0 and 1.0 inclusive and each `<Height`, `Slope>` is a 2 component vector such as <0,1> where the first value represents the apparent height of the wave and the second value represents the slope of the wave at that point. The height should range between 0.0 and 1.0 but any value could be used.

The slope value is the change in height per unit of distance. For example a slope of zero means flat, a slope of 1.0 means slope upwards at a 45 degree angle and a slope of -1 means slope down at 45 degrees. Theoretically a slope straight up would have infinite slope. In practice, slope values should be kept in the range -3.0 to +3.0. Keep in mind that this is only the visually apparent slope. A normal does not actually change the surface.

For example here is how to make the ramp slope up for the first half and back down on the second half creating a triangle wave with a sharp peak in the center.

```

normal {
 gradient x // this is the PATTERN_TYPE
 slope_map {
 [0 <0, 1>] // start at bottom and slope up
 [0.5 <1, 1>] // halfway through reach top still climbing
 [0.5 <1,-1>] // abruptly slope down
 [1 <0,-1>] // finish on down slope at bottom
 }
}
```

The pattern function is evaluated and the result is a value from 0.0 to 1.0. The first entry says that at x=0 the apparent height is 0 and the slope is 1. At x=0.5 we are at height 1 and slope is still up at 1. The third entry also specifies that at x=0.5 (actually at some tiny fraction above 0.5) we have height 1 but slope -1 which is downwards. Finally at x=1 we are at height 0 again and still sloping down with slope -1.

Although this example connects the points using straight lines the shape is actually a cubic spline. This example creates a smooth sine wave.

```

normal {
 gradient x // this is the PATTERN_TYPE
 slope_map {
 [0 <0.5, 1>] // start in middle and slope up
 [0.25 <1.0, 0>] // flat slope at top of wave
 [0.5 <0.5,-1>] // slope down at mid point
 [0.75 <0.0, 0>] // flat slope at bottom
 [1 <0.5, 1>] // finish in middle and slope up
 }
}
```

This example starts at height 0.5 sloping up at slope 1. At a fourth of the way through we are at the top of the curve at height 1 with slope 0 which is flat. The space between these two is a gentle curve because the start and end slopes are different. At half way we are at half height sloping down to bottom out at 3/4ths. By the end we are climbing at slope 1 again to complete the cycle. There are more examples in `slopemap.pov` in the sample scenes.

A `slope_map` may be used with any pattern except `brick`, `checker`, `object`, `hexagon`, `bumps`, `dents`, `ripples`, `waves`, `wrinkles` and `bump_map`.

You may declare and use slope map identifiers. For example:

```

#declare Fancy_Wave =
slope_map { // Now let's get fancy
 [0.0 <0, 1>] // Do tiny triangle here
 [0.2 <1, 1>] // down
 [0.2 <1,-1>] // to
 [0.4 <0,-1>] // here.
 [0.4 <0, 0>] // Flat area
 [0.5 <0, 0>] // through here.
 [0.5 <1, 0>] // Square wave leading edge
 [0.6 <1, 0>] // trailing edge
 [0.6 <0, 0>] // Flat again
 [0.7 <0, 0>] // through here.
 [0.7 <0, 3>] // Start scallop
 [0.8 <1, 0>] // flat on top
 [0.9 <0,-3>] // finish here.
 [0.9 <0, 0>] // Flat remaining through 1.0
 }

object{ My_Object
 pigment { White }
 normal {
 wood
 slope_map { Fancy_Wave }
 }
 }
```

3.4.6.2.2.1 Normals, Accuracy

Surface normals that use patterns that were not designed for use with normals (anything other than bumps, dents, waves, ripples, and wrinkles) uses a `slope_map` whether you specify one or not. To create a perturbed normal from a pattern, POV-Ray samples the pattern at four points in a pyramid surrounding the desired point to determine the gradient of the pattern at the center of the pyramid. The distance that these points are from the center point determines the accuracy of the approximation. Using points too close together causes floating-point inaccuracies. However, using points too far apart can lead to artefacts as well as smoothing out features that should not be smooth.

Usually, points very close together are desired. POV-Ray currently uses a delta or accuracy distance of 0.02. Sometimes it is necessary to decrease this value to get better accuracy if you are viewing a close-up of the texture. Other times, it is nice to increase this value to smooth out sharp edges in the normal (for example, when using a 'solid' crackle pattern). For this reason, a new property, `accuracy`, has been added to normals. It only makes a difference if the normal uses a `slope_map` (either specified or implied).

You can specify the value of this accuracy (which is the distance between the sample points when determining the gradient of the pattern for slope_map) by adding `accuracy <float>` to your normal. For all patterns, the default is 0.02.

For more on `slope_map` see the Slope Map Tutorial

3.4.6.2.3 Bump Map

When all else fails and none of the normal pattern types meets your needs you can use a `bump_map` to wrap a 2-D bit-mapped bump pattern around your 3-D objects.

Instead of placing the color of the image on the shape like an `image_map` a `bump_map` perturbs the surface normal based on the color of the image at that point. The result looks like the image has been embossed into the surface. By default, a bump map uses the brightness of the actual color of the pixel. Colors are converted to gray scale internally before calculating height. Black is a low spot, white is a high spot. The image's index values may be used instead. See the sections Use_Index
and Use_Color below.

3.4.6.2.3.1 Specifying a Bump Map

The syntax for a `bump_map` is:

```

BUMP_MAP:
 normal {
 bump_map {
 BITMAP_TYPE "bitmap.ext" [gamma GAMMA] [premultiplied BOOL]
 [BUMP_MAP_MODS...]
 }
 [NORMAL_MODFIERS...]
 }
BITMAP_TYPE:
 exr | gif | hdr | iff | jpeg | pgm | png | ppm | sys | tga | tiff
BUMP_MAP_MOD:
 map_type Type | once | interpolate Type | use_color |
 use_colour | bump_size Value
```

After the required BITMAP_TYPE keyword is a string expression containing the name of a bitmapped bump file of the specified type. Several optional modifiers may follow the file specification. The modifiers are described below.

Note: Earlier versions of POV-Ray allowed some modifiers before the BITMAP_TYPE but that syntax is being phased out in favor of the syntax described here.

Filenames specified in the `bump_map` statements will be searched for in the home (current) directory first and, if not found, will then be searched for in directories specified by any `+L` or `Library_Path` options active. This would facilitate keeping all your bump maps files in a separate subdirectory and giving a `Library_Path` option to specify where your library of bump maps are. See Library Paths for details.

By default, the bump pattern is mapped onto the x-y-plane. The bump pattern is projected onto the object as though there were a slide projector somewhere in the -z-direction. The pattern exactly fills the square area from (x,y) coordinates (0,0) to (1,1) regardless of the pattern's original size in pixels. If you would like to change this default you may translate, rotate or scale the pigment or texture to map it onto the object's surface as desired. If you would like to change this default orientation you may translate, rotate or scale the pigment or texture to map it onto the object's surface as desired.

While POV-Ray will normally interpret the bump map input file as a container of linear data irregardless of file type, this can be overridden for any individual bump map input file by specifying `gamma` GAMMA immediately after the file name. For example:

```

bump_map {
 jpeg "foobar.jpg" gamma 1.8
 }
```

This will cause POV-Ray to perform gamma adjustment or -decoding on the input file data before building the bump map. Alternatively to a numerical value, `srgb` may be specified to denote that the file is pre-corrected or encoded using the sRGB transfer function instead of a power-law gamma function. See section Gamma Handling for more information on gamma.

The file name is optionally followed by one or more BITMAP_MODIFIERS. The `bump_size`, `use_color` and `use_index` modifiers are specific to bump maps and are discussed in the following sections. See the section Bitmap Modifiers where the generic bitmap modifiers `map_type`, `once` and `interpolate` are described.

3.4.6.2.3.2 Bump_Size

The relative bump size can be scaled using the `bump_size` modifier. The bump size number can be any number other than 0 but typical values are from about 0.1 to as high as 4.0 or 5.0.

```

normal {
 bump_map {
 gif "stuff.gif"
 bump_size 5.0
 }
 }
```

Originally `bump_size` could only be used inside a bump map but it can now be used with any normal. Typically it is used to override a previously defined size. For example:

```

normal {
 My_Normal //this is a previously defined normal identifier
 bump_size 2.0
 }
```

3.4.6.2.3.3 Use_Index and Use_Color

Usually the bump map converts the color of the pixel in the map to a gray scale intensity value in the range 0.0 to 1.0 and calculates the bumps based on that value. If you specify `use_index`, the bump map uses the color's palette number to compute as the height of the bump at that point. So, color number 0 would be low and color number 255 would be high (if the image has 256 palette entries). The actual color of the pixels doesn't matter when using the index. This option is only available on
palette based formats. The `use_color` keyword may be specified to explicitly note that the color methods should be used instead. The alternate spelling `use_colour` is also valid. These modifiers may only be used inside the `bump_map` statement.

### 3.4.6.3 Finish

The finish properties of a surface can greatly affect its appearance. How does light reflect? What happens in shadows? What kind of highlights are visible. To answer these questions you need a `finish`.

The syntax for `finish` is as follows:

```

FINISH:
 finish { [FINISH_IDENTIFIER] [FINISH_ITEMS...] }
FINISH_ITEMS:
 ambient COLOR | diffuse [albedo] Amount [, Amount] | emission COLOR |
 brilliance Amount | phong [albedo] Amount | phong_size Amount | specular [albedo] Amount |
 roughness Amount | metallic [Amount] | reflection COLOR |
 crand Amount | conserve_energy BOOL_ON_OFF |
 reflection { Color_Reflecting_Min [REFLECTION_ITEMS...] } |
 subsurface { translucency COLOR } |
 irid { Irid_Amount [IRID_ITEMS...] }
REFLECTION_ITEMS:
 COLOR_REFLECTION_MAX | fresnel BOOL_ON_OFF |
 falloff FLOAT_FALLOFF | exponent FLOAT_EXPONENT |
 metallic FLOAT_METALLIC
IRID_ITEMS:
 thickness Amount | turbulence Amount
```

The FINISH_IDENTIFIER is optional but should proceed all other items. Any items after the FINISH_IDENTIFIER modify or override settings given in the FINISH_IDENTIFIER. If no identifier is specified then the items modify the finish values in the current default texture.

Note: Transformations are not allowed inside a finish because finish items cover the entire surface uniformly. Each of the FINISH_ITEMS listed above is described in sub-sections below.

In earlier versions of POV-Ray, the `refraction`, `ior`, and `caustics` keywords were part of the `finish` statement but they are now part of the `interior` statement. They are still supported under `finish` for backward compatibility but the results may not be 100% identical to previous versions. See Why are Interior and Media Necessary? for more details.

A `finish` statement is part of a `texture` specification. However it can be tedious to use a `texture` statement just to add a highlights or other lighting properties to an object. Therefore you may attach a finish directly to an object without explicitly specifying that it as part of a texture. For example instead of this:

```

object { My_Object texture { finish { phong 0.5 } } }
```

you may shorten it to:

```

object { My_Object finish { phong 0.5 } }
```

Doing so creates an entire `texture` structure with default `pigment` and `normal`
statements just as if you had explicitly typed the full `texture {...}` around it.

Finish identifiers may be declared to make scene files more readable and to parameterize scenes so that changing a single declaration changes many values. An identifier is declared as follows.

```

FINISH_DECLARATION:
 #declare IDENTIFIER = FINISH |
 #local IDENTIFIER = FINISH
```

Where IDENTIFIER is the name of the identifier up to 40 characters long and FINISH is any valid `finish` statement. See #declare vs. #local for information on identifier scope.

3.4.6.3.1 Ambient

The light you see in dark shadowed areas comes from diffuse reflection off of other objects. This light cannot be modeled directly using ray-tracing, however, the radiosity feature can do a realistic approximation at the cost of higher render times. For most scenes, especially in-door scenes, this is will greatly improve the end result.

The classic way to simulate ambient lighting in shadowed areas is to assume that light is scattered
everywhere in the room equally, so the effect can simply be calculated by adding a small amount of light to each texture,
whether or not a light is actually shining on that texture. This renders very fast, but has the disadvantage that shadowed
areas look flat.

Note: Without radiosity, ambient light does not account for the color of surrounding objects. If you walk into a room that has red walls, floor and ceiling then your white clothing will look pink from the reflected light. POV-Ray's ambient shortcut does not account for this.

The `ambient` keyword controls the amount of ambient light used for each object. In some situations the ambient light might also be tinted, for this a color value can be specified. For example:

```

finish { ambient rgb <0.3,0.1,0.1> } //a pink ambient
```

However, if all color components are equal, a single float value may be used. For example the single float value of 0.3 is treated as <0.3,0.3,0.3>. The default value is 0.1, which gives very little ambient light. As with light sources, physically meaningful values are greater than 0, but negative values actually work too, and the value may be arbitrarily high to simulate bright light.

You may also specify the overall ambient light level used when calculating the ambient lighting of an object using the global `ambient_light` setting. The total light is given by Ambient = Finish_Ambient * Global_Ambient_Light_Source. See the section Ambient Light for more details.

Ambient light affects both shadowed and non-shadowed areas, so if you turn up the `ambient` value, you may want to turn down the `diffuse` and `reflection` values. Specifying a high `ambient` value for an object effectively gives it an intrinsic glow, however, if the intent is to actually have it glowing (as opposed to simulating background light), the `emission` keyword should be used instead. The difference is that actual glowing objects light up their surroundings in radiosity scenes, while the `ambient` term is effectively set to zero.

Note: Specular reflected indirect illumination such as the flashlight shining in a mirror is not modeled by either ambient light or radiosity. For this, you need photons.

3.4.6.3.2 Emission

As of version 3.7, you can now add the `emission` keyword to the finish block. The intention is to simplify the use of materials designed for non-radiosity scenes in scenes with radiosity, or the design of scenes that can be rendered with or without radiosity.

The syntax and effect are virtually identical to `ambient`, except that emission is unaffected by the global `ambient_light` parameter. An objects `ambient` term is now effectively set to 0 if radiosity is active, the exception being, in legacy scenes where the `#version` is set to less than 3.7

3.4.6.3.3 Diffuse Reflection Items

When light reflects off of a surface the laws of physics say that it should leave the surface at the exact same angle it came in. This is similar to the way a billiard ball bounces off a bumper of a pool table. This perfect reflection is called specular reflection. However only very smooth polished surfaces reflect light in this way. Most of the time, light reflects and is scattered in all directions by the roughness of the surface. This scattering is called diffuse reflection because the light diffuses
or spreads in a variety of directions. It accounts for the majority of the reflected light we see.

3.4.6.3.3.1 Diffuse

The keyword `diffuse` is used in a `finish` statement to control how much of the light coming directly from any light sources is reflected via diffuse reflection. The optional keyword `albedo` can be used right after diffuse to specify that the parameter is to be taken as the total diffuse/specular reflectance, rather than peak reflectance.

Note: When `brilliance` is equal to 1 `albedo` will have no effect on the diffuse parameter.

For example:

```

finish { diffuse albedo 0.7 }
```

Means that 70% of the light seen comes from direct illumination from light sources. The default value for diffuse is 0.6.

To model thin, diffusely-translucent objects (e.g. paper, curtains, leaves etc.), an optional 2nd float parameter has been added to the `diffuse` finish statement to control the effect of illumination from the back of the surface. The default value is 0.0, i.e. no diffuse backside illumination. For realistic results, the sum of both parameters should be between 0.0 and 1.0, and the 2nd parameter should be the smaller of the two.

Note: This feature is currently experimental and may be subject to change. In particular, the syntax as well as inter-operation with `double_illuminate`, multi-layered textures or `conserve_energy` are still under investigation.

A new sample scene, `~scenes/advanced/diffuse_back.pov`, has been provided to illustrate this new feature.

3.4.6.3.3.2 Brilliance

The amount of direct light that diffuses from an object depends upon the angle at which it hits the surface. When light hits at a shallow angle it illuminates less. When it is directly above a surface it illuminates more. The `brilliance` keyword can be used in a `finish` statement to vary the way light falls off depending upon the angle of incidence. This controls the tightness of the basic diffuse illumination on objects and slightly adjusts the appearance of surface shininess. Objects may appear more metallic by increasing their brilliance. The default value is 1.0. Higher values from 5.0 to about 10.0 cause the light to fall off less at medium to low angles. There are no limits to the brilliance value. Experiment to see what works best for a particular situation. This is best used in concert with highlighting.

3.4.6.3.3.3 Crand Graininess

Very rough surfaces, such as concrete or sand, exhibit a dark graininess in their apparent color. This is caused by the shadows of the pits or holes in the surface. The `crand` keyword can be added to a `finish` to cause a minor random darkening in the diffuse reflection of direct illumination. Typical values range from `crand 0.01` to `crand 0.5` or higher. The default value is 0. For example:

```

finish { crand 0.05 }
```

The grain or noise introduced by this feature is applied on a pixel-by-pixel basis. This means that it will look the same on far away objects as on close objects. The effect also looks different depending upon the resolution you are using for the rendering.

Note: The `crand` should not be used when rendering animations. This is the one of a few truly random features in POV-Ray and will produce an annoying flicker of flying pixels on any textures animated with a `crand` value. For these reasons it is not a very accurate way to model the rough surface effect.

3.4.6.3.3.4 Subsurface Light Transport

The subsurface light transport feature, also know as subsurface scattering, is enabled ONLY when a `global_settings` subsurface block is present. For example, to enable SSLT and use it's default settings, you can specify an empty block.

```

 global_settings {
 subsurface {}
 }
```

To activate SSLT for a particular object you will also need to add the following statement to its finish block.

```

 material {
 texture {
 pigment { PIGMENT }
 finish {
 ...
 subsurface { translucency COLOR }
 }
 }
 interior { ior FLOAT }
 }
```

The pigment determines the SSLT material's overall appearance when applied to an object with sufficiently large structures. The `translucency` color, which can alternatively be a float, determines the strength of the subsurface light transport effect. The material's index of refraction also affects the appearance, and is essential for SSLT materials, but doesn't generate a warning at parse time if omitted.

Note: The effect doesn't scale with the object, and values may be greater than 1.0

To adjust materials to the dimensions of your scene, you should use the `mm_per_unit` setting in the global settings block. The algorithm is designed to give realistic results at a scale of 10 mm per POV-Ray unit by default. For other scales, you can place the following statement in the `global_settings` block:

```

 mm_per_unit INT
```

Hint: Using these scaling examples as a guide you can easily come up with a suitable setting.

 1 cm per unit, set it to 10 (the default)
 1 inch per unit, set it to 25.4
 1 m per unit, set it to 1000

To tune the algorithm for quality or performance, the number of samples for the diffuse scattering and single-scattering approximation, respectively, can be specified by placing the following statement in the `global_settings` section. Both values default is 50.

```

 subsurface { samples INT, INT }
```

See the sample SSLT scene in `~scenes/subsurface/subsurface.pov` for more information. See also this PDF document, A Practical Model for Subsurface Light Transport, for more in depth information about SSLT, including some sample values to use when defining new materials.

To specify whether subsurface light transport effects should be applied to incoming `radiosity` based diffuse illumination, you should place the following in the global settings `subsurface` block:

```

 global_settings {
 subsurface { radiosity BOOL }
 }
```

If this setting is `off`, the default, subsurface light transport effects will only be applied to direct illumination from classic light sources. Setting this feature to `on` will improve realism especially for materials with high translucency, but at a significant cost in rendering time.

See the section Subsurface and Radiosity for additional configuration information.

Note: Subsurface scattering is disabled in all quality levels except `+Q9` or higher.

Warning: Be advised that the subsurface scattering feature is still experimental. These conditions, and possibly others, can apply. Usage and syntax is also subject to change!

 Incorrect use may result in hard crashes instead of parse warnings.
 Pigments having any zero color components currently doesn't play nice with SSLT. For example use `rgb <1,0.01,0.01>` instead of `rgb <1,0,0>` as color literals or when declaring pigment identifiers.
 A diffuse finish attribute of zero can also cause povray to throw an assertion failure.
 Unions of overlapping objects will probably give unexpected results, however merge should work.
 Mesh objects need to be closed (not perfectly) for realism.
 To avoid seams between objects, they currently must share a common interior. It's not sufficient to have interiors with identical parameters, or even instances of the same defined interior. The only way to overcome this is to specify the interior in the parent CSG rather than the individual primitives. For the desired results:

 REMOVE any interior statements from the material.
 ADD the interior statement to the union or merge.
 For each part that needs a different `ior` (e.g. eyelashes or teeth) add an individual interior statement.

3.4.6.3.4 Highlights

Highlights are the bright spots that appear when a light source reflects off of a smooth object. They are a blend of specular reflection and diffuse reflection. They are specular-like because they depend upon viewing angle and illumination angle. However they are diffuse-like because some scattering occurs. In order to exactly model a highlight you would have to calculate specular reflection off of thousands of microscopic bumps called micro facets. The more that micro facets are facing the viewer the shinier the object appears and the tighter the highlights become. POV-Ray uses two different models to simulate highlights without calculating micro facets. They are the specular and Phong models.

Note: Specular and phong highlights are not mutually exclusive. It is possible to specify both and they will both take effect. Normally, however, you will only specify one or the other.

3.4.6.3.4.1 Phong Highlights

The `phong` keyword in the `finish` statement controls the amount of phong highlighting on the object. It causes bright shiny spots on the object that are the color of the light source being reflected.

The phong method measures the average of the facets facing in the mirror direction from the light sources to the viewer.

Phong's value is typically from 0.0 to 1.0, where 1.0 causes complete saturation to the light source's color at the brightest area (center) of the highlight. The default value is 0.0 and gives no highlight.

The size of the highlight spot is defined by the `phong_size` value. The larger the phong size the tighter, or smaller, the highlight and the shinier the appearance. The smaller the phong size the looser, or larger, the highlight and the less glossy the appearance.

Typical values range from 1.0 (very dull) to 250 (highly polished) though any values may be used. The default value is 40 (plastic) if `phong_size` is not specified.

The optional keyword `albedo` can be used right after phong to specify that the parameter is to be taken as the total diffuse/specular reflectance, rather than peak reflectance.

For example:

```

finish { phong albedo 0.9 phong_size 60 }
```

If `phong` is not specified `phong_size` has no effect.

3.4.6.3.4.2 Specular Highlight

The `specular` keyword in a `finish` statement produces a highlight which is very similar to phong highlighting but it uses slightly different model. The specular model more closely resembles real specular reflection and provides a more credible spreading of the highlights occurring near the object horizons.

The `specular` value is typically from 0.0 to 1.0, where 1.0 causes complete saturation to the light source's color at the brightest area (center) of the highlight. The default value is 0.0 and gives no highlight.

The size of the spot is defined by the value given the `roughness` keyword. Typical values range from 1.0 (very rough - large highlight) to 0.0005 (very smooth - small highlight). The default value, if roughness is not specified, is 0.05 (plastic).

It is possible to specify wrong values for `roughness` that will generate an error. Do not use 0! If you get errors, check to see if you are using a very, very small roughness value that may be causing the error.

The optional keyword `albedo` can be used right after specular to specify that the parameter is to be taken as the total diffuse/specular reflectance, rather than peak reflectance.

For example:

```

finish { specular albedo 0.9 roughness 0.02 }
```

If `specular` is not specified `roughness` has no effect.

Note: When light is reflected by a surface such as a mirror, it is called specular reflection however such reflection is not controlled by the `specular` keyword. The `reflection` keyword controls mirror-like specular reflection.

3.4.6.3.4.3 Metallic Highlight Modifier

The keyword `metallic` may be used with `phong` or `specular` highlights. This keyword indicates that the color of the highlights will be calculated by an empirical function that models the reflectivity of metallic
surfaces.

Normally highlights are the color of the light source. Adding this keyword filters the highlight so that white light reflected from a metallic surface takes the color specified by the pigment

The `metallic` keyword may optionally be follow by a numeric value to specify the influence the amount of the effect. If no keyword is specified, the default value is zero. If the keyword is specified without a value, the default value is 1.

For example:

```

finish {
 phong 0.9
 phong_size 60
 metallic
 }
```

If `phong` or `specular` keywords are not specified then `metallic` has no effect.

3.4.6.3.5 Specular Reflection

When light does not diffuse and it does reflect at the same angle as it hits an object,
it is called specular reflection. Such mirror-like reflection is controlled by the
`reflection {...}` block in a `finish` statement.

Syntax:

```

finish {
 reflection {
 [COLOR_REFLECTION_MIN,] COLOR_REFLECTION_MAX
 [fresnel BOOL_ON_OFF]
 [falloff FLOAT_FALLOFF]
 [exponent FLOAT_EXPONENT]
 [metallic FLOAT_METALLIC]
 }
 }

[interior { ior IOR }]
```

The simplest use would be a perfect mirror:

```

finish { reflection {1.0} ambient 0 diffuse 0 }
```

This gives the object a mirrored finish. It will reflect all other elements in the scene.
Usually a single float value is specified after the keyword even though the syntax calls
for a color. For example a float value of 0.3 gets promoted to the full color vector
<0.3,0.3,0.3,0.3,0.3> which is acceptable because only the red, green and blue
parts are used.

The value can range from 0.0 to 1.0. By default there is no reflection.

Note: You should be aware that:

 Adding reflection to a texture makes it take longer to render because additional rays must be traced.

 The reflected light may be tinted by specifying a color rather than a float. For example, `finish { reflection rgb <1,0,0> }` gives a red mirror that only reflects red light.

 Although such reflection is called specular it is not controlled by the `specular` keyword. That keyword controls a specular highlight.

 The old syntax for simple reflection: `reflection COLOR` and `reflection_exponent FLOAT` (without braces) is still supported for backward compatibility.

`falloff` sets a falloff exponent in the variable reflection. This is the exponent
telling how fast the reflectivity will fall off, i.e. linear, squared, cubed, etc.

The `metallic` keyword is similar in function to the metallic keyword
used for highlights in finishes: it simulates the reflective properties of metallic surfaces,
where reflected light takes on the colour of the surface. When `metallic` is used,
the reflection color is multiplied by the pigment color at each point. You can
specify an optional float value, which is the amount of influence the `metallic`
keyword has on the reflected color. `metallic` uses the Fresnel equation so that
the color of the light is reflected at glancing angles, and the color of the metal is reflected
for angles close to the surface's normal.

exponent

POV-Ray uses a limited light model that cannot distinguish between objects which are simply
brightly colored and objects which are extremely bright. A white piece of paper, a light
bulb, the sun, and a supernova, all would be modeled as `rgb<1,1,1>` and
slightly off-white objects would be only slightly darker. It is especially difficult to model
partially reflective surfaces in a realistic way. Middle and lower brightness objects typically
look too bright when reflected. If you reduce the `reflection` value, it tends to
darken the bright objects too much. Therefore the optional `exponent` keyword has
been added. It produces non-linear reflection intensities. The default value of 1.0 produces
a linear curve. Lower values darken middle and low intensities and keeps high intensity
reflections bright. This is a somewhat experimental feature designed for artistic use. It does
not directly correspond to any real world reflective properties.

Variable reflection

Many materials, such as water, ceramic glaze, and linoleum are more reflective when viewed at shallow angles.
This can be simulated by also specifying a minimum reflection in the `reflection {...}` statement.

For example:

```

finish { reflection { 0.03, 1 }}
```

uses the same function as the standard reflection, but the first parameter sets the minimum reflectivity.
It could be a color vector or a float (which is automatically promoted to a gray vector). This minimum
value is how reflective the surface will be when viewed from a direction parallel to its normal.

The second parameter sets the maximum reflectivity, which could also be a color vector or a float
(which is automatically promoted to a gray vector). This maximum parameter is how reflective the
surface will be when viewed at a 90-degree angle to its normal.

Note: You can make maximum reflection less than minimum reflection if you want, although the result
is something that does not occur in nature.

When adding the `fresnel` keyword, the Fresnel reflectivity function is used instead of
standard reflection. It calculates reflectivity using the finish's IOR. So with a fresnel reflection_type
an `interior { ior IOR }` statement is required, even with opaque pigments. Remember that
in real life many opaque objects have a thin layer of transparent glaze on their surface, and it
is the glaze (which -does- have an IOR) that is reflective.

3.4.6.3.6 Conserve Energy for Reflection

One of the features in POV-Ray is variable reflection, including realistic Fresnel
reflection (see the section on Variable Reflection). Unfortunately, when this is coupled with constant transmittance, the texture
can look unrealistic. This unreal-ism is caused by the scene breaking the law of
conservation of energy. As the amount of light reflected changes, the amount of light
transmitted should also change (in a give-and-take relationship).

This can be achieved by adding the `conserve_energy` keyword
to the object's `finish {}`.

When conserve_energy is enabled, POV-Ray will multiply the amount filtered
and transmitted by what is left over from reflection (for example, if reflection is 80%,
filter/transmit will be multiplied by 20%).

3.4.6.3.7 Iridescence

Iridescence, or Newton's thin film interference, simulates
the effect of light on surfaces with a microscopic transparent film overlay.
The effect is like an oil slick on a puddle of water or the rainbow hues of a
soap bubble. This effect is controlled by the `irid` statement
specified inside a `finish` statement.

This parameter modifies the surface color as a function of the angle between
the light source and the surface. Since the effect works in conjunction with
the position and angle of the light sources to the surface it does not behave
in the same ways as a procedural pigment pattern.

The syntax is:

```

IRID:
 irid { Irid_Amount [IRID_ITEMS...] }
IRID_ITEMS:
 thickness Amount | turbulence Amount
```

The required `Irid_Amount` parameter is the
contribution of the iridescence effect to the overall surface color. As a
rule of thumb keep to around 0.25 (25% contribution) or less, but experiment.
If the surface is coming out too white, try lowering the `diffuse` and possibly the `ambient` values of the
surface.

The `thickness` keyword represents the film's thickness. This
is an awkward parameter to set, since the thickness value has no relationship
to the object's scale. Changing it affects the scale or
busy-ness of the effect. A very thin film will have a high frequency of
color changes while a thick film will have large areas of color. The default
value is zero.

The thickness of the film can be varied with the `turbulence`
keyword. You can only specify the amount of turbulence with iridescence. The
octaves, lambda, and omega values are internally set and are not adjustable
by the user at this time. This parameter varies only a single value: the
thickness. Therefore the value must be a single float value. It cannot be a
vector as in other uses of the `turbulence` keyword.

In addition, perturbing the object's surface normal through the use of
bump patterns will affect iridescence.

For the curious, thin film interference occurs because, when the ray hits
the surface of the film, part of the light is reflected from that surface,
while a portion is transmitted into the film. This subsurface ray
travels through the film and eventually reflects off the opaque substrate.
The light emerges from the film slightly out of phase with the ray that was
reflected from the surface.

This phase shift creates interference, which varies with the wavelength of
the component colors, resulting in some wavelengths being reinforced, while
others are cancelled out. When these components are recombined, the result is
iridescence. See also the global setting Irid_Wavelength for additional information.

Note: The version 3.7 iridescence feature has had a major overhaul. The syntax remains the same, however, both the thickness and amount values are now specified in microns. Consequently, iridescence effects will vary from previous versions.

The concept used for this feature came from the book Fundamentals of
Three-Dimensional Computer Graphics by Alan Watt (Addison-Wesley).

### 3.4.6.4 Halo

Earlier versions of POV-Ray used a feature called `halo` to
simulate fine particles such as smoke, steam, fog, or flames. The `halo` statement was part of the `texture` statement. This
feature has been discontinued and replaced by the `interior` and
`media` statements which are object modifiers outside the `texture` statement.

See Why are Interior and Media Necessary? for a detailed explanation on the reasons for the change. See also Media for details on `media`.

### 3.4.6.5 Patterned Textures

Patterned textures are complex textures made up of multiple textures. The
component textures may be plain textures or may be made up of patterned
textures. A plain texture has just one pigment, normal and finish statement.
Even a pigment with a pigment map is still one pigment and thus considered a
plain texture as are normals with normal map statements.

Patterned textures use either a `texture_map` statement to
specify a blend or pattern of textures or they use block textures such as
`checker` with a texture list or a bitmap similar to an image map
called a material map specified with a `material_map`
statement.

The syntax is...

```

PATTERNED_TEXTURE:
 texture {
 [PATTERNED_TEXTURE_ID]
 [TRANSFORMATIONS...]
 } |
 texture {
 PATTERN_TYPE
 [TEXTURE_PATTERN_MODIFIERS...]
 } |
 texture {
 tiles TEXTURE tile2 TEXTURE
 [TRANSFORMATIONS...]
 } |
 texture {
 material_map {
 BITMAP_TYPE "bitmap.ext"
 [BITMAP_MODS...] TEXTURE... [TRANSFORMATIONS...]
 }
 }

TEXTURE_PATTERN_MODIFIER:
 PATTERN_MODIFIER | TEXTURE_LIST |
 texture_map {
 TEXTURE_MAP_BODY
 }
```

There are restrictions on using patterned textures. A patterned texture
may not be used as a default texture, see the section: The #default Directive.
A patterned texture cannot be used as a layer in a layered texture however
you may use layered textures as any of the textures contained within a
patterned texture.

3.4.6.5.1 Texture Maps

In addition to specifying blended color with a color map or a pigment map
you may create a blend of textures using `texture_map`. The syntax
for a texture map is identical to the pigment map except you specify a
texture in each map entry.

The syntax for `texture_map` is as follows:

```

TEXTURE_MAP:
 texture_map { TEXTURE_MAP_BODY }
TEXTURE_MAP_BODY:
 TEXTURE_MAP_IDENTIFIER | TEXTURE_MAP_ENTRY...
TEXTURE_MAP_ENTRY:
 [ Value TEXTURE_BODY ]
```

Where `Value` is a float value between 0.0 and 1.0
inclusive and each TEXTURE_BODY is anything which can be inside a
`texture{...}` statement. The `texture` keyword and
`{}` braces need not be specified.

Note: The `[]` brackets are part of the actual
TEXTURE_MAP_ENTRY. They are not notational symbols denoting optional
parts. The brackets surround each entry in the texture map.

There may be from 2 to 256 entries in the map.

For example:

```

texture {
 gradient x //this is the PATTERN_TYPE
 texture_map {
 [0.3 pigment{Red} finish{phong 1}]
 [0.3 T_Wood11] //this is a texture identifier
 [0.6 T_Wood11]
 [0.9 pigment{DMFWood4} finish{Shiny}]
 }
 }
```

When the `gradient x` function returns values from 0.0 to 0.3
the red highlighted texture is used. From 0.3 to 0.6 the texture identifier
`T_Wood11` is used. From 0.6 up to 0.9 a blend of `T_Wood11` and a shiny `DMFWood4` is used. From 0.9 on up
only the shiny wood is used.

Texture maps may be nested to any level of complexity you desire. The
textures in a map may have color maps or texture maps or any type of texture
you want.

The blended area of a texture map works by fully calculating both
contributing textures in their entirety and then linearly interpolating the
apparent colors. This means that reflection, refraction and lighting
calculations are done twice for every point. This is in contrast to using a
pigment map and a normal map in a plain texture, where the pigment is
computed, then the normal, then reflection, refraction and lighting are
calculated once for that point.

Entire textures may also be used with the block patterns such as `checker`, `hexagon` and `brick`. For
example...

```

texture {
 checker
 texture { T_Wood12 scale .8 }
 texture {
 pigment { White_Marble }
 finish { Shiny }
 scale .5
 }
 }
 }
```

Note: In the case of block patterns the `texture` wrapping is required around the texture information. Also note that this syntax prohibits the use of a layered texture however you can work around this by declaring a texture identifier for the layered texture and referencing the identifier.

A texture map is also used with the `average` texture type. See Average for more details.

You may declare and use texture map identifiers but the only way to declare
a texture block pattern list is to declare a texture identifier for the
entire texture.

3.4.6.5.2 Tiles

Earlier versions of POV-Ray had a patterned texture called a tiles
texture. It used the `tiles` and `tile2` keywords
to create a checkered pattern of textures.

```

TILES_TEXTURE:
 texture {
 tiles TEXTURE tile2 TEXTURE
 [TRANSFORMATIONS...]
 }
```

Although it is still supported for backwards compatibility you should use a `checker` block texture pattern described in the Texture Maps section rather than tiles textures.

3.4.6.5.3 Material Maps

The `material_map` patterned texture extends the concept of
image maps to apply to entire textures rather than solid colors. A material
map allows you to wrap a 2-D bit-mapped texture pattern around your 3-D
objects.

Instead of placing a solid color of the image on the shape like an image
map, an entire texture is specified based on the index or color of the image
at that point. You must specify a list of textures to be used like a
texture palette rather than the usual color palette.

When used with mapped file types such as GIF, and some PNG and TGA images,
the index of the pixel is used as an index into the list of textures you
supply. For unmapped file types such as some PNG and TGA images the 8 bit
value of the red component in the range 0-255 is used as an index.

If the index of a pixel is greater than the number of textures in your list
then the index is taken modulo N where N is the length of your list of
textures.

Note: The `material_map` statement has nothing to do with the `material` statement. A `material_map` is not a way to create patterned `material`. See Material for an explanation of this unrelated, yet similarly named, older feature.

3.4.6.5.3.1 Specifying a Material Map

The syntax for a `material_map` is:

```

MATERIAL_MAP:
 texture {
 material_map {
 BITMAP_TYPE "bitmap.ext"
 [BITMAP_MODS...] TEXTURE... [TRANSFORMATIONS...]
 }
 }
BITMAP_TYPE:
 exr | gif | hdr | iff | jpeg | pgm | png | ppm | sys | tga | tiff
BITMAP_MOD:
 map_type Type | once | interpolate Type
```

After the required BITMAP_TYPE keyword is a string expression
containing the name of a bitmapped material file of the specified type.
Several optional modifiers may follow the file specification. The modifiers
are described below.

Note: Earlier versions of POV-Ray allowed some modifiers before the BITMAP_TYPE but that syntax is being phased out in favor of the syntax described here.

Filenames specified in the `material_map` statements will be
searched for in the home (current) directory first and, if not found, will
then be searched for in directories specified by any `+L` or
`Library_Path` options active. This would facilitate keeping all
your material maps files in a separate subdirectory and giving a `Library_Path` option to specify where your library of material maps
are. See the section Library Paths for details.

By default, the material is mapped onto the x-y-plane. The material is
projected onto the object as though there were a slide projector
somewhere in the -z-direction. The material exactly fills the square area
from (x,y) coordinates (0,0) to (1,1) regardless of the material's
original size in pixels. If you would like to change this default you may
translate, rotate or scale the texture to map it onto the
object's surface as desired.

The file name is optionally followed by one or more BITMAP_MODIFIERS. There are no modifiers which are unique to a `material_map`. It only uses the generic bitmap modifiers `map_type`, `once` and `interpolate` described in BITMAP_MODIFIERS.

Although `interpolate` is legal in material maps, the color
index is interpolated before the texture is chosen. It does not interpolate
the final color as you might hope it would. In general, interpolation of
material maps serves no useful purpose but this may be fixed in future
versions.

Next is one or more `texture` statements. Each texture in the
list corresponds to an index in the bitmap file. For example:

```

texture {
 material_map {
 png "povmap.png"
 texture { //used with index 0
 pigment {color red 0.3 green 0.1 blue 1}
 normal {ripples 0.85 frequency 10 }
 finish {specular 0.75}
 scale 5
 }
 texture { //used with index 1
 pigment {White}
 finish {
 ambient 0 diffuse 0
 reflection 0.9 specular 0.75
 }
 }
 // used with index 2
 texture {pigment{NeonPink} finish{Luminous}}
 texture { //used with index 3
 pigment {
 gradient y
 color_map {
 [0.00 rgb < 1 , 0 , 0>]
 [0.33 rgb < 0 , 0 , 1>]
 [0.66 rgb < 0 , 1 , 0>]
 [1.00 rgb < 1 , 0 , 0>]
 }
 }
 finish{specular 0.75}
 scale 8
 }
 }
 scale 30
 translate <-15, -15, 0>
 }
```

After a `material_map` statement but still inside the texture
statement you may apply any legal texture modifiers.

Note: No other pigment, normal, or finish statements may be added to the texture outside the
material map.

 The following is illegal:

```

texture {
 material_map {
 gif "matmap.gif"
 texture {T1}
 texture {T2}
 texture {T3}
 }
 finish {phong 1.0}
 }
```

The finish must be individually added to each texture. Earlier
versions of POV-Ray allowed such specifications but they were ignored. The
above restrictions on syntax were necessary for various bug fixes. This means
some POV-Ray 1.0 scenes using material maps many need minor modifications
that cannot be done automatically with the version compatibility mode.

If particular index values are not used in an image then it may be necessary
to supply dummy textures. It may be necessary to use a paint program or other
utility to examine the map file's palette to determine how to arrange the
texture list.

The textures within a material map texture may be layered but material map
textures do not work as part of a layered texture. To use a layered texture
inside a material map you must declare it as a texture identifier and invoke
it in the texture list.

### 3.4.6.6 Layered Textures

It is possible to create a variety of special effects using layered
textures. A layered texture consists of several textures that are partially
transparent and are laid one on top of the other to create a more complex
texture. The different texture layers show through the transparent portions
to create the appearance of one texture that is a combination of several
textures.

You create layered textures by listing two or more textures one right after
the other. The last texture listed will be the top layer, the first one
listed will be the bottom layer. All textures in a layered texture other than
the bottom layer should have some transparency. For example:

```

object {
 My_Object
 texture {T1} // the bottom layer
 texture {T2} // a semi-transparent layer
 texture {T3} // the top semi-transparent layer
 }
```

In this example T2 shows only where T3 is transparent and T1 shows only
where T2 and T3 are transparent.

The color of underlying layers is filtered by upper layers but the results
do not look exactly like a series of transparent surfaces. If you had a stack
of surfaces with the textures applied to each, the light would be filtered
twice: once on the way in as the lower layers are illuminated by filtered
light and once on the way out. Layered textures do not filter the
illumination on the way in. Other parts of the lighting calculations work
differently as well. The results look great and allow for fantastic looking
textures but they are simply different from multiple surfaces. See `stones.inc` in the standard include files directory for some
magnificent layered textures.

Note: In versions predating POV-Ray 3.5, `filter` used to work the same
as `transmit` in layered textures. It has been changed to work as filter should. This can change the appearance of "pre 3.5" textures a lot. The `#version` directive can be used to get the "pre 3.5" behavior.

Note: Layered textures must use the `texture` wrapped around any pigment, normal or finish statements. Do not use multiple pigment, normal or finish statements without putting them inside the texture statement.

Layered textures may be declared. For example

```

#declare Layered_Examp =
 texture {T1}
 texture {T2}
 texture {T3}
```

may be invoked as follows:

```

object {
 My_Object
 texture {
 Layer_Examp
 // Any pigment, normal or finish here
 // modifies the bottom layer only.
 }
 }
```

Note: No macros are allowed in layered textures. The problem is that if a macro would contain a declare the parser could no longer guess that two or more texture identifiers are supposed to belong to the layered texture and not some other declare.

If you wish to use a layered texture in a block pattern, such as `checker`, `hexagon`, or `brick`, or in a `material_map`, you must declare it first and then reference it inside a
single texture statement. A patterned texture cannot be used as a layer in a
layered texture however you may use layered textures as any of the textures
contained within a patterned texture.

### 3.4.6.7 UV Mapping

All textures in POV-Ray are defined in 3 dimensions. Even planar image mapping is
done this way. However, it is sometimes more desirable to have the texture defined for
the surface of the object. This is especially true for bicubic_patch objects and mesh
objects, that can be stretched and compressed. When the object is stretched or
compressed, it would be nice for the texture to be glued to the object's
surface and follow the object's deformations.

When uv_mapping is used, then that object's texture will be mapped to it using
surface coordinates (u and v) instead of spatial coordinates (x, y, and z). This is
done by taking a slice of the object's regular 3D texture from the XY plane (Z=0) and
wrapping it around the surface of the object, following the object's surface coordinates.

Note: Some textures should be rotated to fit the slice in the XY plane.

Syntax:

```

texture {
 uv_mapping pigment{PIGMENT_BODY} | pigment{uv_mapping PIGMENT_BODY}
 uv_mapping normal {NORMAL_BODY } | normal {uv_mapping NORMAL_BODY }
 uv_mapping texture{TEXTURE_BODY} | texture{uv_mapping TEXTURE_BODY)
 }
```

3.4.6.7.1 Supported Objects

Surface mapping is currently defined for the following objects:

 bicubic_patch : UV coordinates are based on the patch's parametric coordinates. They stretch with the control points. The default range is (0..1) and can be changed.
 box : the image is wrapped around the box, as shown below.
 lathe, sor : modified spherical mapping... the u coordinate (0..1) wraps around the y axis, while the v coordinate is linked to the object's control points (also ranging 0..1). Surface of Revolution also has special disc mapping on the end caps if the object is not 'open'.
 mesh, mesh2 : UV coordinates are defined for each vertex and interpolated between.
 ovus : spherical mapping centered near the center of mass of the ovus (moving from one sphere to another as the ratio of radius progresses).
 parametric : In this case the map is not taken from a fixed set of coordinates but the map is taken from the area defined by the boundaries of the uv-space, in which the parametric surface has to be calculated.
 sphere : boring spherical mapping.
 torus : The map is taken from the area <0,0><1,1> where the u-coordinate is wrapped around the major radius and the the v-coordinate is wrapped around the minor radius.

| UV Boxmap |

3.4.6.7.2 UV Vectors

With the keyword `uv_vectors`, the UV coordinates of the corners can be
controlled for bicubic patches and standard triangle mesh.

For bicubic patches the UV coordinates can be specified for each of
the four corners of the patch. This goes right before the control points.

The syntax is:

`uv_vectors <corner1>,<corner2>,<corner3>,
<corner4>`

with default

`uv_vectors <0,0>,<1,0>,<1,1>,<0,1>`

For standard triangle meshes (not mesh2) you can specify the UV coordinates for each
of the three vertices `uv_vectors <uv1>,<uv2>,<uv3>` inside each
mesh triangle. This goes right after the coordinates (or coordinates & normals with
smooth triangles) and right before the texture.

Example:

```

mesh {
 triangle {
 <0,0,0>, <0.5,0,0>, <0.5,0.5,0>
 uv_vectors <0,0>, <1,0>, <1,1>
 }
 triangle {
 <0,0,0>, <0.5,0.5,0>, <0,0.5,0>
 uv_vectors <0,0>, <1,1>, <0,1>
 }
 texture {
 uv_mapping
 pigment {
 image_map {
 sys "SomeImage"
 map_type 0
 interpolate 0
 }
 }
 }
}
```

### 3.4.6.8 Triangle Texture Interpolation

This feature is utilized in a number of visualization approaches: triangles with
individual textures for each vertex, which are interpolated during rendering.

Syntax:

```

MESH_TRIANGLE:
triangle {
 <Corner_1>,
 <Corner_2>,
 <Corner_3>
 [MESH_TEXTURE]
 } |
smooth_triangle {
 <Corner_1>, <Normal_1>,
 <Corner_2>, <Normal_2>,
 <Corner_3>, <Normal_3>
 [MESH_TEXTURE]
 }

MESH_TEXTURE:
 texture { TEXTURE_IDENTIFIER } |
 texture_list {
 TEXTURE_IDENTIFIER TEXTURE_IDENTIFIER TEXTURE_IDENTIFIER
 }
```

To specify three vertex textures for the triangle, simply use `texture_list`
instead of texture.

### 3.4.6.9 Interior Texture

Syntax:

```

object {
 texture { TEXTURE_ITEMS... }
 interior_texture { TEXTURE_ITEMS...}
 }
```

All surfaces have an exterior and interior surface. The
`interior_texture` simply allows to specify a separate texture for the
interior surface of the object. For objects with no well defined
inside/outside (bicubic_patch, triangle, ...) the `interior_texture` is
applied to the backside of the surface.
Interior surface textures use exactly the same syntax and should work in
exactly the same way as regular surface textures, except that they use
the keyword `interior_texture` instead of `texture`.

Note: Do not confuse `interior_texture {}` with `interior {}`:
the first one specifies surface properties, the second one specifies volume properties.

### 3.4.6.10 Cutaway Textures

Syntax:

```

difference | intersection {
 OBJECT_1_WITH_TEXTURES
 OBJECT_2_WITH_NO_TEXTURE
 cutaway_textures
 }
```

When using a CSG difference or intersection to cut away parts of an
object, it is sometimes desirable to allow the object to retain its original texture. Generally,
however, the texture of the surface that was used to do the cutting will be displayed.

Also, if the cutting object was not given a texture by the user, the default texture
is assigned to it.

By using the `cutaway_textures` keyword in a CSG difference or
intersection, you specify that you do not want the default texture on the intersected
surface, but instead, the textures of the parent objects in the CSG should be used.

POV-Ray will determine which texture(s) to use by doing insidedness tests on
the objects in the difference or intersection. If the intersection point is inside an object,
that object's texture will be used (and evaluated at the interior point).

If the parent object is a CSG of objects with different textures, then the textures on
overlapping parts will be averaged together.
