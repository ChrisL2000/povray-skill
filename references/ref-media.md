## 3.4.8-3.4.9 Media & Include Files

## 3.4.8 Media

The `media` statement is used to specify particulate matter suspended in a medium such air or water. It can be used to specify smoke, haze, fog, gas, fire, dust etc. Previous versions of POV-Ray had two incompatible systems for generating such effects. One was `halo` for effects enclosed in a transparent or semi-transparent object. The other was `atmosphere` for effects that permeate the entire scene. This duplication of systems was complex and unnecessary. Both `halo`
and `atmosphere` have been eliminated. See Why are Interior and Media Necessary? for further details on this change. See Object Media
for details on how to use `media` with objects. See Atmospheric Media for details on using `media` for atmospheric effects outside of objects. This section and the sub-sections which follow explains the details of the various `media` options which are useful for either object media or atmospheric media.

Media works by sampling the density of particles at some specified number of points along the ray's path. Sub-samples are also taken until the results reach a specified confidence level. POV-Ray provides three methods of sampling. When used in an object's `interior` statement, sampling only occurs inside the object. When used for atmospheric media, the samples run from
the camera location until the ray strikes an object. Therefore for localized effects, it is best to use an enclosing object even though the density pattern might only produce results in a small area whether the media was enclosed or not.

The complete syntax for a `media` statement is as follows:

```

MEDIA:
 media { [MEDIA_IDENTIFIER] [MEDIA_ITEMS...] }
MEDIA_ITEMS:
 method Number | intervals Number | samples Min, Max |
 confidence Value | variance Value | ratio Value | jitter Value
 absorption COLOR | emission COLOR | aa_threshold Value |
 aa_level Value |
 scattering {
 Type, COLOR [ eccentricity Value ] [ extinction Value ]
 } |
 density {
 [DENSITY_IDENTIFIER] [PATTERN_TYPE] [DENSITY_MODIFIER...]
 } |
 TRANSFORMATIONS
DENSITY_MODIFIER:
 PATTERN_MODIFIER | DENSITY_LIST | COLOR_LIST |
 color_map { COLOR_MAP_BODY } | colour_map { COLOR_MAP_BODY } |
 density_map { DENSITY_MAP_BODY }
```

Media default values:

```

aa_level : 3
aa_threshold : 0.1
absorption : <0,0,0>
confidence : 0.9
emission : <0,0,0>
intervals : 1
jitter : 0.0
method : 3
ratio : 0.9
samples : Min 1, Max 1
variance : 1/128
SCATTERING
COLOR : <0,0,0>
eccentricity : 0.0
extinction : 1.0
```

If a media identifier is specified, it must be the first item. All other media items may be specified in any order. All are optional. You may have multiple `density` statements in a single `media` statement. See Multiple Density vs. Multiple Media for details. Transformations apply only to the `density` statements which have been already specified. Any `density` after a transformation is not affected. If the `media` has no `density` statements and none was specified in any media identifier, then the transformation has no effect. All other media items except for `density` and transformations override default values or any previously set values for this `media` statement.

Note: Some media effects depend upon light sources. However the participation of a light source depends upon the `media_interaction` and `media_attenuation` keywords. See Atmospheric Media Interaction and Atmospheric Attenuation for details.

Note: If you specify `transmit` or `filter` to create a transparent container object, `absorption` media will always cast a shadow. The same applies to `scattering` media unless `extinction` is set to zero, so if a shadow is not desired, use the `no_shadow` keyword for the container object. This does not apply to `emission` media as it never casts a shadow.

### 3.4.8.1 Interior

Introduced in POV-Ray 3.1 is an object modifier statement called `interior`. The syntax is:

```

INTERIOR:
 interior { [INTERIOR_IDENTIFIER] [INTERIOR_ITEMS...] }
INTERIOR_ITEM:
 ior Value | caustics Value | dispersion Value |
 dispersion_samples Samples | fade_distance Distance |
 fade_power Power | fade_color <Color>
 MEDIA...
```

Interior default values:

```

ior : 1.0
caustics : 0.0
dispersion : 1.0
dispersion_samples : 7
fade_distance : 0.0
fade_power : 0.0
fade_color : <0,0,0>
```

The `interior` contains items which describe the properties of the interior of the object. This is in contrast to the `texture` and `interior_texture` which describe the surface properties only. The interior of an object is only of interest if it has a transparent texture which allows you to see inside the object. It also applies only to solid objects which have a well-defined inside/outside distinction.

Note: The `open` keyword, or `clipped_by` modifier also allows you to see inside but interior features may not render properly. They should be avoided if accurate interiors are required.

Interior identifiers may be declared to make scene files more readable and to parameterize scenes so that changing a single declaration changes many values. An identifier is declared as follows.

```

INTERIOR_DECLARATION:
 #declare IDENTIFIER = INTERIOR |
 #local IDENTIFIER = INTERIOR
```

Where IDENTIFIER is the name of the identifier up to 40 characters long and INTERIOR is any valid `interior` statement. See #declare vs. #local for information on identifier scope.

3.4.8.1.1 Why are Interior and Media Necessary?

In previous versions of POV-Ray, most of the items in the `interior` statement were previously part of the `finish` statement. Also the `halo` statement which was once part of the `texture` statement has been discontinued and has been replaced by the `media` statement which is part of `interior`.

You are probably asking WHY? As explained earlier, the `interior` contains items which describe the properties of the interior of the object. This is in contrast to the `texture` which describes the surface properties only. However this is not just a philosophical change. There were serious inconsistencies in the old model.

The main problem arises when a `texture_map` or other patterned texture is used. These features allow you to create
textures that are a blend of two textures and which vary the entire texture from one point to another. It does its blending by fully evaluating the apparent color as though only one texture was applied and then fully reevaluating it with the other texture. The two final results are blended.

 It is totally illogical to have a ray enter an object with one index or refraction and then recalculate with another index. The result is not an average of the two ior values. Similarly it makes no sense to have a ray enter at one ior and exit at a different ior without transitioning between them along the way. POV-Ray only calculates refraction as the ray enters or leaves. It cannot incrementally compute a changing ior through the interior of an object. Real world objects such as optical fibers or no-line bifocal eyeglasses can have variable iors but POV-Ray cannot simulate them.

Similarly the `halo` calculations were not performed as the
syntax implied. Using a `halo` in such multi-textured objects did
not vary the `halo` through the interior of the object. Rather,
it computed two separate halos through the whole object and averaged the
results. The new design for `media` which replaces `halo` makes it possible to have media that varies throughout the
interior of the object according to a pattern but it does so independently of
the surface texture. Because there are other changes in the design of this
feature which make it significantly different, it was not only moved to the
`interior` but the name was changed.

During our development, someone asked if we will create patterned interiors
or a hypothetical `interior_map` feature. We will not. That would
defeat the whole purpose of moving these features in the first place. They
cannot be patterned and have logical or self-consistent results.

3.4.8.1.2 Empty and Solid Objects

It is very important that you know the basic concept behind empty and
solid objects in POV-Ray to fully understand how features like interior and
translucency are used. Objects in POV-Ray can either be solid, empty or
filled with (small) particles.

A solid object is made from the material specified by its pigment and finish
statements (and to some degree its normal statement). By default all objects
are assumed to be solid. If you assign a stone texture to a sphere you will
get a ball made completely of stone. It is like you had cut this ball from
a block of stone. A glass ball is a massive sphere made of glass. You should
be aware that solid objects are conceptual things. If you clip away parts of
the sphere you will clearly see that the interior is empty and it just has
a very thin surface.

This is not contrary to the concept of a solid object used in POV-Ray. It is
assumed that all space inside the sphere is covered by the sphere's
`interior`. Light passing through the object is affected by
attenuation and refraction properties. However there is no room for any other
particles like those used by fog or interior media.

Empty objects are created by adding the `hollow` keyword (see
Hollow) to the object statement. An empty (or hollow) object is
assumed to be made of a very thin surface which is of the material specified
by the pigment, finish and normal statements. The object's interior is
empty, it normally contains air molecules.

An empty object can be filled with particles by adding fog or atmospheric
media to the scene or by adding an interior media to the object. It is very
important to understand that in order to fill an object with any kind of
particles it first has to be made hollow.

There is a pitfall in the empty/solid object implementation that you have to
be aware of.

In order to be able to put solid objects inside a media or fog, a test has
to be made for every ray that passes through the media. If this ray travels
through a solid object the media will not be calculated. This is what anyone
will expect. A solid glass sphere in a fog bank does not contain fog.

The problem arises when the camera ray is inside any non-hollow object. In
this case the ray is already traveling through a solid object and even if the
media's container object is hit and it is hollow, the media will not be
calculated. There is no way of telling between these two cases.

POV-Ray has to determine whether the camera is inside any object prior to
tracing a camera ray in order to be able to correctly render medias when the
camera is inside the container object. There is no way around doing
this.

The solution to this problem (that will often happen with infinite objects
like planes) is to make those objects hollow too. Thus the ray will travel
through a hollow object, will hit the container object and the media will be
calculated.

3.4.8.1.3 Scaling objects with an interior

All the statements that can be put in an interior represent aspects of
the matter that an object is made of. Scaling an object, changing its size, does not change
its matter. Two pieces of the same quality steel, one twice as big as
the other, both have the same density. The bigger piece is quite a bit
heavier though.

 So, in POV-Ray, if you design a lens from a glass with an ior of 1.5
and you scale it bigger, the focal distance of the lens will get longer
as the ior stays the same. For light attenuation it means that an object will be
darker after being scaled up. The light intensity decreases a certain
amount per pov-unit. The object has become bigger, more pov-units, so more light is faded.
The `fade_distance, fade_power` themselves have not been changed.

 The same applies to media. Imagine media as a density of particles,
you specify 100 particles per cubic pov-unit. If we scale a 1 cubic
pov-unit object to be twice as big in every direction, we will have a
total of 800 particles in the object. The object will look different,
as we have more particles to look through. Yet the objects density is
still 100 particles per cubic pov-unit. In media this particle
density is set by the color after `emission`, `absorption`, or in
the `scattering` statement

```

#version 3.5;
global_settings {
 assumed_gamma 1.0
 }

camera {location <0, 0,-12.0> look_at 0 angle 30 }

#declare Container_T =
 texture {
 pigment {rgbt <1,1,1,1>}
 finish {ambient 0 diffuse 0}
 }

#declare Scale=2;

box { //The reference
 <-1,-1,0>,<1,1,.3>
 hollow
 texture {Container_T}
 interior {
 media {
 intervals 1
 samples 1,1
 emission 1
 }
 }
 translate <-2.1,0,0>
 }

box { //Object scaled twice as big
 <-1,-1,0>,<1,1,.3> //looks different but same
 hollow //particle density
 texture {Container_T}
 interior {
 media {
 intervals 1
 samples 1,1
 emission 1
 }
 }
 scale Scale
 translate<0,0,12>
 }

box { //Object scaled twice as big
 <-1,-1,0>,<1,1,.3> //looks the same but particle
 hollow //density scaled down
 texture {Container_T}
 interior {
 media {
 intervals 1
 samples 1,1
 emission 1/Scale
 }
 }
 scale Scale
 translate<0,0,12>
 translate<4.2,0,0>
 }
```

The third object in the scene above, shows what to do, if you want to scale
the object and want it to keep the same look as before. The interior
feature has to be divided by the same amount, that the object was scaled by.
This is only possible when the object is scaled uniform.

In general, the correct approach is to scale the media density proportionally to
the change in container volume. For non-uniform scaling to get an unambiguous result,
that can be explained in physical terms, we need to do:

```

Density*sqrt(3)/vlength(Scale)
```

where Density is your original media density and Scale is the scaling
vector applied to the container.

Note: The density modifiers inside the `density{}`
statement are scaled along with the object.

3.4.8.1.4 Refraction

When light passes through a surface either into or out of a dense medium
the path of the ray of light is bent. Such bending is called
refraction. The amount of bending or refracting of light depends upon
the density of the material. Air, water, crystal and diamonds all have
different densities and thus refract differently. The index of
refraction or ior value is used by scientists to describe the
relative density of substances. The `ior` keyword is used in
POV-Ray in the `interior` to turn on refraction and to specify the
ior value. For example:

```

object { MyObject pigment {Clear } interior { ior 1.5 } }
```

The default ior value of 1.0 will give no refraction. The index of
refraction for air is 1.0, water is 1.33, glass is 1.5 and diamond is
2.4.

Normally transparent or semi-transparent surfaces in POV-Ray do not refract
light. Earlier versions of POV-Ray required you to use the `refraction` keyword in the `finish` statement to turn on
refraction. This is no longer necessary. Any non-zero `ior` value
now turns refraction on.

In addition to turning refraction on or off, the old `refraction`
keyword was followed by a float value from 0.0 to 1.0. Values in between 0.0
and 1.0 would darken the refracted light in ways that do not correspond to
any physical property. Many POV-Ray scenes were created with intermediate
refraction values before this bug was discovered so the feature has been
maintained. A more appropriate way to reduce the brightness of refracted
light is to change the `filter` or
`transmit` value in the colors
specified in the pigment statement or to use the `fade_power`
and `fade_distance` keywords.
See Attenuation.

Note: Neither the `ior` nor `refraction` keywords cause the
object to be transparent. Transparency only occurs if there is a non-zero
`filter` or `transmit` value in the color.

The `refraction` and `ior` keywords were originally
specified in `finish` but are now properly specified in `interior`. They are accepted in `finish`
for backward compatibility and generate a warning message.

3.4.8.1.5 Dispersion

For all materials with a ior different from 1.0 the refractive index is not
constant throughout the spectrum. It changes as a function of wavelength.
Generally the refractive index decreases as the wavelength increases. Therefore
light passing through a material will be separated according to wavelength.
This is known as chromatic dispersion.

By default POV-Ray does not calculate dispersion as light travels through a
transparent object. In order to get a more realistic effect the `dispersion` and `dispersion_samples` keywords can be added to the
`interior{}` block. They will simulate dispersion by creating a
prismatic color effect in the object.

The `dispersion` value is the ratio of refractive indices for violet to
red. It controls the strength of dispersion (how much the colors are spread out) used.
A DISPERSION_VALUE of 1 will give no dispersion, good values are 1.01 to 1.1.

Note: There will be no dispersion, unless the `ior` keyword has
been specified in `interior{ }`. An ior of 1 is legal. The ior has no
influence on the dispersion strength, only on the angle of refraction.

As POV-Ray does not use wavelengths for raytracing, a spectrum is simulated.
The `dispersion_samples` value controls the amount of color-steps and
smoothness in the spectrum. The default value is 7, the minimum is 2. Values up to
100 or higher may be needed to get a very smooth result.

3.4.8.1.5.1 Dispersion & Caustics

Dispersion only affects the interior of an object and has no effect on faked
caustics (See Faked Caustics).

To see the effects of dispersion in caustics, photon mapping is needed. See the sections
Photons and Photons and Dispersion.

3.4.8.1.6 Attenuation

Light attenuation is used to model the decrease in light intensity as
the light travels through a transparent object. The keywords `fade_power`, `fade_distance` and `fade_color` are specified in the `interior` statement.

The `fade_distance` value determines the distance the light has to travel to reach half intensity while the `fade_power` value determines how fast the light will fall off. `fade_color` colorizes the attenuation. For realistic effects a fade power of 1 to 2 should be used. Default values for `fade_power` and `fade_distance` is 0.0 which turns this feature off. Default for `fade_color` is `<0,0,0>`, if `fade_color` is `<1,1,1>` there is no attenuation. The actual colors give colored attenuation. `<1,0,0>` looks red, not cyan as in media.

The attenuation is calculated by a formula similar to that used for light
source attenuation.

| Media Attenuation |

If you set fade_power in the interior of an object at 1000 or above,
a realistic exponential attenuation function will be used:

```
 Attenuation = exp(-depth/fade_dist)
```

The `fade_power` and `fade_distance` keywords were
originally specified in `finish` but are now properly specified in
`interior`. They are accepted in `finish` for backward
compatibility and generate a warning message.

3.4.8.1.7 Simulated Caustics

Caustics are light effects that occur if light is reflected or refracted
by specular reflective or refractive surfaces. Imagine a glass of water
standing on a table. If sunlight falls onto the glass you will see spots of
light on the table. Some of the spots are caused by light being reflected by
the glass while some of them are caused by light being refracted by the water
in the glass.

Since it is a very difficult and time-consuming process to actually calculate those effects (though it is not impossible), see the sections Photons. POV-Ray uses a quite simple method to simulate caustics caused by refraction. The method calculates the angle between the incoming light ray and the surface normal. Where they are nearly parallel it makes the shadow brighter. Where the angle is greater, the effect is diminished. Unlike real-world caustics, the effect does not vary based on distance. This caustic effect is limited to areas that are shaded by the transparent object. You will get no caustic effects from reflective surfaces nor in parts that are not shaded by the object.

The `caustics` `Power` keyword controls the
effect. Values typically range from 0.0 to 1.0 or higher. Zero is the default
which is no caustics. Low, non-zero values give broad hot-spots while higher
values give tighter, smaller simulated focal points.

The `caustics` keyword was originally specified in `finish` but is now properly specified in `interior`. It is
accepted in `finish` for backward compatibility and generates a
warning message.

3.4.8.1.8 Object-Media

The `interior` statement may contain one or more `media`
statements. Media is used to simulate suspended particles such as smoke,
haze, or dust. Or visible gasses such as steam or fire and explosions. When
used with an object interior, the effect is constrained by the object's
shape. The calculations begin when the ray enters an object and ends when
it leaves the object. This section only discusses media when used with
object interior. The complete syntax and an explanation of all of the
parameters and options for `media` is given in the section
Media.

Typically the object itself is given a fully transparent texture however
media also works in partially transparent objects. The texture pattern itself
does not effect the interior media except perhaps to create shadows on it.
The texture pattern of an object applies only to the surface shell. Any
interior media patterns are totally independent of the texture.

In previous versions of POV-Ray, this feature was called `halo`
and was part of the `texture` specification along with `pigment`, `normal`, and `finish`. See the section: Why are Interior and Media Necessary? for an explanation of the reasons for the change.

Media may also be specified outside an object to simulate atmospheric
media. There is no constraining object in this case. If you only want media
effects in a particular area, you should use object media rather than only
relying upon the media pattern. In general it will be faster and more
accurate because it only calculates inside the constraining object. See
Atmospheric Media for
details on unconstrained uses of media.

You may specify more than one `media` statement per `interior` statement. In that case, all of the media participate and
where they overlap, they add together.

Any object which is supposed to have media effects inside it, whether those
effects are object media or atmospheric media, must have the `hollow on`
keyword applied. Otherwise the media is blocked. See the section: Empty and Solid Objects
for details.

### 3.4.8.2 Media Types

There are three types of particle interaction in `media`: absorbing, emitting, and scattering. All three activities may occur in a single media. Each of these three specifications requires a color. Only the red, green, and blue components of the color are used. The filter and transmit values are ignored. For this reason it is permissible to use one float value to specify an intensity of white color. For example, the following two lines are legal and produce the same results:

```

emission 0.75
emission rgb <0.75,0.75,0.75>
```

3.4.8.2.1 Absorption

The `absorption` keyword specifies a color of light which is absorbed when looking through the media. For example, `absorption rgb<0,1,0>` blocks the green light but permits red and blue to get through. Therefore a white object behind the media will appear magenta. The default value is `rgb<0,0,0>` which means no light is absorbed, meaning all light passes through normally.

3.4.8.2.2 Emission

The `emission` keyword specifies the color of the light emitted from the particles. Particles which emit light are visible without requiring additional illumination. However, they will only illuminate other objects if radiosity is used with media on. This is similar to an object with high `ambient` values. The default value is `rgb<0,0,0>` which means no light is emitted.

3.4.8.2.3 Scattering

The syntax of a `scattering` statement is:

```

SCATTERING:
 scattering {
 Type, COLOR [ eccentricity Value ] [ extinction Value ]
 }
```

The first float value specifies the type of scattering. This is followed by the color of the scattered light. The default value if no `scattering` statement is given is `rgb <0,0,0>` which means no scattering occurs.

The scattering effect is only visible when light is shining on the media from a light source. This is similar to `diffuse` reflection off of an object. In addition to reflecting light, scattering media also absorbs light like an `absorption` media. The balance between how much absorption occurs for a given amount of scattering is controlled by the optional `extinction` keyword and a single float value. The default value of 1.0 gives an extinction effect that matches the scattering. Values such as `extinction 0.25` give 25% the normal amount. Using `extinction 0.0` turns it off completely. Any value other than the 1.0 default is contrary to the real physical model but decreasing extinction can give you more artistic flexibility.

The integer value `Type` specifies one of five different scattering phase functions representing the different models: isotropic, Mie (haze and murky atmosphere), Rayleigh, and Henyey-Greenstein.

Type 1, isotropic scattering is the simplest form of scattering because it is independent of direction. The amount of light scattered by particles in the atmosphere does not depend on the angle between the viewing direction and the incoming light.

Types 2 and 3 are Mie haze and Mie murky scattering which are used for relatively small particles such as minuscule water droplets of fog, cloud particles, and particles responsible for the polluted sky. In this model the scattering is extremely directional in the forward direction, i.e. the amount of scattered light is largest when the incident light is anti-parallel to the viewing direction (the light goes directly to the viewer). It is smallest when the incident light is parallel to the viewing direction. The haze and murky atmosphere models differ in their scattering characteristics. The murky model is much more directional than the haze model.

| The Mie haze scattering function |

| The Mie murky scattering function |

Type 4 Rayleigh scattering models the scattering for extremely small particles such as molecules of the air. The amount of scattered light depends on the incident light angle. It is largest when the incident light is parallel or anti-parallel to the viewing direction and smallest when the incident light is perpendicular to the viewing direction. You should note that the Rayleigh model used in POV-Ray does not take the dependency of scattering on the wavelength into account.

| The Rayleigh scattering function |

Type 5 is the Henyey-Greenstein scattering model. It is based on an analytical function and can be used to model a large variety of different scattering types. The function models an ellipse with a given eccentricity e. This eccentricity is specified by the optional keyword `eccentricity` which is only used for scattering type five. The default eccentricity value of zero defines isotropic scattering while positive values lead to scattering in the direction of the light and negative values lead to scattering in the opposite direction of the light. Larger values of e (or smaller values in the negative case) increase the directional property of the scattering.

| The Henyey-Greenstein scattering function for different eccentricity values |

Note: See the section on Light Groups for additional information when using scattering media in a light group.

### 3.4.8.3 Sampling Parameters & Methods

Media effects are calculated by sampling the media along the path of the ray. It uses a process called Monte Carlo integration. POV-Ray provides three different types of media sampling. The `method` keyword lets you specify what sampling type is used.

Note: As of version 3.5 the default sampling `method` is 3, and it's default for `intervals` is 1. Sampling methods 1 and 2 have been retained for legacy purposes.

Sample `method 3` uses adaptive sampling (similar to adaptive anti-aliasing) which is very much like the sampling method used in POV-Ray 3.0 atmosphere. This code was written from the ground-up to work with media. However, adaptive sampling works by taking another sample between two existing samples if there is too much variance in the original two samples. This leads to fewer samples being taken in areas where the effect from the media remains constant. The adaptive sampling is only performed if the minimum samples are set to 3 or more.

You can specify the anti-aliasing recursion depth using the `aa_level` keyword followed by an integer. You can specify the anti-aliasing threshold by using the `aa_threshold` followed by a float. The default for `aa_level` is 4 and the default `aa_threshold` is 0.1. `jitter` also works with method 3.

Note: It is usually best to only use one interval with method 3. Too many intervals can lead to artifacts, and POV will create more intervals if it needs them.

Sample `method 1` used the `intervals` keyword to specify the integer number of intervals used to sample the ray. For object media, the intervals are spread between the entry and exit points as the ray passes through the container object. For atmospheric media, the intervals spans the entire length of the ray from its start until it hits an object. For media types which interact with spotlights or cylinder lights, the intervals which are not illuminated by these light types are weighted differently than the illuminated intervals when distributing samples.

The `ratio` keyword distributes intervals differently between lit and unlit areas. The default value of `ratio 0.9` means that lit intervals get more samples than unlit intervals. Note that the total number of intervals must exceed the number of illuminated intervals. If a ray passes in and out of 8 spotlights but you have only specified 5 intervals then an error occurs.

The `samples` `Min`, `Max` keyword specifies the minimum and maximum number of samples taken per interval. The default values are `samples 1,1`. The value for Max may be omitted, in which case the range Min = Max will be used.

As each interval is sampled, the variance is computed. If the variance is below a threshold value, then no more samples are needed. The `variance` and `confidence` keywords specify the permitted variance allowed and the confidence that you are within that variance. The exact calculations are quite complex and involve chi-squared tests and other statistical principles too messy to describe here. The default values are `variance 1.0/128` and `confidence
0.9`. For slower more accurate results, decrease the variance and increase the confidence.

Note: The maximum number of samples limits the calculations even if the proper variance and confidence are never reached.

Sample `method 2` distributed samples evenly along the viewing ray or light ray. The latter can make things look smoother sometimes. If you specify a maximum number of samples higher than the minimum number of samples, POV will take additional samples, but they will be random, just like in method 1. Therefore, it is suggested you set the max samples equal to the minimum samples.
`jitter` will cause method 2 to look similar to method 1. It should be followed by a float, and a value of 1 will stagger the samples in the full range between samples.

### 3.4.8.4 Density

Particles of media are normally distributed in constant density throughout the media. However, the `density` statement allows you to vary the density across space using any of POV-Ray's pattern functions such as those used in textures. If no `density` statement is given then the density remains a constant value of 1.0 throughout the media. More than one `density` may be specified per `media` statement. See Multiple Density vs. Multiple Media.

The syntax for `density` is:

```

DENSITY:
 density {
 [DENSITY_IDENTIFIER]
 [DENSITY_TYPE]
 [DENSITY_MODIFIER...]
 }

DENSITY_TYPE:
 PATTERN_TYPE | COLOR
 DENSITY_MODIFIER:
 PATTERN_MODIFIER | DENSITY_LIST | color_map { COLOR_MAP_BODY } |
 colour_map { COLOR_MAP_BODY } | density_map { DENSITY_MAP_BODY }
```

The `density` statement may begin with an optional density identifier. All subsequent values modify the defaults or the values in the identifier. The next item is a pattern type. This is any one of POV-Ray's pattern functions such as `bozo`, `wood`, `gradient`, `waves`, etc. Of particular usefulness are the `spherical`, `planar`, `cylindrical`, and `boxed` patterns which were previously available only for use with our discontinued `halo` feature. All patterns return a value from 0.0 to 1.0. This value is interpreted as the density of the media at that particular point. See the section Pattern for details on particular pattern types. Although a solid COLOR pattern is legal, in general it is used only when the `density` statement is inside a `density_map`.

3.4.8.4.1 General Density Modifiers

A `density` statement may be modified by any of the general pattern modifiers such as transformations, `turbulence` and `warp`. See Pattern Modifiers for details. In addition, there are several density-specific modifiers which can be used.

3.4.8.4.2 Density with color_map

Typically, a `media` uses just one constant color throughout. Even if you vary the density, it is usually just one color which is specified by the `absorption`, `emission`, or `scattering` keywords. However, when using `emission` to simulate fire or explosions, the center of the flame (high density area) is typically brighter and white or yellow. The outer edge of the flame (less density) fades to orange, red, or in some cases deep blue. To model the density-dependent change in color which is visible, you may specify a `color_map`. The pattern function returns a value from 0.0 to 1.0 and the value is passed to the color map to compute what color or blend of colors is used. See Color Maps for details on how pattern values work with `color_map`. This resulting color is multiplied by the `absorption`, `emission` and `scattering` color. Currently there is no way to specify different color maps for each media type within the same `media` statement.

Consider this example:

```

media {
 emission 0.75
 scattering {1, 0.5}
 density {
 spherical
 color_map {
 [0.0 rgb <0,0,0.5>]
 [0.5 rgb <0.8, 0.8, 0.4>]
 [1.0 rgb <1,1,1>]
 }
 }
 }
```

The color map ranges from white at density 1.0 to bright yellow at density 0.5 to deep blue at density 0. Assume we sample a point at density 0.5. The emission is 0.75*<0.8,0.8,0.4> or <0.6,0.6,0.3>. Similarly the scattering color is 0.5*<0.8,0.8,0.4> or <0.4,0.4,0.2>.

For block pattern types `checker`, `hexagon`, and `brick` you may specify a color list such as this:

```

density {
 checker
 density {rgb<1,0,0>}
 density {rgb<0,0,0>}
 }
```

See Color List Pigments which describes how `pigment` uses a color list. The same principles apply when using them with `density`.

3.4.8.4.3 Density Maps and Density Lists

In addition to specifying blended colors with a color map you may create a blend of densities using a `density_map`. The syntax for a density map is identical to a color map except you specify a density in each map entry (and not a color).

The syntax for `density_map` is as follows:

```

DENSITY_MAP:
 density_map { DENSITY_MAP_BODY }
DENSITY_MAP_BODY:
 DENSITY_MAP_IDENTIFIER | DENSITY_MAP_ENTRY...
DENSITY_MAP_ENTRY:
 [ Value DENSITY_BODY ]
```

Where `Value` is a float value between 0.0 and 1.0 inclusive and each DENSITY_BODY is anything which can be inside a `density{...}` statement. The `density` keyword and `{}` braces need not be specified.

Note: The `[]` brackets are part of the actual DENSITY_MAP_ENTRY. They are not notational symbols denoting optional parts. The brackets surround each entry in the density map.

There may be from 2 to 256 entries in the map.

Density maps may be nested to any level of complexity you desire. The densities in a map may have color maps or density maps or any type of density you want.

Density lists may also be used with block patterns such as `checker`, `hexagon` and `brick`, as well as the object pattern `object`.

For example:

```

density {
 checker
 density { Flame scale .8 }
 density { Fire scale .5 }
 }
```

Note: In the case of block patterns the `density` wrapping is required around the density information.

A density map is also used with the `average` density type. See Average for details.

You may declare and use density map identifiers but the only way to declare a density block pattern list is to declare a density identifier for the entire density.

3.4.8.4.4 Multiple Density vs. Multiple Media

It is possible to have more than one `media` specified per object and it is legal to have more than one `density` per `media`. The effects are quite different.

Consider this example:

```

object {
 MyObject
 pigment { rgbf 1 }
 interior {
 media {
 density { Some_Density }
 density { Another_Density }
 }
 }
 }
```

As the media is sampled, calculations are performed for each density pattern at each sample point. The resulting samples are multiplied together. Suppose one density returned `rgb<.8,.8,.4>` and the other returned `rgb<.25,.25,0>`. The resulting color is `rgb<.2,.2,0>`.

Note: In areas where one density returns zero, it will wipe out the other density. The end result is that only density areas which overlap will be visible. This is similar to a CSG intersection operation. Now consider:

```

object {
 MyObject
 pigment { rgbf 1 }
 interior {
 media {
 density { Some_Density }
 }
 media {
 density { Another_Density }
 }
 }
 }
```

In this case each media is computed independently. The resulting colors are added together. Suppose one density and media returned `rgb<.8,.8,.4>` and the other returned `rgb<.25,.25,0>`. The resulting color is `rgb<1.05,1.05,.4>`. The end result is that density areas which overlap will be especially bright and all areas will be visible. This is similar to a CSG union operation. See the sample scene `~scenes\interior\media\media4.pov` for an example which illustrates this.

## 3.4.9 Include Files

This section covers the include files that come with every distribution of POV-Ray. File location varies, so see your platform specific documentation for more information.

### 3.4.9.1 Main Files

The main include files in alphabetical order:

3.4.9.1.1 Arrays.inc

This file contains macros for manipulating arrays.

`ARRAYS_WriteDF3(Array, FileName, BitDepth)`: Write an array to a df3 file.

Parameters:

 `Array` = The array that contains the data.
 `FileName` = The name of the file to be written.
 `BitDepth` = The size of the binary word.

Note: See the #write directive for more information.

`Rand_Array_Item(Array, Stream)`: Randomly Picks an item from a 1D array.

Parameters:

 `Array` = The array from which to choose the item.
 `Stream` = A random number stream.

`Resize_Array(Array, NewSize)`: Resize a 1D array, retaining its contents.

Parameters:

 `Array` = The array to be resized.
 `NewSize` = The desired new size of the array.

`Reverse_Array(Array)`: Reverses the order of items in a 1D array.

Parameters:

 `Array` = The array to be reversed.

`Sort_Compare(Array, IdxA, IdxB)`: This macro is used by the `Sort_Array()`
and `Sort_Partial_Array()` macros. The given macro works for 1D arrays of floats, but
you can redefine it in your scene file for more complex situations, arrays of vectors or
multidimensional arrays for example. Just make sure your macro returns true if the item at IdxA <
the item at IdxB, and otherwise returns false.

Parameters:

 `Array` = The array containing the data being sorted.
 `IdxA, IdxB` = The array offsets of the data elements being compared.

`Sort_Swap_Data(Array, IdxA, IdxB)`: This macro is used by the `Sort_Array()`
and `Sort_Partial_Array()` macros. The given macro works for 1D arrays only,
but you can redefine it in your scene file to handle multidimensional arrays if needed.
The only requirement is that your macro swaps the data at IdxA with that at IdxB.

Parameters:

 `Array` = The array containing the data being sorted.
 `IdxA, IdxB` = The array offsets of the data elements being swapped.

`Sort_Array(Array)`: This macro sorts a 1D array of floats, though you can
redefine the `Sort_Compare()` and `Sort_Swap_Data()` macros to
handle multidimensional arrays and other data types.

Parameters:

 `Array` = The array to be sorted.

`Sort_Partial_Array(Array, FirstInd, LastInd)`: This macro is like
`Sort_Array()`, but sorts a specific range of an array instead of the whole array.

Parameters:

 `Array` = The array to be sorted.
 `FirstInd, LastInd` = The start and end indices of the range being sorted.

3.4.9.1.2 Chars.inc

This file includes 26 upper-case letter and other characters defined as objects.
The size of all characters is 4 * 5 * 1. The center of the bottom side of a character
face is set to the origin, so you may need to translate a character appropriately
before rotating it about the x or z axes.

Letters:

`char_A, char_B, char_C,
char_D, char_E, char_F,
char_G, char_H, char_I,
char_J, char_K, char_L,
char_M, char_N, char_O,
char_P, char_Q, char_R,
char_S, char_T, char_U,
char_V, char_W, char_X,
char_Y, char_Z`

Numerals:

`char_0, char_1,
char_2, char_3,
char_4, char_5,
char_6, char_7,
char_8, char_9`

Symbols:

`char_Dash, char_Plus, char_ExclPt,
char_Amps, char_Num, char_Dol,
char_Perc, char_Astr, char_Hat,
char_LPar, char_RPar, char_AtSign,
char_LSqu, char_RSqu`

Usage:

```

#include "chars.inc"
.
.
object {char_A ...}
```

3.4.9.1.3 Colors.inc

This file is mainly a list of predefined colors, but also has a few color
manipulation macros.

3.4.9.1.3.1 Predefined colors

This file contains 127 predefined colors that you can use in your scenes. Simply `#include` them in your scene file to use them:

```

 #include "colors.inc"
```

These basic colors:

 Red
 Green
 Blue
 Yellow
 Cyan
 Magenta
 Clear
 White
 Black

A series of percentage grays that are useful for fine-tuning lighting color values and for other areas where subtle variations of grays are needed, and a palette 99 additional color definitions are available. See the distribution file `~include/colors.inc` for more details.

3.4.9.1.3.2 Color macros

In POV-Ray all colors are handled in RGB color space with a component for
the amount of red, green and blue light. However, not everybody thinks this
is the most intuitive way to specify colors. For your convenience there are
macros included in colors.inc that converts between a few different types of
color spaces.

The three supported color spaces:

`RGB` = < Red, Green, Blue, Filter, Transmit >
`HSL` = < Hue, Saturation, Lightness, Filter, Transmit >
`HSV` = < Hue, Saturation, Value, Filter, Transmit >

Note: The Hue parameter is given in degrees.

`CHSL2RGB(Color)`: Converts a color given in `HSL` space to one in `RGB` space.

Parameters:

`Color` = `HSL` color to be converted.

`CRGB2HSL(Color)`: Converts a color given in `RGB` space to one in `HSL` space.

Parameters:

`Color` = `RGB` color to be converted.

`CHSV2RGB(Color)`: Converts a color given in `HSV` space to one in `RGB` space.

Parameters:

`Color` = `HSV` color to be converted.

`CRGB2HSV(Color)`: Converts a color given in `RGB` space to one in `HSV` space.

Parameters:

`Color` = `RGB` color to be converted.

`Convert_Color(SourceType, DestType, Color)`: Converts a color from one color space to another. Color spaces available are: `RGB`, `HSL`, and `HSV`:

Parameters:

 `SourceType` = Color space of input color.
 `DestType` = Desired output color space.
 `Color` = Color to be converted, in SourceType color space.

3.4.9.1.4 Consts.inc

This file defines a number of constants, including things such as mapping types and ior definitions.

3.4.9.1.4.1 Vector constants

`o` = < 0, 0, 0> (origin)
`xy` = < 1, 1, 0>
`yz` = < 0, 1, 1>
`xz` = < 1, 0, 1>

3.4.9.1.4.2 Map type constants

`Plane_Map` = 0
`Sphere_Map` = 1
`Cylinder_Map` = 2
`Torus_Map` = 5

3.4.9.1.4.3 Interpolation type constants

`Bi` = 2
`Norm` = 4

3.4.9.1.4.4 Fog type constants

`Uniform_Fog` = 1
`Ground_Fog` = 2

3.4.9.1.4.5 Focal blur hexgrid constants

`Hex_Blur1` = 7
`Hex_Blur2` = 19
`Hex_Blur3` = 37

3.4.9.1.4.6 IORs

`Air_Ior` = 1.000292
`Amethyst_Ior` = 1.550
`Apatite_Ior` = 1.635
`Aquamarine_Ior` = 1.575
`Beryl_Ior` = 1.575
`Citrine_Ior` = 1.550
`Crown_Glass_Ior` = 1.51
`Corundum_Ior` = 1.765
`Diamond_Ior` = 2.47
`Emerald_Ior` = 1.575
`Flint_Glass_Ior` = 1.71
`Flint_Glass_Heavy_Ior` = 1.8
`Flint_Glass_Medium_Ior` = 1.63
`Flint_Glass_Light_Ior` = 1.6
`Fluorite_Ior` = 1.434
`Gypsum_Ior` = 1.525
`Ice_Ior` = 1.31
`Plexiglas_Ior` = 1.5
`Quartz_Ior` = 1.550
`Quartz_Glass_Ior`= 1.458
`Ruby_Ior` = 1.765
`Salt_Ior` = 1.544
`Sapphire_Ior` = 1.765
`Topaz_Ior` = 1.620
`Tourmaline_Ior` = 1.650
`Water_Ior` = 1.33

3.4.9.1.4.7 Dispersion amounts

`Quartz_Glass_Dispersion` = 1.012
`Water_Dispersion` = 1.007
`Diamond_Dispersion` = 1.035
`Sapphire_Dispersion` = 1.015

3.4.9.1.4.8 Scattering media type constants

`ISOTROPIC_SCATTERING` = 1;
`MIE_HAZY_SCATTERING` = 2;
`MIE_MURKY_SCATTERING` = 3;
`RAYLEIGH_SCATTERING` = 4;
`HENYEY_GREENSTEIN_SCATTERING` = 5;

3.4.9.1.5 Debug.inc

This file contains a set of macros designed to make debugging easier. It also functions like the old debug.inc, with the exception that you have to call the `Debug_Inc_Stack()` macro to get the include stack output.

`Debug_Inc_Stack()`: Activates include file tracking, each included file will send a debug message when it is included.

Parameters:

 None.

`Set_Debug(Bool)`: Activate or deactivate the debugging macros.

Parameters:

 `Bool` = A boolean (true/false) value.

`Debug_Message(Str)`: If debugging, sends the message to the debug stream.

Parameters:

 `Str` = The desired message.

`Debug(Condition, Message)`: Sends a message to the `#debug` stream depending on a given condition.

Parameters:

 `Condition` = Any boolean expression.
 `Message` = The message to be sent if Condition evaluates as true.

`Warning(Condition, Message)`: Sends a message to the `#warning` stream depending on a given condition.

Parameters:

 `Condition` = Any boolean expression.
 `Message` = The message to be sent if Condition evaluates as true.

`Error(Condition, Message)`: Sends a message to the `#error` stream depending on a given condition.

Parameters:

 `Condition` = Any boolean expression.
 `Message` = The message to be sent if Condition evaluates as true.

3.4.9.1.6 Finish.inc

This file contains some predefined finishes.

`Dull`
Dull, with a large, soft specular highlight.

`Shiny`
Shiny, with a small, tight specular highlight.

`Glossy`
Very shiny with very tight specular highlights and a fair amount of reflection.

`Phong_Dull`
Dull, with a large, soft phong highlight.

`Phong_Shiny`
Shiny, with a small, tight phong highlight.

`Phong_Glossy`
Very shiny with very tight phong highlights and a fair amount of reflection.

`Luminous`
A glowing surface, unaffected by light_sources.

`Mirror`
A perfectly reflective surface, no highlights or shading.

3.4.9.1.7 Functions.inc

This include file contains interfaces to internal functions as well as several predefined functions. The ID's used to access the internal
functions through calls to internal(XX), are not guaranteed to stay the same between POV-Ray versions, so users are encouraged to
use the functions declared here.

The number of required parameters and what they control are also given in the include file, this chapter gives more information. For starter
values of the parameters, see the `~scenes/incdemo/i_internal.pov` demo file.

Syntax to be used:

```

#include "functions.inc"
isosurface {
 function { f_torus_gumdrop(x,y,z, P0) }
 ...
 }

pigment {
 function { f_cross_ellipsoids(x,y,z, P0, P1, P2, P3) }
 COLOR_MAP ...
 }
```

Some special parameters are found in several of these functions. These are described in the next section and later referred to as
Cross section type, Field Strength, Field Limit, SOR parameters.

3.4.9.1.7.1 Common Parameters

3.4.9.1.7.2 Cross Section Type

In the helixes and spiral functions, the 9th parameter is the cross section type.

Some shapes are:

`0`: square
`0.0 to 1.0`: rounded squares
`1`: circle
`1.0 to 2.0`: rounded diamonds
`2`: diamond
`2.0 to 3.0`: partially concave diamonds
`3`: concave diamond

3.4.9.1.7.3 Field Strength

The numerical value at a point in space generated by the function is multiplied by the Field Strength. The set of points where the
function evaluates to zero are unaffected by any positive value of this parameter, so if you are just using the function on its own with
threshold = 0, the generated surface is still the same.

In some cases, the field strength has a considerable effect on the speed and accuracy of rendering the surface. In general, increasing
the field strength speeds up the rendering, but if you set the value too high the surface starts to break up and may disappear
completely.

Setting the field strength to a negative value produces the inverse of the surface, like making the function negative.

3.4.9.1.7.4 Field Limit

This will not make any difference to the generated surface if you are using threshold that is within the field limit (and will kill the
surface completely if the threshold is greater than the field limit). However, it may make a huge difference to the rendering times.

If you use the function to generate a pigment, then all points that are a long way from the surface will have the same color, the color
that corresponds to the numerical value of the field limit.

3.4.9.1.7.5 SOR Switch

If greater than zero, the curve is swept out as a surface of revolution (SOR). If the value is zero or negative, the curve is extruded
linearly in the Z direction.

3.4.9.1.7.6 SOR Offset

If the SOR switch is on, then the curve is shifted this distance in the X direction before being swept out.

3.4.9.1.7.7 SOR Angle

If the SOR switch is on, then the curve is rotated this number of degrees about the Z axis before being swept out.

3.4.9.1.7.8 Invert Isosurface

Sometimes, when you render a surface, you may find that you get only the shape of the container. This could be caused by the fact that
some of the build in functions are defined inside out.

We can invert the isosurface by negating the whole function: `-(function) - threshold`

3.4.9.1.7.9 Internal Functions

Here is a list of the internal functions in the order they appear in the functions.inc include file

`f_algbr_cyl1(x,y,z, P0, P1, P2, P3, P4)`: An algebraic cylinder is what you get if you take any 2d curve and plot it in 3d.
The 2d curve is simply extruded along the third axis, in this case the z axis. With the SOR Switch switched on, the figure-of-eight curve
will be rotated around the Y axis instead of being extruded along the Z axis.

`P0` : Field Strength
`P1` : Field Limit
`P2` : SOR Switch
`P3` : SOR Offset
`P4` : SOR Angle

`f_algbr_cyl2(x,y,z, P0, P1, P2, P3, P4)`: An algebraic cylinder is what you
get if you take any 2d curve and plot it in 3d.
The 2d curve is simply extruded along the third axis, in this case the z axis.With the SOR Switch switched on, the cross section curve will
be rotated around the Y axis instead of being extruded along the Z axis.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Field Limit
`P2` : SOR Switch
`P3` : SOR Offset
`P4` : SOR Angle

`f_algbr_cyl3(x,y,z, P0, P1, P2, P3, P4)`: An algebraic cylinder is what you get
if you take any 2d curve and plot it in 3d. The 2d curve
is simply extruded along the third axis, in this case the Z axis. With the SOR Switch switched on, the cross section curve will be rotated
around the Y axis instead of being extruded along the Z axis.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Field Limit
`P2` : SOR Switch
`P3` : SOR Offset
`P4` : SOR Angle

`f_algbr_cyl4(x,y,z, P0, P1, P2, P3, P4)`: An algebraic cylinder is what you get
if you take any 2d curve and plot it in 3d. The 2d curve
is simply extruded along the third axis, in this case the z axis. With the SOR Switch switched on, the cross section curve will be rotated
around the Y axis instead of being extruded along the Z axis.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Field Limit
`P2` : SOR Switch
`P3` : SOR Offset
`P4` : SOR Angle

`f_bicorn(x,y,z, P0, P1)`: The surface is a surface of revolution.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Scale. The mathematics of this surface suggest that the shape should be different
for different values of this parameter. In practice the difference in shape is hard to spot.
Setting the scale to 3 gives a surface with a radius of about 1 unit

`f_bifolia(x,y,z, P0, P1)`: The bifolia surface looks something like the top part of
a a paraboloid bounded below by another paraboloid.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Scale. The surface is always the same shape. Changing this parameter has the
same effect as adding a scale modifier. Setting the scale to 1 gives a surface with a radius
of about 1 unit

`f_blob(x,y,z, P0, P1, P2, P3, P4)`: This function generates blobs that are
similar to a CSG blob with two spherical components. This function only seems to work
with negative threshold settings.

`P0` : X distance between the two components
`P1` : Blob strength of component 1
`P2` : Inverse blob radius of component 1
`P3` : Blob strength of component 2
`P4` : Inverse blob radius of component 2

`f_blob2(x,y,z, P0, P1, P2, P3)`: The surface is similar to a CSG blob
with two spherical components.

`P0` : Separation. One blob component is at the origin, and the other is this distance away on the X axis
`P1` : Inverse size. Increase this to decrease the size of the surface
`P2` : Blob strength
`P3` : Threshold. Setting this parameter to 1 and the threshold to zero has exactly the same effect as setting this parameter to zero and the threshold to -1

`f_boy_surface(x,y,z, P0, P1)`: For this surface, it helps if the field strength
is set low, otherwise the surface has a tendency to break up or disappear entirely. This has
the side effect of making the rendering times extremely long.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Scale. The surface is always the same shape. Changing this parameter has the
same effect as adding a scale modifier

`f_comma(x,y,z, P0)`: The comma surface is very much like a comma-shape.

`P0` : Scale

`f_cross_ellipsoids(x,y,z, P0, P1, P2, P3)`: The cross ellipsoids surface is
like the union of three crossed ellipsoids, one oriented along each axis.

`P0` : Eccentricity. When less than 1, the ellipsoids are oblate, when greater than 1 the
ellipsoids are prolate, when zero the ellipsoids are spherical (and hence the whole surface is a sphere)
`P1` : Inverse size. Increase this to decrease the size of the surface
`P2` : Diameter. Increase this to increase the size of the ellipsoids
`P3` : Threshold. Setting this parameter to 1 and the threshold to zero has exactly the same
effect as setting this parameter to zero and the threshold to -1

`f_crossed_trough(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_cubic_saddle(x,y,z, P0)`: For this surface, it helps if the field strength is set quite low, otherwise the surface has a
tendency to break up or disappear entirely.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_cushion(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_devils_curve(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_devils_curve_2d(x,y,z, P0, P1, P2, P3, P4, P5)`: The `f_devils_curve_2d` curve can be
extruded along the z axis, or using the SOR parameters it can be made into a surface of revolution.
The X and Y factors control the size of the central feature.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : X factor
`P2` : Y factor
`P3` : SOR Switch
`P4` : SOR Offset
`P5` : SOR Angle

`f_dupin_cyclid(x,y,z, P0, P1, P2, P3, P4, P5)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Major radius of torus
`P2` : Minor radius of torus
`P3` : X displacement of torus
`P4` : Y displacement of torus
`P5` : Radius of inversion

`f_ellipsoid(x,y,z, P0, P1, P2)`: `f_ellipsoid` generates spheres and ellipsoids. Needs `threshold 1`.
Setting these scaling parameters to 1/n gives exactly the same effect as performing a scale operation to increase the scaling by n
in the corresponding direction.

`P0` : X scale (inverse)
`P1` : Y scale (inverse)
`P2` : Z scale (inverse)

`f_enneper(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_flange_cover(x,y,z, P0, P1, P2, P3)`:

`P0` : Spikiness. Set this to very low values to increase the spikes. Set it to 1 and you get a sphere
`P1` : Inverse size. Increase this to decrease the size of the surface. (The other parameters
also drastically affect the size, but this parameter has no other effects)
`P2` : Flange. Increase this to increase the flanges that appear between the spikes. Set it to 1 for no flanges
`P3` : Threshold. Setting this parameter to 1 and the threshold to zero has exactly the
same effect as setting this parameter to zero and the threshold to -1

`f_folium_surface(x,y,z, P0, P1, P2)`: A folium surface looks something like a paraboloid glued to a plane.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Neck width factor - the larger you set this, the narrower the neck where the
paraboloid meets the plane
`P2` : Divergence - the higher you set this value, the wider the paraboloid gets

`f_folium_surface_2d(x,y,z, P0, P1, P2, P3, P4, P5)`: The `f_folium_surface_2d` curve can be
rotated around the X axis to generate the same 3d surface as the `f_folium_surface`, or it can be extruded
in the Z direction (by switching the SOR switch off)

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Neck width factor - same as the 3d surface if you are revolving it around the Y axis
`P2` : Divergence - same as the 3d surface if you are revolving it around the Y axis
`P3` : SOR Switch
`P4` : SOR Offset
`P5` : SOR Angle

`f_glob(x,y,z, P0)`: One part of this surface would actually go off to
infinity if it were not restricted by the contained_by shape.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_heart(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_helical_torus(x,y,z, P0, P1, P2, P3, P4, P5, P6, P7, P8, P9)`: With some sets of parameters, it looks like a torus with a
helical winding around it. The
winding optionally has grooves around the outside.

`P0` : Major radius
`P1` : Number of winding loops
`P2` : Twistiness of winding. When zero, each winding loop is separate. When set to one,
each loop twists into the next one. When set to two, each loop twists into the one after next
`P3` : Fatness of winding?
`P4` : Threshold. Setting this parameter to 1 and the threshold to zero has s similar effect
as setting this parameter to zero and the threshold to 1
`P5` : Negative minor radius? Reducing this parameter increases the minor radius of the
central torus. Increasing it can make the torus disappear and be replaced by a vertical column.
The value at which the surface switches from one form to the other depends on several other parameters
`P6` : Another fatness of winding control?
`P7` : Groove period. Increase this for more grooves
`P8` : Groove amplitude. Increase this for deeper grooves
`P9` : Groove phase. Set this to zero for symmetrical grooves

`f_helix1(x,y,z, P0, P1, P2, P3, P4, P5, P6)`:

`P0` : Number of helixes - e.g. 2 for a double helix
`P1` : Period - is related to the number of turns per unit length
`P2` : Minor radius (major radius > minor radius)
`P3` : Major radius
`P4` : Shape parameter. If this is greater than 1 then the tube becomes fatter in the y direction
`P5` : Cross section type
`P6` : Cross section rotation angle (degrees)

`f_helix2(x,y,z, P0, P1, P2, P3, P4, P5, P6)`: Needs a negated function

`P0` : Not used
`P1` : Period - is related to the number of turns per unit length
`P2` : Minor radius (minor radius > major radius)
`P3` : Major radius
`P4` : Not used
`P5` : Cross section type
`P6` : Cross section rotation angle (degrees)

`f_hex_x(x,y,z, P0)`: This creates a grid of hexagonal cylinders stretching along
the z-axis. The fatness is controlled
by the threshold value. When this value equals 0.8660254 or cos(30) the sides will touch, because
this is the distance between centers. Negating the function will inverse the surface and create a
honey-comb structure. This function is also useful as pigment function.

`P0` : No effect (but the syntax requires at least one parameter)

`f_hex_y(x,y,z, P0)`: This is function forms a lattice of infinite boxes
stretching along the z-axis. The fatness is
controlled by the threshold value. These boxes are rotated 60 degrees around centers, which
are 0.8660254 or cos(30) away from each other. This function is also useful as pigment function.

`P0` : No effect (but the syntax requires at least one parameter)

`f_hetero_mf(x,y,z, P0, P1, P2, P3, P4, P5)`: `f_hetero_mf (x,0,z)` makes multifractal height fields and patterns
of 1/f noise. Multifractal refers to their characteristic of having a fractal dimension which varies with
altitude. Built from summing noise of a number of frequencies, the hetero_mf parameters determine how many, and which frequencies are to be
summed. An advantage to using these instead of a height_field {} from an image (a number of height field programs output multifractal types
of images) is that the hetero_mf function domain extends arbitrarily far in the x and z directions so huge landscapes can be made without
losing resolution or having to tile a height field. Other functions of interest are `f_ridged_mf` and `f_ridge`.

`P0` : H is the negative of the exponent of the basis noise frequencies used in building these functions (each frequency
f's amplitude is weighted by the factor f - H ). In landscapes, and many natural forms, the amplitude of high frequency
contributions are usually less than the lower frequencies. When H is 1, the fractalization is relatively smooth (1/f noise). As H
nears 0, the high frequencies contribute equally with low frequencies as in white noise.
`P1` : Lacunarity is the multiplier used to get from one octave to the next. This parameter affects the
size of the frequency gaps in the pattern. Make this greater than 1.0
`P2` : Octaves is the number of different frequencies added to the fractal. Each Octave frequency is the previous
one multiplied by Lacunarity, so that using a large number of octaves can get into very high frequencies very quickly.
`P3` : Offset is the base altitude (sea level) used for the heterogeneous scaling
`P4` : T scales the heterogeneity of the fractal. T=0 gives straight 1/f (no heterogeneous
scaling). T=1 suppresses higher frequencies at lower altitudes
`P5` : Generator type used to generate the noise3d. 0, 1, 2 and 3 are legal values.

`f_hunt_surface(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_hyperbolic_torus(x,y,z, P0, P1, P2)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Major radius: separation between the centers of the tubes at the closest point
`P2` : Minor radius: thickness of the tubes at the closest point

`f_isect_ellipsoids(x,y,z, P0, P1, P2, P3)`: The isect ellipsoids surface is like the
intersection of three crossed ellipsoids, one oriented along each axis.

`P0` : Eccentricity. When less than 1, the ellipsoids are oblate, when greater than 1 the
ellipsoids are prolate, when zero the ellipsoids are spherical (and hence the whole surface is a sphere)
`P1` : Inverse size. Increase this to decrease the size of the surface
`P2` : Diameter. Increase this to increase the size of the ellipsoids
`P3` : Threshold. Setting this parameter to 1 and the threshold to zero has exactly
the same effect as setting this parameter to zero and the threshold to -1

`f_kampyle_of_eudoxus(x,y,z, P0, P1, P2)`: The kampyle of eudoxus is like two infinite planes with a dimple at the
center.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Dimple: When zero, the two dimples punch right through and meet at the center.
Non-zero values give less dimpling
`P2` : Closeness: Higher values make the two planes become closer

`f_kampyle_of_eudoxus_2d(x,y,z, P0, P1, P2, P3, P4, P5)`: The 2d curve that generates the above surface can be extruded in the
Z direction or rotated about various axes by using the SOR parameters.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Dimple: When zero, the two dimples punch right through and meet at the center.
Non-zero values give less dimpling
`P2` : Closeness: Higher values make the two planes become closer
`P3` : SOR Switch
`P4` : SOR Offset
`P5` : SOR Angle

`f_klein_bottle(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_kummer_surface_v1(x,y,z, P0)`: The Kummer surface consists of a collection of radiating rods.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_kummer_surface_v2(x,y,z, P0, P1, P2, P3)`: Version 2 of the kummer surface only looks like radiating rods when the
parameters are set to particular negative values. For positive values it tends to look rather like a superellipsoid.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Rod width (negative): Setting this parameter to larger negative values increases the diameter of the rods
`P2` : Divergence (negative): Setting this number to -1 causes the rods to become
approximately cylindrical. Larger negative values cause the rods to become fatter further
from the origin. Smaller negative numbers cause the rods to become narrower away from
the origin, and have a finite length
`P3` : Influences the length of half of the rods.Changing the sign affects the other half of the rods. 0 has no effect

`f_lemniscate_of_gerono(x,y,z, P0)`: The Lemniscate of Gerono surface is an hourglass shape, or two teardrops with
their ends connected.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_lemniscate_of_gerono_2d(x,y,z, P0, P1, P2, P3, P4, P5)`: The 2d version of the Lemniscate can be extruded in the Z
direction, or used as a surface of revolution to generate the equivalent of the 3d version, or revolved in different ways.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Size: increasing this makes the 2d curve larger and less rounded
`P2` : Width: increasing this makes the 2d curve fatter
`P3` : SOR Switch
`P4` : SOR Offset
`P5` : SOR Angle

`f_mesh1(x,y,z, P0, P1, P2, P3, P4)`: The overall thickness of the threads is controlled by the isosurface threshold, not
by a parameter. If you render a mesh1 with zero threshold, the threads have zero thickness and are therefore invisible. Parameters P2 and P4
control the shape of the thread relative to this threshold parameter.

`P0` : Distance between neighboring threads in the x direction
`P1` : Distance between neighboring threads in the z direction
`P2` : Relative thickness in the x and z directions
`P3` : Amplitude of the weaving effect. Set to zero for a flat grid
`P4` : Relative thickness in the y direction

`f_mitre(x,y,z, P0)`: The Mitre surface looks a bit like an ellipsoid which has been nipped at each end with a pair
of sharp nosed pliers.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_nodal_cubic(x,y,z, P0)`: The Nodal Cubic is something like what you would get if you were to extrude the Stophid2D
curve along the X axis and then lean it over.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_noise3d(x,y,z)`:

`f_noise_generator(x,y,z, P0)`:

`P0` : Noise generator number

`f_odd(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_ovals_of_cassini(x,y,z, P0, P1, P2, P3)`: The Ovals of Cassini are a generalization of the torus shape.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Major radius - like the major radius of a torus
`P2` : Filling. Set this to zero, and you get a torus. Set this to a higher value and the hole in the middle starts to heal
up. Set it even higher and you get an ellipsoid with a dimple
`P3` : Thickness. The higher you set this value, the plumper is the result

`f_paraboloid(x,y,z, P0)`: This paraboloid is the surface of revolution that you get if you rotate a parabola about the Y
axis.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_parabolic_torus(x,y,z, P0, P1, P2)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Major radius
`P2` : Minor radius

`f_ph(x,y,z)`: When used alone, the PH function gives a surface that consists of all points that are at a particular
latitude, i.e. a cone. If you use a threshold of zero (the default) this gives a cone of width zero, which is invisible. Also look at
`f_th` and `f_r`

`f_pillow(x,y,z, P0)`:

`P0` : Field Strength

`f_piriform(x,y,z, P0)`: The piriform surface looks rather like half a lemniscate.

`P0` : Field Strength

`f_piriform_2d(x,y,z, P0, P1, P2, P3, P4, P5, P6)`: The 2d version of the Piriform can be extruded in the Z
direction, or used as a surface of revolution to generate the equivalent of the 3d version.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Size factor 1: increasing this makes the curve larger
`P2` : Size factor 2: making this less negative makes the curve larger but also thinner
`P3` : Fatness: increasing this makes the curve fatter
`P4` : SOR Switch
`P5` : SOR Offset
`P6` : SOR Angle

`f_poly4(x,y,z, P0, P1, P2, P3, P4)`: This `f_poly4` can be used to generate the surface of revolution of any
polynomial up to degree 4. To put it another way: If we call the parameters A, B, C, D, E; then this function generates the surface of
revolution formed by revolving `x = A + By + Cy2 + Dy3 + Ey4` around the Y axis.

`P0` : Constant
`P1` : Y coefficient
`P2` : Y2 coefficient
`P3` : Y3 coefficient
`P4` : Y4 coefficient

`f_polytubes(x,y,z, P0, P1, P2, P3, P4, P5)`: The Polytubes surface consists of a number of tubes. Each tube follows
a 2d curve which is specified by a polynomial of degree 4 or less. If we look at the parameters, then this function generates P0
tubes which all follow the equation `x = P1 + P2y + P3y2 + P4y3 + P5y4` arranged around the Y axis. This function needs a
positive threshold (fatness of the tubes).

`P0` : Number of tubes
`P1` : Constant
`P2` : Y coefficient
`P3` : Y2 coefficient
`P4` : Y3 coefficient
`P5` : Y4 coefficient

`f_quantum(x,y,z, P0)`: It resembles the shape of the electron density cloud for one of the d orbitals.

`P0` : Not used, but required

`f_quartic_paraboloid(x,y,z, P0)`: The Quartic Paraboloid is similar to a paraboloid, but has a squarer shape.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_quartic_saddle(x,y,z, P0)`: The Quartic saddle is similar to a saddle, but has a squarer shape.

`P0` : Field Strength

`f_quartic_cylinder(x,y,z, P0, P1, P2)`: The Quartic cylinder looks a bit like a cylinder that is swallowed an
egg.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Diameter of the egg
`P2` : Controls the width of the tube and the vertical scale of the egg

`f_r(x,y,z)`: When used alone, the R function gives a surface that consists of all the points that are a specific
distance (threshold value) from the origin, i.e. a sphere. Also look at `f_ph` and `f_th`

`f_ridge(x,y,z, P0, P1, P2, P3, P4, P5)`: This function is mainly intended for modifying other surfaces as you might use a
height field or to use as pigment function. Other functions of interest are `f_hetero_mf` and `f_ridged_mf`.

`P0` : Lambda
`P1` : Octaves
`P2` : Omega
`P3` : Offset
`P4` : Ridge
`P5` : Generator type used to generate the noise3d. 0, 1, 2 and 3 are legal values.

`f_ridged_mf(x,y,z, P0, P1, P2, P3, P4, P5)`: The Ridged Multifractal surface can be used to create multifractal
height fields and patterns. Multifractal refers to their characteristic of having a fractal dimension which varies with altitude.
They are built from summing noise of a number of frequencies. The f_ridged_mf parameters determine how many, and which frequencies are to be
summed, and how the different frequencies are weighted in the sum.

An advantage to using these instead of a `height_field{}` from an image is that the ridged_mf function domain extends
arbitrarily far in the x and z directions so huge landscapes can be made without losing resolution or having to tile a height field. Other
functions of interest are `f_hetero_mf` and `f_ridge`.

`P0` : H is the negative of the exponent of the basis noise frequencies used in building these
functions (each frequency f's amplitude is weighted by the factor fE- H ). When H is 1, the
fractalization is relatively smooth. As H nears 0, the high frequencies contribute equally with
low frequencies
`P1` : Lacunarity is the multiplier used to get from one octave to the next in the fractalization. This
parameter affects the size of the frequency gaps in the pattern. (Use values greater than 1.0)
`P2` : Octaves is the number of different frequencies added to the fractal. Each octave
frequency is the previous one multiplied by Lacunarity. So, using a large number of octaves can
get into very high frequencies very quickly
`P3` : Offset gives a fractal whose fractal dimension changes from altitude to altitude. The high
frequencies at low altitudes are more damped than at higher altitudes, so that lower altitudes are
smoother than higher areas
`P4` : Gain weights the successive contributions to the accumulated fractal result to make
creases stick up as ridges
`P5` : Generator type used to generate the noise3d. 0, 1, 2 and 3 are legal values.

`f_rounded_box(x,y,z, P0, P1, P2, P3)`: The Rounded Box is defined in a cube from <-1, -1, -1> to <1, 1, 1>. By
changing the Scale parameters, the size can be adjusted, without affecting the Radius of curvature.

`P0` : Radius of curvature. Zero gives square corners, 0.1 gives corners that match `sphere {0, 0.1}`
`P1` : Scale x
`P2` : Scale y
`P3` : Scale z

`f_sphere(x,y,z, P0)`:

`P0`: radius of the sphere

`f_spikes(x,y,z, P0, P1, P2, P3, P4)`:

`P0` : Spikiness. Set this to very low values to increase the spikes. Set it to 1 and you get a sphere
`P1` : Hollowness. Increasing this causes the sides to bend in more
`P2` : Size. Increasing this increases the size of the object
`P3` : Roundness. This parameter has a subtle effect on the roundness of the spikes
`P4` : Fatness. Increasing this makes the spikes fatter

`f_spikes_2d(x,y,z, P0, P1, P2, P3)`:

`P0` : Height of central spike
`P1` : Frequency of spikes in the X direction
`P2` : Frequency of spikes in the Z direction
`P3` : Rate at which the spikes reduce as you move away from the center

`f_spiral(x,y,z, P0, P1, P2, P3, P4, P5)`:

`P0` : Distance between windings
`P1` : Thickness
`P2` : Outer radius of the spiral. The surface behaves as if it is contained_by a sphere of this diameter
`P3` : Not used
`P4` : Not used
`P5` : Cross section type

`f_steiners_roman(x,y,z, P0)`: The Steiners Roman is composed of four identical triangular pads which together make
up a sort of rounded tetrahedron. There are creases along the X, Y and Z axes where the pads meet.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_strophoid(x,y,z, P0, P1, P2, P3)`: The Strophoid is like an infinite plane with a bulb sticking out of it.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Size of bulb. Larger values give larger bulbs. Negative values give a bulb on the other side of the plane
`P2` : Sharpness. When zero, the bulb is like a sphere that just touches the plane. When
positive, there is a crossover point. When negative the bulb simply bulges out of the plane like a pimple
`P3` : Flatness. Higher values make the top end of the bulb fatter

`f_strophoid_2d(x,y,z, P0, P1, P2, P3, P4, P5, P6)`: The 2d strophoid curve can be extruded in the Z direction or rotated
about various axes by using the SOR parameters.

`P0` : Field Strength
`P1` : Size of bulb. Larger values give larger bulbs. Negative values give a bulb on the other side of the plane
`P2` : Sharpness. When zero, the bulb is like a sphere that just touches the plane. When positive, there is a crossover
point. When negative the bulb simply bulges out of the plane like a pimple
`P3` : Fatness. Higher values make the top end of the bulb fatter
`P4` : SOR Switch
`P5` : SOR Offset
`P6` : SOR Angle

`f_superellipsoid(x,y,z, P0, P1)`: Needs a negative field strength or a negated function.

`P0` : east-west exponentx
`P1` : north-south exponent

`f_th(x,y,z)`: `f_th()` is a function that is only useful when combined with other surfaces. It produces a value
which is equal to the theta angle, in radians, at any point. The theta angle is like the longitude coordinate on the Earth. It
stays the same as you move north or south, but varies from east to west. Also look at `f_ph` and `f_r`

`f_torus(x,y,z, P0, P1)`:

`P0` : Major radius
`P1` : Minor radius

`f_torus2(x,y,z, P0, P1, P2)`: This is different from the f_torus function which just has the major and minor radii as
parameters.

`P0` : Field Strength (Needs a negative field strength or a
 negated function)
`P1` : Major radius
`P2` : Minor radius

`f_torus_gumdrop(x,y,z, P0)`: The Torus Gumdrop surface is something like a torus with a couple of gumdrops hanging
off the end.

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_umbrella(x,y,z, P0)`:

`P0` : Field Strength (Needs a negative field strength or a negated function)

`f_witch_of_agnesi(x,y,z, P0, P1, P2, P3, P4, P5)`: The Witch of Agnesi surface looks something like a witches
hat.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Controls the width of the spike. The height of the spike is always about 1 unit

`f_witch_of_agnesi_2d(x,y,z, P0, P1, P2, P3, P4, P5)`: The 2d version of the Witch of Agnesi curve can be extruded in
the Z direction or rotated about various axes by use of the SOR parameters.

`P0` : Field Strength (Needs a negative field strength or a negated function)
`P1` : Controls the size of the spike
`P2` : Controls the height of the spike
`P3` : SOR Switch
`P4` : SOR Offset
`P5` : SOR Angle

3.4.9.1.7.10 Pre defined functions

`eval_pigment(Pigm, Vect)`: This macro evaluates the color of a pigment at a specific point. Some pigments require more
information than simply a point, slope pattern based pigments for example, and will not work with this macro. However, most pigments will
work fine.

Parameters:

 `Vect` = The point at which to evaluate the pigment.
 `Pigm` = The pigment to evaluate.

`f_snoise3d(x, y, z)`: Just like f_noise3d(), but returns values in the range [-1, 1].

`f_sine_wave(val, amplitude, frequency)`: Turns a ramping waveform into a sine waveform.

`f_scallop_wave(val, amplitude, frequency)`: Turns a ramping waveform into a scallop wave waveform.

3.4.9.1.7.11 Pattern functions

Predefined pattern functions, useful for building custom function patterns or performing displacement mapping on isosurfaces.
Many of them are not really useful for these purposes, they are simply included for completeness.

Some are not implemented at all because they require special parameters that must be specified in the definition, or information that is
not available to pattern functions. For this reason, you probably would want to define your own versions of these functions.

All of these functions take three parameters, the XYZ coordinates of the point to evaluate the pattern at.

`f_agate(x, y, z)`
`f_boxed(x, y, z)`
`f_bozo(x, y, z)`
`f_brick(x, y, z)`
`f_bumps(x, y, z)`
`f_checker(x, y, z)`
`f_crackle(x, y, z)`
 This pattern has many more options, this function uses the defaults.
`f_cylindrical(x, y, z)`
`f_dents(x, y, z)`
`f_gradientX(x, y, z)`
`f_gradientY(x, y, z)`
`f_gradientZ(x, y, z)`
`f_granite(x, y, z)`
`f_hexagon(x, y, z)`
`f_leopard(x, y, z)`
`f_mandel(x, y, z)`
 Only the basic mandel pattern is implemented, its variants
and the other fractal patterns are not implemented.
`f_marble(x, y, z)`
`f_onion(x, y, z)`
`f_planar(x, y, z)`
`f_radial(x, y, z)`
`f_ripples(x, y, z)`
`f_spherical(x, y, z)`
`f_spiral1(x, y, z)`
`f_spiral2(x, y, z)`
`f_spotted(x, y, z)`
`f_waves(x, y, z)`
`f_wood(x, y, z)`
`f_wrinkles(x, y, z)`

3.4.9.1.8 Glass.inc

This file contains glass materials using new features introduced in POV 3.1 and 3.5. The old glass.inc
file is still included for backwards compatibility (it is named glass_old.inc, and is included
by glass.inc, so you do not need to change any scenes), but these materials will give more
realistic results.

3.4.9.1.8.1 Glass colors (with transparency)

| Col_Glass_Beerbottle Col_Glass_Bluish Col_Glass_Clear Col_Glass_Dark_Green | Col_Glass_General Col_Glass_Green Col_Glass_Old Col_Glass_Orange | Col_Glass_Ruby Col_Glass_Vicksbottle Col_Glass_Winebottle Col_Glass_Yellow |

3.4.9.1.8.2 Glass colors (without transparency, for fade_color)

| Col_Amber_01 Col_Amber_02 Col_Amber_03 Col_Amber_04 Col_Amber_05 | Col_Amber_06 Col_Amber_07 Col_Amber_08 Col_Amber_09 Col_Amethyst_01 | Col_Amethyst_02 Col_Amethyst_03 Col_Amethyst_04 Col_Amethyst_05 Col_Amethyst_06 |
| Col_Apatite_01 Col_Apatite_02 Col_Apatite_03 Col_Apatite_04 Col_Apatite_05 | Col_Aquamarine_01 Col_Aquamarine_02 Col_Aquamarine_03 Col_Aquamarine_04 Col_Aquamarine_05 | Col_Aquamarine_06 Col_Azurite_01 Col_Azurite_02 Col_Azurite_03 Col_Azurite_04 |
| Col_Beerbottle Col_Blue_01 Col_Blue_02 Col_Blue_03 Col_Blue_04 | Col_Citrine_01 Col_Dark_Green Col_Emerald_01 Col_Emerald_02 Col_Emerald_03 | Col_Emerald_04 Col_Emerald_05 Col_Emerald_06 Col_Emerald_07 Col_Fluorite_01 |
| Col_Fluorite_02 Col_Fluorite_03 Col_Fluorite_04 Col_Fluorite_05 Col_Fluorite_06 | Col_Fluorite_07 Col_Fluorite_08 Col_Fluorite_09 Col_Green Col_Green_01 | Col_Green_02 Col_Green_03 Col_Green_04 Col_Gypsum_01 Col_Gypsum_02 |
| Col_Gypsum_03 Col_Gypsum_04 Col_Gypsum_05 Col_Gypsum_06 Col_Orange | Col_Red_01 Col_Red_02 Col_Red_03 Col_Red_04 Col_Ruby | Col_Ruby_01 Col_Ruby_02 Col_Ruby_03 Col_Ruby_04 Col_Ruby_05 |
| Col_Sapphire_01 Col_Sapphire_02 Col_Sapphire_03 Col_Topaz_01 Col_Topaz_02 | Col_Topaz_03 Col_Tourmaline_01 Col_Tourmaline_02 Col_Tourmaline_03 Col_Tourmaline_04 | Col_Tourmaline_05 Col_Tourmaline_06 Col_Vicksbottle Col_Winebottle Col_Yellow |
| Col_Yellow_01 Col_Yellow_02 Col_Yellow_03 Col_Yellow_04 | | |

3.4.9.1.8.3 Glass finishes
`F_Glass5, ..., F_Glass10`

3.4.9.1.8.4 Glass interiors

`I_Glass1, ..., I_Glass4`
`I_Glass_Fade_Sqr1` (identical to `I_Glass1`)
`I_Glass_Fade_Exp1` (identical to `I_Glass2`)
`I_Glass_Fade_Exp2` (identical to `I_Glass3`)
`I_Glass_Fade_Exp3` (identical to `I_Glass4`)
Glass interiors with various fade_power settings.
`I_Glass_Dispersion1, I_Glass_Dispersion2`
Glass interiors with dispersion. `I_Glass_Dispersion1` has
an approximately natural glass dispersion. `I_Glass_Dispersion2` is exaggerated.
`I_Glass_Caustics1, I_Glass_Caustics2`
Glass interiors with caustics.

3.4.9.1.8.5 Glass interior macros

`I_Glass_Exp(Distance)` and `I_Glass_Sqr(Distance)`: These macros return an interior with either exponential or fade_power 2 falloff, and a fade_distance of Distance.

3.4.9.1.9 Golds.inc

This file has its own versions of `F_MetalA` through `F_MetalB`. The gold textures themselves are `T_Gold_1A` through `T_Gold_5E`.

3.4.9.1.10 Logo.inc

The official POV-Ray logo designed by Chris Colefax, in two versions

`Povray_Logo`
 The POV-Ray logo object
`Povray_Logo_Prism`
 The POV-Ray logo as a prism
`Povray_Logo_Bevel`
The POV-Ray logo as a beveled prism

3.4.9.1.11 Makegrass.inc

makegrass.inc - grass and prairie building macros.

`MakeBlade()`
 creates an individual blade of grass as mesh.

`MakeGrassPatch()`
 creates a patch of grass (mesh)

optional with saving the mesh in a text file.

`MakePrairie()`
 creates a prairie of grass patches.

3.4.9.1.12 Math.inc

This file contains many general math functions and macros.

3.4.9.1.12.1 Float functions and macros

`even(N)`: A function to test whether N is even, returns 1 when true, 0 when false.

Parameters:

 `N` = Input value

`odd(N)`: A function to test whether N is odd, returns 1 when true, 0 when false.

Parameters:

 `N` = Input value

`Interpolate(GC, GS, GE, TS, TE, Method)`: Interpolation macro, interpolates between the float values `TS` and `TE`. The method of interpolation is cosine, linear or exponential. The position where to evaluate the interpolation is determined by the position of `GC` in the range `GS` - `GE`. See the example below.

Parameters:

 `GC` = global current, float value within the range GS - GE
 `GS` = global start
 `GE` = global end
 `TS` = target start
 `TE` = target end
 `Method` = interpolation method, float value:

 `Method` < 0 : exponential, using the value of Method as exponent.
 `Method` = 0 : cosine interpolation.
 `Method` > 0 : exponential, using the value of Method as exponent.

 `Method` = 1 : linear interpolation,

Example:

```

#declare A = Interpolate(0.5, 0, 1, 0, 10, 1);
#debug str(A,0,2)
// result A = 5.00

#declare A = Interpolate(0.0,-2, 2, 0, 10, 1);
#debug str(A,0,2)
// result A = 5.00

#declare A = Interpolate(0.5, 0, 1, 0, 10, 2);
#debug str(A,0,2)
// result A = 2.50
```

`Mean(A)`: A macro to compute the average of an array of values.

Parameters:

 `A` = An array of float or vector values.

`Std_Dev(A, M)`: A macro to compute the standard deviation.

Parameters:

 `A` = An array of float values.
 `M` = Mean of the floats in the array.

`GetStats(A)`: This macro declares a global array named `StatisticsArray` containing: N, Mean, Min, Max, and Standard Deviation

Parameters:

 `A` = An array of float values.

`Histogram(ValArr, Intervals)`: This macro declares a global, 2D array named `HistogramArray`. The first value in the array is the center of the interval/bin, the second the number of values in that interval.

Parameters:

 `ValArr` = An array with values.
 `Intervals` = The desired number of intervals/bins.

`sind(v), cosd(v), tand(v), asind(v), acosd(v), atand(v), atan2d(a, b)`: These functions are versions of the
trigonometric functions using degrees, instead of radians, as the angle unit.

Parameters:

 The same as for the analogous built-in trig function.

`max3(a, b, c)`: A function to find the largest of three numbers.

Parameters:

 `a, b, c` = Input values.

`min3(a, b, c)`: A function to find the smallest of three numbers.

Parameters:

 `a, b, c` = Input values.

`f_sqr(v)`: A function to square a number.

Parameters:

 `v` = Input value.

`sgn(v)`: A function to show the sign of the number. Returns -1 or 1 depending on the sign of v.

Parameters:

 `v` = Input value.

`clip(V, Min, Max)`: A function that limits a value to a specific range, if it goes outside that range it is clipped. Input values larger than `Max` will return `Max`, those less than `Min` will return `Min`.

Parameters:

 `V` = Input value.
 `Min` = Minimum of output range.
 `Max` = Maximum of output range.

`clamp(V, Min, Max)`: A function that limits a value to a specific range, if it goes outside that range it is clamped to this range, wrapping around. As the input increases or decreases outside the given range, the output will
repeatedly sweep through that range, making a sawtooth waveform.

Parameters:

 `V` = Input value.
 `Min` = Minimum of output range.
 `Max` = Maximum of output range.

`adj_range(V, Min, Max)`: A function that adjusts input values in the range [0, 1] to a given range. An input value of 0 will return `Min`, 1 will return `Max`, and values outside the [0, 1] range will be linearly extrapolated (the graph will continue in a straight line).

Parameters:

 `V` = Input value.
 `Min` = Minimum of output range.
 `Max` = Maximum of output range.

`adj_range2(V, InMin, InMax, OutMin, OutMax)`: Like `f_range()`, but adjusts input values in the range `[InMin, InMax]` to the range `[OutMin, OutMax]`.

Parameters:

 `V` = Input value.
 `InMin` = Minimum of input range.
 `InMax` = Maximum of input range.
 `OutMin` = Minimum of output range.
 `OutMax` = Maximum of output range.

3.4.9.1.12.2 Vector functions and macros

These are all macros in the current version because functions can not take vector parameters, but this may change in the future.

`VSqr(V)`: Square each individual component of a vector, equivalent to `V*V`.

Parameters:

 `V` = Vector to be squared.

`VPow(V, P), VPow5D(V, P)`: Raise each individual component of a vector to a given power.

Parameters:

 `V` = Input vector.
 `P` = Power.

`VEq(V1, V2)`: Tests for equal vectors, returns true if all three components of `V1`equal the respective components of `V2`.

Parameters:

 `V1, V2` = The vectors to be compared.

`VEq5D(V1, V2)`: A 5D version of `VEq()`. Tests for equal vectors, returns true if all 5 components of `V1`equal the respective components of `V2`.

Parameters:

 `V1, V2` = The vectors to be compared.

`VZero(V)`: Tests for a < 0, 0, 0> vector.

Parameters:

 `V` = Input vector.

`VZero5D(V)`: Tests for a < 0, 0, 0, 0, 0> vector.

Parameters:

 `V` = Input vector.

`VLength5D(V)`: Computes the length of a 5D vector.

Parameters:

 `V` = Input vector.

`VNormalize5D(V)`: Normalizes a 5D vector.

Parameters:

 `V` = Input vector.

`VDot5D(V1, V2)`: Computes the dot product of two 5D vectors. See vdot() for more information on dot products.

Parameters:

 `V` = Input vector.

`VCos_Angle(V1, V2)`: Compute the cosine of the angle between two vectors.

Parameters:

 `V1, V2` = Input vectors.

`VAngle(V1, V2), VAngleD(V1, V2)`: Compute the angle between two vectors. `VAngle()` returns the angle in radians, `VAngleD()` in degrees.

Parameters:

 `V1, V2` = Input vectors.

`VRotation(V1, V2, Axis)` and `VRotationD(V1, V2, Axis)`: Compute the rotation angle from V1 to V2 around Axis. Axis should be perpendicular to both V1 and V2. The output will be in the range between -pi and pi radians or between -180 degrees and 180 degrees if you are using the degree version. However, if Axis is set to <0,0,0> the output will always be positive or zero, the same result you will get with the VAngle() macros.

Parameters:

 `V1, V2` = Input vectors.

`VDist(V1, V2)`: Compute the distance between two points.

Parameters:

 `V1, V2` = Input vectors.

`VPerp_To_Vector(V)`: Find a vector perpendicular to the given vector.

Parameters:

 `V` = Input vector.

`VPerp_To_Plane(V1, V2)`: Find a vector perpendicular to both given vectors. In other words, perpendicular to the plane defined by the two input vectors.

Parameters:

 `V1, V2` = Input vectors.

`VPerp_Adjust(V1, Axis)`: Find a vector perpendicular to Axis and in the plane of V1 and Axis. In other words, the new vector is a version of V1 adjusted to be perpendicular to Axis.

Parameters:

 `V1, Axis` = Input vectors.

`VProject_Plane(V1, Axis)`: Project vector V1 onto the plane defined by Axis.

Parameters:

 `V1` = Input vectors.
 `Axis` = Normal of the plane.

`VProject_Axis(V1, Axis)`: Project vector V1 onto the axis defined by Axis.

Parameters:

 `V1, Axis` = Input vectors.

`VMin(V), VMax(V)`: Find the smallest or largest component of a vector.

Parameters:

 `V` = Input vector.

`VWith_Len(V, Len)`: Create a vector parallel to a given vector but with a given length.

Parameters:

 `V` = Direction vector.
 `Len` = Length of desired vector.

3.4.9.1.12.3 Vector Analysis

`SetGradientAccuracy(Value)`: All the macros below make use of a constant named __Gradient_Fn_Accuracy_ for numerical approximation of the derivatives. This constant can be changed with the macro, the default value is 0.001.

`fn_Gradient(Fn)`: A macro calculating the gradient of a function as a function.

Parameters:

`Fn` = function to calculate the gradient from.

Output: the length of the gradient as a function.

`fn_Gradient_Directional(Fn, Dir)`: A macro calculating the gradient of a function in one direction as a function.

Parameters:

`Fn` = function to calculate the gradient from.
`Dir` = direction to calculate the gradient.

Output: the gradient in that direction as a function.

`fn_Divergence(Fnx, Fny, Fnz)`: A macro calculating the divergence of a (vector) function as a function.

Parameters:

`Fnx, Fny, Fnz`= x, y and z components of a vector function.

Output: the divergence as a function.

`vGradient(Fn, p0)`: A macro calculating the gradient of a function as a vector expression.

Parameters:

`Fn` = function to calculate the gradient from.
`p0` = point where to calculate the gradient.

Output: the gradient as a vector expression.

`vCurl(Fnx, Fny, Fnz, p0)`: A macro calculating the curl of a (vector) function as a vector expression.

Parameters:

`Fnx, Fny, Fnz` = x, y and z components of a vector function.
`p0` = point where to calculate the gradient.

Output: the curl as a vector expression

`Divergence(Fnx, Fny, Fnz, p0)`: A macro calculating the divergence of a (vector) function as a float expression.

Parameters:

`Fnx, Fny, Fnz` = x, y and z components of a vector function.
`p0` = point where to calculate the gradient.

Output: the divergence as a float expression.

`Gradient_Length(Fn, p0)`: A macro calculating the length of the gradient of a function as a float expression.

Parameters:

`Fn` = function to calculate the gradient from.
`p0` = point where to calculate the gradient.

Output: the length of the gradient as a float expression.

`Gradient_Directional(Fn, p0, Dir)`: A macro calculating the gradient of a function in one direction as a float expression.

Parameters:

`Fn` = function to calculate the gradient from.
`p0` = point where to calculate the gradient.
`Dir` = direction to calculate the gradient.

Output: the gradient in that direction as a float expression

3.4.9.1.13 Meshmaker.inc

meshmaker.inc - various mesh2 objects by splines.

`MSM(SplineArray, SplRes, Interp_type, InterpRes, FileName)`
 Generates a mesh2 from an array of splines and optionally writes the mesh2 object as a file of the given `FileName`.

The uv_coordinates come from the square <0,0> - <1,1>.

The spline is evaluated from t=0 to t=1.

For the normal calculation, it is required that all splines (also linear_spline) have one extra
point before t=0 and after t=1.

`BuildWriteMesh2(VecArr, NormArr, UVArr, U, V, FileName)`
 Generates and optionally writes a mesh2 object based on 3 input arrays,
the number of quads in U and V direction and a filename.

`VecArr` : The array that contains the vertices of the triangles in the mesh.

`NormArr` : The array with the normal vectors that go with the vertices.

`UVArr` : The array containing the uv_vectors.

`U` : The amount of subdivisions of the surface in the u-direction.

`V` : The amount of subdivisions in the v-direction.

Based on the U and V values the face_indices of the triangles in the mesh are calculated.

`FileName` : The name of the file to which the mesh will be written.
If is an empty string (""), no file will be written.

If the file extension is 'obj' a Wavefront objectfile will be written.

If the extension is 'pcm' a compressed mesh file is written.

If a file name is given, the macro will first check if it already exists.

If that is so, it will try to parse the existing file unless it's a '*.obj', '*.pcm' or '*.arr' file as POV-Ray
can not read them directly. In this case a new mesh will be generated,
but the existing files will _not_ be over-written.

`BuildSpline(Arr, SplType)`
 A helper macro for MSM()

Generates from a array `Arr` a spline of the given spline type `SplType`.

`CheckFileName(FileName)`
 A helper macro for MSM()

If Return has a value of 0 the mesh will not be build,
but it will be parsed from file.

`LInterpolate(Val, Min, Max)`
 A helper macro for MSM()

Linear interpolation of a vector or float between `Min` and `Max`.

`Min` : minimal float value or vector.

`Max` : Maximal float value or vector.

`Val` : A float in the range 0 - 1.

`RangeMM() = function(Val,Rmin,Rmax,Min,Max)`
 A helper function for MSM()

Adjusts input values in the range [`RMin, RMax`] to fit in the range [Min, Max].
`Val`: A float value in the range [Rmin, Rmax].

`Parametric(__F1__, __F2__, __F3__, UVmin, UVmax, Iter_U, Iter_V, FileName)`
 Generates a mesh2 object from the parametric uv functions `__F1__, __F2__, __F3__`
in the ranges between `UVmin` and `UVmax` with the iteration steps

`Iter_U` and `Iter_V` and optionally saves the mesh2 object as a file with the name `FileName`.

`Paramcalc(UVmin, UVmax, Iter_U, Iter_V, FileName)`
 The kernel of the macro Parametric(). See `Parametric()`.

`Prism1(Spl, ResSpl, PSpl, PRes, FileName)`
 Generates a mesh2 object by extruding the spline `Spl` along the y-axis with the resolution spline `ResSpl`.
In every step the spline is scaled by the 'relative' distance from the
y-axis of the second spline (PSpl).

The uv_coordinates come from the square <0,0> - <1,1>.

`Spl` : The spline to be extruded.

The spline is evaluated from t=0 to t=1. For the normal calculation,

it is required that all splines (also linear_spline) have one extra

point before t=0 and after t=1.
`ResSpl` : The amount of triangles to be used along the spline.
`PSpl` : The spline that determines by what amount the extrusion

is scaled in each step. The scaling is based on the relative distance from the y-axis.

That is, at t=0 the scale is always 1, so that the start of the shape is

identical to the spline `Spl`.

PSpl also sets the height of the resulting shape (its y-value at t=1).

The spline is evaluated from t=0 to t=1. For the normal calculation,

it is required that all splines (also linear_spline) have one extra

point before t=0 and after t=1.

`FileName` : The name of the file to which the mesh will be written.

If is an empty string (""), no file will be written. If a file name is given, the macro

will first check if it already exists. If that is so, it will expect a

mesh2 with the name "Surface" and try to parse the existing file.

`Lathe(Spl, ResSpl, Rot, ResRot, FileName)`
 This macro generates a mesh2 object by rotating a two-dimensional curve about the y-axis.

The uv_coordinates come from the square <0,0> - <1,1>.

`Spl` : The spline to be rotated.
The spline is evaluated from t=0 to t=1. For the normal calculation,
it is required that all splines (also linear_spline) have one extra
point before t=0 and after t=1.
`ResSpl` : The amount of triangles to be used along the spline.
`Rot` : The angle the spline has to be rotated.

`ResRot` : The amount of triangles to be used in the circumference.

`FileName` : The name of the file to which the mesh will be written.

If is an empty string (""), no file will be written.

If the file extension is 'obj' a Wavefront objectfile will be written.

If the extension is 'pcm' a compressed mesh file is written.

If a file name is given, the macro will first check if it already exists.

If that is so, it will try to parse the existing file unless it's a '*.obj',

'*.pcm' or '*.arr' file as POV-Ray can not read them directly. In this case a new

mesh will be generated, but the existing files will _not_ be over-written.

`Coons(Spl1, Spl2, Spl3, Spl4, Iter_U, Iter_V, FileName)`
 Generates a mesh2 'coons surface' defined by four splines, all attached head to tail to the previous / next one.

The uv_coordinates come from the square <0,0> - <1,1>.

`Spl1 - 4` : The four spline that define the surface.

The splines are evaluated from t=0 to t=1.

`Iter_U` : The resolution for the splines 1 and 3.

`Iter_V` : The resolution for the splines 2 and 4.

`FileName` : The name of the file to which the mesh will be written.

If is an empty string (""), no file will be written.

If the file extension is 'obj' a Wavefront objectfile will be written.

If the extension is 'pcm' a compressed mesh file is written.

If a file name is given, the macro will first check if it already exists.

If that is so, it will try to parse the existing file unless it's a '*.obj',

'*.pcm' or '*.arr' file as POV-Ray can not read them directly. In this case a new

mesh will be generated, but the existing files will _not_ be over-written.

`TwoVarSurf(__Fuv, Urange, Vrange, Iter_U, Iter_V, FileName)`
 Generates a mesh2 object by extruding an uv-function.
`Urange` : The range in x direction.

`Vrange` : The range in y direction.

`Iter_U` : The resolution in x direction.

`Iter_V` : The resolution in y direction.

`FileName` : The name of the file to which the mesh will be written.

If is an empty string (""), no file will be written. If a file name is given, the macro

will first check if it already exists. If that is so, it will expect a

mesh2 with the name "Surface" and try to parse the existing file.

`SweepSpline1(Track,Shape,Waist,U,V,Filename)`
 Generates a mesh2 object by extruding a spline `Shape` along `Track`
and optionally writes it as a file with method 1.

`FileName` : The name of the file to which the mesh will be written.
If is an empty string (""), no file will be written. If a file name is given, the macro

will first check if it already exists. If that is so, it will expect a

mesh2 with the name "Surface" and try to parse the existing file.
`SweepSpline2(Track,Shape,Waist,U,V,Filename)`
 Generates a mesh2 object by extruding a spline `Shape` along `Track`
and optionally writes it as a file with method 2.

`FileName` : The name of the file to which the mesh will be written.
If is an empty string (""), no file will be written. If a file name is given, the macro

will first check if it already exists. If that is so, it will expect a

mesh2 with the name "Surface" and try to parse the existing file.

3.4.9.1.14 Metals.inc

These files define several metal textures. The file metals.inc contains copper, silver, chrome, and brass textures, and golds.inc contains the gold textures. Rendering the demo files will come in useful in using these textures.

Pigments:

 `P_Brass1`
 Dark brown bronze.

 `P_Brass2`
 Somewhat lighter brown than Brass4. Old penny, in soft finishes.

 `P_Brass3`
 Used by Steve Anger's Polished_Brass. Slightly coppery.

 `P_Brass4`
 A little yellower than Brass1.

 `P_Brass5`
 Very light bronze, ranges from med tan to almost white.

 `P_Copper1`
 Bronze-like. Best in finish #C.

 `P_Copper2`
 Slightly brownish copper/bronze. Best in finishes #B-#D.

 `P_Copper3`
 Reddish-brown copper. Best in finishes #C-#E.

 `P_Copper4`
 Pink copper, like new tubing. Best in finishes #C-#E.

 `P_Copper5`
 Bronze in softer finishes, gold in harder finishes.

 `P_Chrome1`
 20% Gray. Used in Steve Anger's Polished_Chrome.

 `P_Chrome2`
 Slightly blueish 60% gray. Good steel w/finish #A.

 `P_Chrome3`
 50% neutral gray.

 `P_Chrome4`
 75% neutral gray.

 `P_Chrome5`
 95% neutral gray.

 `P_Silver1`
 Yellowish silverplate. Somewhat tarnished looking.

 `P_Silver2`
 Not quite as yellowish as Silver1 but more so than Silver3.

 `P_Silver3`
 Reasonably neutral silver.

 `P_Silver4`

 `P_Silver5`

Finishes:

 `F_MetalA`
 Very soft and dull.

 `F_MetalB`
 Fairly soft and dull.

 `F_MetalC`
 Medium reflectivity. Holds color well.

 `F_MetalD`
 Very hard and highly polished. High reflectivity.

 `F_MetalE`
 Very highly polished and reflective.

Textures:

 `T_Brass_1A to T_Brass_5E`
 `T_Copper_1A to T_Copper_5E`
 `T_Chrome_1A to T_Chrome_5E`
 `T_Silver_1A to T_Silver_5E`

3.4.9.1.15 Rad_def.inc

This file defines a macro that sets some common radiosity settings. These settings are extremely general and are intended for ease of use, and do not necessarily give the best results.

Usage:

```

#include "rad_def.inc"
global_settings {
 ...
 radiosity {
 Rad_Settings(Setting, Normal, Media)
 }
 }
```

Parameters:

 Setting = Quality setting. Use one of the predefined constants:

 Radiosity_Default
 Radiosity_Debug
 Radiosity_Fast
 Radiosity_Normal
 Radiosity_2Bounce
 Radiosity_Final
 Radiosity_OutdoorLQ
 Radiosity_OutdoorHQ
 Radiosity_OutdoorLight
 Radiosity_IndoorLQ
 Radiosity_IndoorHQ

 Normal = Boolean value, whether or not to use surface normal modifiers for radiosity samples.
 Media = Boolean value, whether or not to calculate media for radiosity samples.

3.4.9.1.16 Rand.inc

A collection of macros for generating random numbers, as well as 4 predefined random number streams: `RdmA, RdmB, RdmC,` and `RdmD`. There are macros for creating random numbers in a flat distribution (all numbers equally likely) in various ranges, and a variety of other distributions.

3.4.9.1.16.1 Flat Distributions

`SRand(Stream)`: Signed rand(), returns random numbers in the range [-1, 1].

Parameters:

 `Stream` = Random number stream.

`RRand(Min, Max, Stream)`: Returns random numbers in the range [Min, Max].

Parameters:

 `Min` = The lower end of the output range.
 `Max` = The upper end of the output range.
 `Stream` = Random number stream.

`VRand(Stream)`: Returns random vectors in a box from < 0, 0, 0> to < 1, 1, 1>

Parameters:

 `Stream` = Random number stream.

`VRand_In_Box(PtA, PtB, Stream)`: Like VRand(), this macro returns a random vector in a box, but this version lets you specify the two corners of the box.

Parameters:

 `PtA` = Lower-left-bottom corner of box.
 `PtB` = Upper-right-top corner of box.
 `Stream` = Random number stream.

`VRand_In_Sphere(Stream)`: Returns a random vector in a unit-radius sphere located at the origin.

Parameters:

 `Stream` = Random number stream.

`VRand_On_Sphere(Stream)`: Returns a random vector on the surface of a unit-radius sphere located at the origin.

Parameters:

 `Stream` = Random number stream.

`VRand_In_Obj(Object, Stream)`: This macro takes a solid object and returns a random point that is inside it. It does this by randomly sampling the bounding box of the object, and can be quite slow if the object occupies a small percentage of the volume of its bounding box (because it will take more attempts to find a point inside the object). This macro is best used on finite, solid objects (non-solid objects, such as meshes and bezier patches, do not have a defined inside, and will not work).

Parameters:

 `Object` = The object the macro chooses the points from.
 `Stream` = Random number stream.

3.4.9.1.16.2 Other Distributions

3.4.9.1.16.3 Continuous Symmetric Distributions

`Rand_Cauchy(Mu, Sigma, Stream)`: Cauchy distribution.

Parameters:

 `Mu` = Mean.
 `Sigma` = Standard deviation.
 `Stream` = Random number stream.

`Rand_Student(N, Stream)`: Student's distribution.

Parameters:

 `N` = degrees of freedom.
 `Stream` = Random number stream.

`Rand_Normal(Mu, Sigma, Stream)`: Normal distribution.

Parameters:

 `Mu` = Mean.
 `Sigma` = Standard deviation.
 `Stream` = Random number stream.

`Rand_Gauss(Mu, Sigma, Stream)`: Gaussian distribution. Like Rand_Normal(), but a bit faster.

Parameters:

 `Mu` = Mean.
 `Sigma` = Standard deviation.
 `Stream` = Random number stream.

3.4.9.1.16.4 Continuous Skewed Distributions

`Rand_Spline(Spline, Stream)`: This macro takes a spline describing the desired distribution. The T value of the spline is the output value, and the .y value its chance of occuring.

Parameters:

 `Spline` = A spline determining the distribution.
 `Stream` = Random number stream.

`Rand_Gamma(Alpha, Beta, Stream)`: Gamma distribution.

Parameters:

 `Alpha` = Shape parameter > 0.
 `Beta` = Scale parameter > 0.
 `Stream` = Random number stream.

`Rand_Beta(Alpha, Beta, Stream)`: Beta variate.

Parameters:

 `Alpha` = Shape Gamma1.
 `Beta` = Scale Gamma2.
 `Stream` = Random number stream.

`Rand_Chi_Square(N, Stream)`: Chi Square random variate.

Parameters:

 `N` = Degrees of freedom (integer).
 `Stream` = Random number stream.

`Rand_F_Dist(N, M, Stream)`: F-distribution.

Parameters:

 `N, M` = Degrees of freedom.
 `Stream` = Random number stream.

`Rand_Tri(Min, Max, Mode, Stream)`: Triangular distribution

Parameters:

 `Min, Max, Mode`: Min < Mode < Max.
 `Stream` = Random number stream.

`Rand_Erlang(Mu, K, Stream)`: Erlang variate.

Parameters:

 `Mu` = Mean >= 0.
 `K` = Number of exponential samples.
 `Stream` = Random number stream.

`Rand_Exp(Lambda, Stream)`: Exponential distribution.

Parameters:

 `Lambda` = rate = 1/mean.
 `Stream` = Random number stream.

`Rand_Lognormal(Mu, Sigma, Stream)`: Lognormal distribution.

Parameters:

 `Mu` = Mean.
 `Sigma` = Standard deviation.
 `Stream` = Random number stream.

`Rand_Pareto(Alpha, Stream)`: Pareto distribution.

Parameters:

 `Alpha` = ?
 `Stream` = Random number stream.

`Rand_Weibull(Alpha, Beta, Stream)`: Weibull distribution.

Parameters:

 `Alpha` = ?
 `Beta` = ?
 `Stream` = Random number stream.

3.4.9.1.16.5 Discrete Distributions

`Rand_Bernoulli(P, Stream)` and `Prob(P, Stream)`: Bernoulli distribution. Output is true with probability equal to the value of P and false with a probability of 1 - P.

Parameters:

 `P` = probability range (0-1).
 `Stream` = Random number stream.

`Rand_Binomial(N, P, Stream)`: Binomial distribution.

Parameters:

 `N` = Number of trials.
 `P` = Probability (0-1)
 `Stream` = Random number stream.

`Rand_Geo(P, Stream)`: Geometric distribution.

Parameters:

 `P` = Probability (0-1).
 `Stream` = Random number stream.

`Rand_Poisson(Mu, Stream)`: Poisson distribution.

Parameters:

 `Mu` = Mean.
 `Stream` = Random number stream.

3.4.9.1.17 Screen.inc

Screen.inc will enable you to place objects and textures right in front of the camera. When you move the camera, the objects placed with screen.inc will follow the movement and stay in the same position on the screen. One use of this is to place your signature or a logo in the corner of the image.

You can only use screen.inc with the perspective camera. Screen.inc will automatically create a default camera definition for you when it is included. All aspects of the camera can than be changed, by invoking the appropriate 'Set_Camera_...' macros in your scene. After calling these setup macros you can use the macros Screen_Object and Screen_Plane.

Note: Even though objects aligned using screen.inc follow the camera, they are still part of the scene. That means that they will be affected by perspective, lighting, the surroundings etc.

For an example of use, see the screen.pov demo file.

`Set_Camera_Location(Loc)`: Changes the position of the default camera to a new location as specified by the `Loc` vector.

`Set_Camera_Look_At(LookAt)`: Changes the position the default camera looks at to a new location as specified by the `LookAt` vector.

`Set_Camera_Aspect_Ratio(Aspect)`: Changes the default aspect ratio, `Aspect` is a float value, usually width divided by the height of the image.

`Set_Camera_Aspect(Width,Height)`: Changes the default aspect ratio of the camera.

`Set_Camera_Sky(Sky)`: Sets a new Sky-vector for the camera.

`Set_Camera_Zoom(Zoom)`: The amount to zoom in or out, `Zoom` is a float.

`Set_Camera_Angle(Angle)`: Sets a new camera angle.

`Set_Camera(Location, LookAt, Angle)`: Set `location`, `look_at` and `angle` in one go.

`Reset_Camera()`: Resets the camera to its default values.

`Screen_Object (Object, Position, Spacing, Confine, Scaling)`: Puts an object in front of the camera.

Parameters:

 `Object` = The object to place in front of the screen.
 `Position` = UV coordinates for the object. <0,0> is lower left corner of the screen and <1,1> is upper right corner.
 `Spacing` = Float describing minimum distance from object to the borders. UV vector can be used to get different horizontal and vertical spacing.
 `Confine` = Set to true to confine objects to visible screen area. Set to false to allow objects to be outside visible screen area.
 `Scaling` = If the object intersects or interacts with the scene, try to move it closer to the camera by decreasing Scaling.

`Screen_Plane (Texture, Scaling, BLCorner, TRCorner)`: Screen_Plane is a macro that will place a texture of your choice on a plane right in front of the camera.

Parameters:

 `Texture` = The texture to be displayed on the camera plane. <0,0,0> is lower left corner and <1,1,0> is upper right corner.
 `Scaling` = If the plane intersects or interacts with the scene, try to move it closer to the camera by decreasing Scaling.
 `BLCorner` = The bottom left corner of the Screen_Plane.
 `TRCorner` = The top right corner of the Screen_Plane.

3.4.9.1.18 Shapes.inc

These files contain predefined shapes and shape-generation macros.

shapes.inc includes shapes_old.inc and contains many macros for working
with objects, and for creating special objects, such as bevelled text, spherical height fields,
and rounded shapes.

Many of the objects in shapes_old.inc are not very useful in the newer versions of
POV-Ray, and are kept for backwards compatability with old scenes written for versions of POV-Ray
that lacked primitives like cones, disks, planes, etc.

The file shapes2.inc contains some more useful shapes, including regular polyhedrons,
and shapesq.inc contains several quartic and cubic shape definitions.

Some of the shapes in shapesq.inc would be much easier to generate, more flexible,
and possibly faster rendering as isosurfaces, but are still useful for two reasons: backwards
compatability, and the fact that isosurfaces are always finite.

`Isect(Pt, Dir, Obj, OPt)` and `IsectN(Pt, Dir, Obj, OPt, ONorm)`: These macros are interfaces to the trace() function. Isect() only returns the intersection point, IsectN() returns the surface normal as well. These macros return the point and normal information through their parameters, and true or false depending on whether an intersection was found: If an intersection is found, they return true and set OPt to the intersection point, and ONorm to the normal. Otherwise they return false, and do not modify OPt or ONorm.

Parameters:

 `Pt` = The origin (starting point) of the ray.
 `Dir` = The direction of the ray.
 `Obj` = The object to test for intersection with.
 `OPt` = A declared variable, the macro will set this to the intersection point.
 `ONorm` = A declared variable, the macro will set this to the surface normal at the intersection point.

`Extents(Obj, Min, Max)`: This macro is a shortcut for calling both min_extent() and max_extent() to get the corners of the bounding box of an object. It returns these values through the Min and Max parameters.

Parameters:

 `Obj` = The object you are getting the extents of.
 `Min` = A declared variable, the macro will set this to the min_extent of the object.
 `Max` = A declared variable, the macro will set this to the max_extent of the object.

`Center_Object(Object, Axis)`: A shortcut for using the Center_Trans() macro with an object.

Parameters:

 `Object` = The object to be centered.
 `Axis` = See Center_Trans() in the transforms.inc documentation.

`Align_Object(Object, Axis, Pt)`: A shortcut for using the Align_Trans() macro with an object.

Parameters:

 `Object` = The object to be aligned.
 `Axis` = See Align_Trans() in the transforms.inc documentation.
 `Point` = The point to which to align the bounding box of the object.

`Bevelled_Text(Font, String, Cuts, BevelAng, BevelDepth, Depth, Offset, UseMerge)`: This macro attempts to bevel the front edges of a text object. It accomplishes this by making an intersection of multiple copies of the text object, each sheared in a different direction. The results are no perfect, but may be entirely acceptable for some purposes. Warning: the object generated may render considerably more slowly than an ordinary text object.

Parameters:

 `Font` = A string specifying the font to use.
 `String` = The text string the object is generated from.
 `Cuts` = The number of intersections to use in bevelling the text.
 More cuts give smoother results, but take more memory and are slower rendering.
 `BevelAng` = The angle of the bevelled edge.
 `BevelDepth` = The thickness of the bevelled portion.
 `Depth` = The total thickness of the resulting text object.
 `Offset` = The offset parameter for the text object. The z value
of this vector will be ignored, because the front faces of all the letters need
to be coplanar for the bevelling to work.
 `UseMerge` = Switch between merge (1) and union (0).

`Text_Space(Font, String, Size, Spacing)`: Computes the width of a text string, including white space, it returns the advance widths of all n letters. Text_Space gives the space a text, or a glyph, occupies in regard to its surroundings.

Parameters:

 `Font` = A string specifying the font to use.
 `String` = The text string the object is generated from.
 `Size` = A scaling value.
 `Spacing` = The amount of space to add between the characters.

`Text_Width(Font, String, Size, Spacing)`: Computes the width of a text string, it returns the advance widths of the first n-1 letters, plus the glyph width of the last letter. Text_Width gives the physical width of the text and if you use only one letter the physical width of one glyph.

Parameters:

 `Font` = A string specifying the font to use.
 `String` = The text string the object is generated from.
 `Size` = A scaling value.
 `Spacing` = The amount of space to add between the characters.

`Align_Left, Align_Right, Align_Center`: These constants are used by the
`Circle_Text()` macro.

`Circle_Text(Font, String, Size, Spacing, Depth, Radius, Inverted, Justification, Angle)`: Creates a text object with the bottom (or top) of the character cells aligned with all or part of a circle. This macro should be used inside an `object{...}` block.

Parameters:

 `Font` = A string specifying the font to use.
 `String` = The text string the object is generated from.
 `Size` = A scaling value.
 `Spacing` = The amount of space to add between the characters.
 `Depth` = The thickness of the text object.
 `Radius` = The radius of the circle the letters are aligned to.
 `Inverted` = Controls what part of the text faces outside.
 If this parameter is nonzero, the tops of the letters will point toward the center
 of the circle. Otherwise, the bottoms of the letters will do so.
 `Justification` = Align_Left, Align_Right, or Align_Center.
 `Angle` = The point on the circle from which rendering will begin. The +x
 direction is 0 and the +y direction is 90 (i.e. the angle increases
 anti-clockwise).

`Wedge(Angle)`: This macro creates an infinite wedge shape, an intersection of two planes. It is mainly useful in CSG, for example to obtain a specific arc of a torus. The edge of the wedge is positioned along the y axis, and one side is fixed to the zy plane, the other side rotates clockwise around the y axis.

Parameters:

 `Angle` = The angle, in degrees, between the sides of the wedge shape.

`Spheroid(Center, Radius)`: This macro creates an unevenly scaled sphere. Radius is a vector where each component is the radius along that axis.

Parameters:

 `Center` = Center of the spheroid.
 `Radius` = A vector specifying the radii of the spheroid.

`Supertorus(MajorRadius, MinorRadius, MajorControl, MinorControl, Accuracy, MaxGradient)`: This macro creates an isosurface of the torus equivalent of a superellipsoid. If you specify a MaxGradient of less than 1, evaluate will be used. You will have to adjust MaxGradient to fit the parameters you choose, a squarer supertorus will have a higher gradient. You may want to use the function alone in your own isosurface.

Parameters:

 `MajorRadius, MinorRadius` = Base radii for the torus.
 `MajorControl, MinorControl` = Controls for the roundness of the supertorus. Use numbers in the range [0, 1].
 `Accuracy` = The accuracy parameter.
 `MaxGradient` = The max_gradient parameter.

`Supercone(EndA, A, B, EndB, C, D)`: This macro creates an object similar to a cone, but where the end points are ellipses. The actual object is an intersection of a quartic with a cylinder.

Parameters:

 `EndA` = Center of end A.
 `A, B` = Controls for the radii of end A.
 `EndB` = Center of end B.
 `C, D` = Controls for the radii of end B.

`Connect_Spheres(PtA, RadiusA, PtB, RadiusB)`: This macro creates a cone that will smoothly join two spheres. It creates only the cone object, however, you will have to supply the spheres yourself or use the Round_Cone2() macro instead.

Parameters:

 `PtA` = Center of sphere A.
 `RadiusA` = Radius of sphere A.
 `PtB` = Center of sphere B.
 `RadiusB` = Radius of sphere B.

`Wire_Box_Union(PtA, PtB, Radius)`,

`Wire_Box_Merge(PtA, PtB, Radius)`,

`Wire_Box(PtA, PtB, Radius, UseMerge)`: Creates a wire-frame box from cylinders and spheres. The resulting object will fit entirely within a box object with the same corner points.

Parameters:

 `PtA` = Lower-left-front corner of box.
 `PtB` = Upper-right-back corner of box.
 `Radius` = The radius of the cylinders and spheres composing the object.
 `UseMerge` = Whether or not to use a merge.

`Round_Box_Union(PtA, PtB, EdgeRadius)`,

`Round_Box_Merge(PtA, PtB, EdgeRadius)`,

`Round_Box(PtA, PtB, EdgeRadius, UseMerge)`: Creates a box with rounded edges from boxes, cylinders and spheres. The resulting object will fit entirely within a box object with the same corner points. The result is slightly different from a superellipsoid, which has no truely flat areas.

Parameters:

 `PtA` = Lower-left-front corner of box.
 `PtB` = Upper-right-back corner of box.
 `EdgeRadius` = The radius of the edges of the box.
 `UseMerge` = Whether or not to use a merge.

`Round_Cylinder_Union(PtA, PtB, Radius, EdgeRadius)`,

`Round_Cylinder_Merge(PtA, PtB, Radius, EdgeRadius)`,

`Round_Cylinder(PtA, PtB, Radius, EdgeRadius, UseMerge)`: Creates a cylinder with rounded edges from cylinders and tori. The resulting object will fit entirely within a cylinder object with the same end points and radius. The result is slightly different from a superellipsoid, which has no truely flat areas.

Parameters:

 `PtA, PtB` = The end points of the cylinder.
 `Radius` = The radius of the cylinder.
 `EdgeRadius` = The radius of the edges of the cylinder.
 `UseMerge` = Whether or not to use a merge.

`Round_Cone_Union(PtA, RadiusA, PtB, RadiusB, EdgeRadius)`,

`Round_Cone_Merge(PtA, RadiusA, PtB, RadiusB, EdgeRadius)`,

`Round_Cone(PtA, RadiusA, PtB, RadiusB, EdgeRadius, UseMerge)`: Creates a cone with rounded edges from cones and tori. The resulting object will fit entirely within a cone object with the same end points and radii.

Parameters:

 `PtA, PtB` = The end points of the cone.
 `RadiusA, RadiusB` = The radii of the cone.
 `EdgeRadius` = The radius of the edges of the cone.
 `UseMerge` = Whether or not to use a merge.

`Round_Cone2_Union(PtA, RadiusA, PtB, RadiusB)`,

`Round_Cone2_Merge(PtA, RadiusA, PtB, RadiusB)`,

`Round_Cone2(PtA, RadiusA, PtB, RadiusB, UseMerge)`: Creates a cone with rounded edges from a cone and two spheres. The resulting object will not fit entirely within a cone object with the same end points and radii because of the spherical caps. The end points are not used for the conical portion, but for the spheres, a suitable cone is then generated to smoothly join them.

Parameters:

 `PtA, PtB` = The centers of the sphere caps.
 `RadiusA, RadiusB` = The radii of the sphere caps.
 `UseMerge` = Whether or not to use a merge.

`Round_Cone3_Union(PtA, RadiusA, PtB, RadiusB)`,

`Round_Cone3_Merge(PtA, RadiusA, PtB, RadiusB)`,

`Round_Cone3(PtA, RadiusA, PtB, RadiusB, UseMerge)`: Like Round_Cone2(), this creates a cone with rounded edges from a cone and two spheres, and the resulting object will not fit entirely within a cone object with the same end points and radii because of the spherical caps. The difference is that this macro takes the end points of the conical portion and moves the spheres to be flush with the surface, instead of putting the spheres at the end points and generating a cone to join them.

Parameters:

 `PtA, PtB` = The end points of the cone.
 `RadiusA, RadiusB` = The radii of the cone.
 `UseMerge` = Whether or not to use a merge.

`Quad(A, B, C, D)` and `Smooth_Quad(A, NA, B, NB, C, NC, D, ND)`: These macros create quads, 4-sided polygonal objects, using triangle pairs.

Parameters:

 `A, B, C, D` = Vertices of the quad.
 `NA, NB, NC, ND` = Vertex normals of the quad.

3.4.9.1.18.1 The HF Macros

There are several HF macros in shapes.inc, which generate meshes in various shapes. All the HF macros have these things in common:

 The HF macros do not directly use an image for input, but evaluate a user-defined function. The macros deform the surface based on the function values.
 The macros can either write to a file to be included later, or create an object directly. If you want to output to a file, simply specify a filename. If you want to create an object directly, specify "" as the file name (an empty string).
 The function values used for the heights will be taken from the square that goes from <0,0,0> to <1,1,0> if UV height mapping is on. Otherwise the function values will be taken from the points where the surface is (before the deformation).
 The texture you apply to the shape will be evaluated in the square that goes from <0,0,0> to <1,1,0> if UV texture mapping is on. Otherwise the texture is evaluated at the points where the surface is (after the deformation.

The usage of the different HF macros is described below.

`HF_Square (Function, UseUVheight, UseUVtexture, Res, Smooth, FileName, MnExt, MxExt)`: This macro generates a mesh in the form of a square height field, similar to the built-in height_field primitive. Also see the general description of the HF macros above.

Parameters:

 `Function` = The function to use for deforming the height field.
 `UseUVheight` = A boolean value telling the macro whether or not to use UV height mapping.
 `UseUVtexture` = A boolean value telling the macro whether or not to use UV texture mapping.
 `Res` = A 2D vector specifying the resolution of the generated mesh.
 `Smooth` = A boolean value telling the macro whether or not to smooth the generated mesh.
 `FileName` = The name of the output file.
 `MnExt` = Lower-left-front corner of a box containing the height field.
 `MxExt` = Upper-right-back corner of a box containing the height field.

`HF_Sphere(Function, UseUVheight, UseUVtexture, Res, Smooth, FileName, Center, Radius, Depth)`: This macro generates a mesh in the form of a spherical height field. When UV-mapping is used, the UV square will be wrapped around the sphere starting at +x and going anti-clockwise around the y axis. Also see the general description of the HF macros above.

Parameters:

 `Function` = The function to use for deforming the height field.
 `UseUVheight` = A boolean value telling the macro whether or not to use UV height mapping.
 `UseUVtexture` = A boolean value telling the macro whether or not to use UV texture mapping.
 `Res` = A 2D vector specifying the resolution of the generated mesh.
 `Smooth` = A boolean value telling the macro whether or not to smooth the generated mesh.
 `FileName` = The name of the output file.
 `Center` = The center of the height field before being displaced, the displacement can, and most likely will, make the object off-center.
 `Radius` = The starting radius of the sphere, before being displaced.
 `Depth` = The depth of the height field.

`HF_Cylinder(Function, UseUVheight, UseUVtexture, Res, Smooth, FileName, EndA, EndB, Radius,Depth)`: This macro generates a mesh in the form of an open-ended cylindrical height field. When UV-mapping is used, the UV square will be wrapped around the cylinder. Also see the general description of the HF macros above.

Parameters:

 `Function` = The function to use for deforming the height field.
 `UseUVheight` = A boolean value telling the macro whether or not to use UV height mapping.
 `UseUVtexture` = A boolean value telling the macro whether or not to use UV texture mapping.
 `Res` = A 2D vector specifying the resolution of the generated mesh.
 `Smooth` = A boolean value telling the macro whether or not to smooth the generated mesh.
 `FileName` = The name of the output file.
 `EndA, EndB` = The end points of the cylinder.
 `Radius` = The (pre-displacement) radius of the cylinder.
 `Depth` = The depth of the height field.

`HF_Torus (Function, UseUVheight, UseUVtexture, Res, Smooth, FileName, Major, Minor, Depth)`: This macro generates a mesh in the form of a torus-shaped height field. When UV-mapping is used, the UV square is wrapped around similar to spherical or cylindrical mapping. However the top and bottom edges of the map wrap over and under the torus where they meet each other on the inner rim. Also see the general description of the HF macros above.

Parameters:

 `Function` = The function to use for deforming the height field.
 `UseUVheight` = A boolean value telling the macro whether or not to use UV height mapping.
 `UseUVtexture` = A boolean value telling the macro whether or not to use UV texture mapping.
 `Res` = A 2D vector specifying the resolution of the generated mesh.
 `Smooth` = A boolean value telling the macro whether or not to smooth the generated mesh.
 `FileName` = The name of the output file.
 `Major` = The major radius of the torus.
 `Minor` = The minor radius of the torus.

3.4.9.1.19 Shapes2.inc

`Tetrahedron`
 4-sided regular polyhedron.

`Octahedron`
 8-sided regular polyhedron.

`Dodecahedron`
 12-sided regular polyhedron.

`Icosahedron`
 20-sided regular polyhedron.

`Rhomboid`
 Three dimensional 4-sided diamond, basically a sheared box.

`Hexagon`
 6-sided regular polygonal solid, axis along x.

`HalfCone_Y`
 Convenient finite cone primitive, pointing up in the Y axis.

`Pyramid`
 4-sided pyramid (union of triangles, can not be used in CSG).

`Pyramid2`
 4-sided pyramid (intersection of planes, can be used in CSG).

`Square_X, Square_Y, Square_Z`
 Finite planes stretching 1 unit along each axis. In other words, 2X2 unit squares.

3.4.9.1.20 Shapes3.inc

This file contains macros for segments of shapes, facetted shapes and others.

Segments of shapes:

`Segment_of_Torus ( R_major, R_minor, Segment_Angle )`
 Segment of a torus around the y axis. The angle starts at positive x axis.

`Segment_of_CylinderRing ( R_out, R_in, Height, Segment_Angle )`
 Segment of a cylindrical ring around the y axis. The angle starts at positive x axis.

`Segment_of_Object ( Segment_Object, Segment_Angle )`
 Segment of an object around the y axis. The angle starts at positive x axis.

Based on min_extend and max_extend.

Angular shapes:

`Column_N (N, R_in, Height )`
 A regular n-sided column around the y axis, defined by the incircle radius `R_in`. `Height` is the height in y direction.

`Column_N_AB (N, A, B, R_in)`
 A regular n-sided column from point `A` to point `B`, defined by the incircle radius `R_in`.

`Pyramid_N (N, R_in_1, R_in_2, Height )`
 A regular n-sided pyramid around the y axis, defined by the incircle radii:

`R_in_1` at y = 0 and `R_in_2` at y = `Height`.

`Pyramid_N_AB(N, A, R_in_A, B, R_in_B)`
 A regular n-sided column from point `A` to point `B`,
defined by the incircle radii:

`R_in_A` at point `A` and `R_in_B` at point `B`.

Facetted shapes:

`Facetted_Sphere (Quarter_Segments, Radial_Segments)`
 A facetted sphere with incircle radius 1.

`Quarter_Segments` = number of equitorial facetts in one quarter (1/2 of the total number).

`Radial_Segments` = number of radial facetts.

`Facetted_Egg_Shape (Quarter_Segments, Radial_Segments, Lower_Scale, Upper_Scale)`
 A facetted egg shape. The number of facetts are defined analog to `Facetted_Egg_Shape()`.

Equitorial incircle radius = 1. Lower half scaled in y by `Lower_Scale`,
Upper half scaled in y by `Upper_Scale`.

`Facetted_Egg (N_Quarter_Segments, N_Radial_Segments)`
 A facetted egg with total height = 2. Lower half scaled in y by 1.15, Upper half scaled in y by 1.55.

Round shapes:

`Egg_Shape (Lower_Scale, Upper_Scale)`
 An egg shape with equitorial radius 1.

Lower half scaled in y by `Lower_Scale`,
Upper half scaled in y by `Upper_Scale`.

`Egg`
 Uses the macro Egg_Shape.

Lower half scaled in y by 1.15, upper half scaled in y by 1.55.

Wireframe shape (mostly also optionally filled:

`Ring_Sphere (Rmaj_H, Rmaj_V, Rmin_H, Rmin_V, Number_of_Rings_horizontal, Number_of_Rings_vertical)`
 A wireframe sphere by vertical and horizontal torii.

Horizontal tori: equatorial radius major `Rmaj_H`, radius minor `Rmin_H`.

Vertical tori: radius major `Rmaj_V`, radius minor `Rmin_V`.

`Round_Pyramid_N_out (N, A, CornerR_out_A, B, CornerR_out_B, R_Border, Filled, Merge )`
 A regular n-sided column from point `A` to point `B`,
defined by the outcircle radii:
`R_in_A` at point `A` and `R_in_B` at point `B`.

`Round_Pyramid_N_in (N, A, FaceR_in_A, B, FaceR_in_B, R_Border, Filled, Merge_On )`
 A regular n-sided column from point `A` to point `B`,
defined by the incircle radii:
`R_in_A` at point `A` and `R_in_B` at point `B`..

`Round_Cylinder_Tube( A, B, R_out, R_border, Filled, Merge)`
 A cylindrical tube from point `A` to point `B`,
with the outer radius `R_out` and the border radius `R_border`.
The inner radius is `R_out - R_border`.

With `Filled` = 1 we get a `Round_Cylinder`.

`Rounded_Tube( R_out, R_in, R_Border, Height, Merge)`
 A cylindrical tube around the y axis with the `Height` in y, with the outer radius `R_out`, the inner radius `R_in` and the radius of the rounded borders `R_border`.

`Rounded_Tube_AB( A, B, R_out, R_in, R_Border, Merge)`
 A cylindrical tube from point `A` to point `B`.

The outer radius is `R_out`, the inner radius is `R_in`
and the radius of the rounded borders is `R_border`.

`Round_Conic_Torus( Center_Distance, R_upper, R_lower, R_border, Merge)`
 A toroid ring the z axis,
with the lower torus part at y = 0 and the upper part at y = Center_Distance.

The radius of the lower part is `R_lower`,
the radius of the lower part is `R_lower`
The minor radius of the toroid is `R_border`.

`Round_Conic_Prism( Center_Distance, R_upper, R_lower, Length_Zminus, R_Border, Merge)`
 A shape of the toroidal form like `Round_Conic_Torus()`,
but filled and in the negativ z direction with the length `Length_Zminus`.

`Half_Hollowed_Rounded_Cylinder1( Length, R_out, R_border, BorderScale, Merge)`
 A hollowed half rounded cylinder with the `Length` in x, of the outer radius `R_out`, with round ends.
The borders have a minor radius `R_border` with the scale in y `BorderScale`.

The inner radius is `R_out - R_border`.
`Half_Hollowed_Rounded_Cylinder2( Length, R_out, R_corner, R_border, BorderScale, Merge)`
 A hollowed half rounded cylinder with the `Length` in x, of the outer radius `R_out`, with flat ends.
The corners have a minor radius of `R_corner`,
the borders have a minor radius of `R_border` with the scale in y `BorderScale`.

The inner radius is `R_out - R_border`, the inner lenght is `Length - 2*R_border`.

`Round_N_Tube_Polygon (N, Tube_R, R_incircle, Edge_R, Filled, Merge)`
 A regular polygon with `N` edges (or corners) with incircle radius `R_incircle`,
formed by a tube with the minor radius `Tube_R`.
The corners are formed by torus segments with the major radius `Edge_R`.

3.4.9.1.21 Shapesq.inc

`Bicorn`
This curve looks like the top part of a paraboloid, bounded from below by another paraboloid.
The basic equation is:
y^2 - (x^2 + z^2) y^2 - (x^2 + z^2 + 2 y - 1)^2 = 0

`Crossed_Trough`
This is a surface with four pieces that sweep up from the x-z plane.
The equation is: y = x^2 z^2

`Cubic_Cylinder`
A drop coming out of water? This is a curve formed by using the equation:
y = 1/2 x^2 (x + 1)
as the radius of a cylinder having the x-axis as its central axis. The final form of the equation is:
y^2 + z^2 = 0.5 (x^3 + x^2)

`Cubic_Saddle_1`
A cubic saddle. The equation is: z = x^3 - y^3

`Devils_Curve`
Variant of a devil's curve in 3-space. This figure has a top and bottom part that are very similar to a hyperboloid of one sheet, however the central region is pinched in the middle leaving two teardrop shaped holes.
The equation is:
x^4 + 2 x^2 z^2 - 0.36 x^2 - y^4 + 0.25 y^2 + z^4 = 0

`Folium`
This is a folium rotated about the x-axis. The formula is:
2 x^2 - 3 x y^2 - 3 x z^2 + y^2 + z^2 = 0

`Glob_5`
Glob - sort of like basic teardrop shape. The equation is:
y^2 + z^2 = 0.5 x^5 + 0.5 x^4

`Twin_Glob`
Variant of a lemniscate - the two lobes are much more teardrop-like.

`Helix, Helix_1`
Approximation to the helix z = arctan(y/x).
The helix can be approximated with an algebraic equation (kept to the range of a quartic) with the following steps:
tan(z) = y/x => sin(z)/cos(z) = y/x =>

(1) x sin(z) - y cos(z) = 0
Using the taylor expansions for sin, cos about z = 0,

sin(z) = z - z^3/3! + z^5/5! - ...

cos(z) = 1 - z^2/2! + z^6/6! - ...

Throwing out the high order terms, the expression (1) can be written as:

x (z - z^3/6) - y (1 + z^2/2) = 0, or

(2) -1/6 x z^3 + x z + 1/2 y z^2 - y = 0

This helix (2) turns 90 degrees in the range 0 <= z <= sqrt(2)/2.
By using scale <2 2 2>, the helix defined below turns 90 degrees in the range 0 <= z <= sqrt(2) = 1.4042.

`Hyperbolic_Torus`
Hyperbolic Torus having major radius sqrt(40), minor radius sqrt(12). This figure is
generated by sweeping a circle along the arms of a hyperbola. The equation is:
x^4 + 2 x^2 y^2 - 2 x^2 z^2 - 104 x^2 + y^4 - 2 y^2 z^2 + 56 y^2 + z^4 + 104 z^2 + 784 = 0

`Lemniscate`
Lemniscate of Gerono. This figure looks like two teardrops with their pointed ends connected. It is formed by rotating the Lemniscate of Gerono about the x-axis. The formula is:
x^4 - x^2 + y^2 + z^2 = 0

`Quartic_Loop_1`
This is a figure with a bumpy sheet on one side and something that looks like a paraboloid (but with an internal bubble). The formula is:
(x^2 + y^2 + a c x)^2 - (x^2 + y^2)(c - a x)^2
-99*x^4+40*x^3-98*x^2*y^2-98*x^2*z^2+99*x^2+40*x*y^2
+40*x*z^2+y^4+2*y^2*z^2-y^2+z^4-z^2

`Monkey_Saddle`
This surface has three parts that sweep up and three down. This gives a saddle that has a place for two legs and a tail. The equation is:
`z = c (x^3 - 3 x y^2)`
The value c gives a vertical scale to the surface - the smaller the value of c, the flatter the surface will be (near the origin).

`Parabolic_Torus_40_12`
Parabolic Torus having major radius sqrt(40), minor radius sqrt(12). This figure is generated by sweeping a circle along the arms of a parabola. The equation is:
x^4 + 2 x^2 y^2 - 2 x^2 z - 104 x^2 + y^4 - 2 y^2 z + 56 y^2 + z^2 + 104 z + 784 = 0

`Piriform`
This figure looks like a hersheys kiss. It is formed by sweeping a Piriform about the x-axis. A basic form of the equation is:
(x^4 - x^3) + y^2 + z^2 = 0.

`Quartic_Paraboloid`
Quartic parabola - a 4th degree polynomial (has two bumps at the bottom) that has been swept around the z axis. The equation is:
0.1 x^4 - x^2 - y^2 - z^2 + 0.9 = 0

`Quartic_Cylinder`
Quartic Cylinder - a Space Needle?

`Steiner_Surface`
Steiners quartic surface

`Torus_40_12`
Torus having major radius sqrt(40), minor radius sqrt(12).

`Witch_Hat`
Witch of Agnesi.

`Sinsurf`
Very rough approximation to the sin-wave surface z = sin(2 pi x y).
In order to get an approximation good to 7 decimals at a distance of 1 from the origin would require a polynomial of degree around 60, which would require around 200,000 coefficients. For best results, scale by something like <1 1 0.2>.

3.4.9.1.22 Skies.inc

These files contain some predefined skies for you to use in your scenes.

skies.inc: There are textures and pigment definitions in this file.

 all pigment definitions start with "P_"
 all sky_spheres start with "S_"
 all textures start with "T_"
 and all objects start with "O_"

Pigments:

 `P_Cloud1`
 `P_Cloud2`
 `P_Cloud3`

Sky Spheres:

 `S_Cloud1`
 This sky_sphere uses P_Cloud2 and P_Cloud3.
 `S_Cloud2`
 This sky_sphere uses P_Cloud4.
 `S_Cloud3`
 This sky_sphere uses P_Cloud2.
 `S_Cloud4`
 This sky_sphere uses P_Cloud3.
 `S_Cloud5`
 This sky_sphere uses a custom pigment.

Textures:

 `T_Cloud1`
 2-layer texture using P_Cloud1 pigment, contains clear regions.
 `T_Cloud2`
 1-layer texture, contains clear regions.
 `T_Cloud3`
 2-layer texture, contains clear regions.

Objects:

 `O_Cloud1`
 Sphere, radius 10000 with T_Cloud1 texture.
 `O_Cloud2`
 Union of 2 planes, with T_Cloud2 and T_Cloud3.

3.4.9.1.23 Stars.inc

stars.inc: This file contains predefined starfield textures. The starfields become denser and more colorful with the number, with Starfield6 being the densest and most colorful.

`Starfield1`
`Starfield2`
`Starfield3`
`Starfield4`
`Starfield5`
`Starfield6`

3.4.9.1.24 Stones.inc

The file stones.inc simply includes both stones1.inc and stones2.inc, and the file stoneold.inc provides backwards compatability for old scenes, the user is advised to use the textures in stones1.inc instead.

The two files stones1.inc and stones2.inc contain lists of predefined stone textures.

The file stones1.inc contains texture definitions for:

 T_Grnt0 to T_Grnt29
 T_Grnt1a to T_Grnt24a
 T_Stone0 to T_Stone24

The T_GrntXX, T_GrntXXa, and CrackX textures are building blocks that are used to create the final usable T_StoneX textures (and other textures that *you* design, of course!)

The T_GrntXX textures generally contain no transparency, but the T_GrntXXa textures DO contain transparency. The CrackX textures are clear with thin opaque bands, simulating cracks.

The file stones2.inc provides additional stone textures, and contains texture definitions for T_Stone25 to T_Stone44.

3.4.9.1.25 Stdinc.inc

This file simply includes the most commonly used include files,
so you can get all of them with a single #include.
The files included are:

colors.inc
shapes.inc
transforms.inc
consts.inc
functions.inc
math.inc
rand.inc

3.4.9.1.26 Strings.inc

This include contains macros for manipulating and generating text strings.

`CRGBStr(C, MinLen, Padding)` and `CRGBFTStr(C, MinLen, Padding)`: These macros convert a color to a string. The format of the output string is "rgb < R, G, B>" or "rgbft < R, G, B, F, T>", depending on the macro being called.

Parameters:

 `C` = The color to be turned into a string.
 `MinLen` = The minimum length of the individual components, analogous to the second parameter of str().
 `Padding` = The padding to use for the components, see the third parameter of the str() function for details.

`Str(A)`: This macro creates a string containing a float with the systems default precision. It is a shortcut for using the str() function.

Parameters:
 `A` = The float to be converted to a string.

`VStr2D(V), VStr(V)`: These macros create strings containing vectors using POV syntax (<X,Y,Z>) with the default system precision. VStr2D() works with 2D vectors, VStr() with 3D vectors. They are shortcuts for using the `vstr()` function.

Parameters:

 `V` = The vector to be converted to a string.

`Vstr2D(V,L,P), Vstr(V,L,P)`: These macros create strings containing vectors using POV syntax (<X,Y,Z>) with user specified precision. Vstr2D() works with 2D vectors, Vstr() with 3D vectors. They are shortcuts for using the vstr() function. The function of L and P is the same as in `vstr` specified in String Functions.

Parameters:

`V` = The vector to be converted to a string.
`L` = Minimum length of the string and the type of left padding used if the string's representation is shorter than the minimum.
`P` = Number of digits after the decimal point.

`Triangle_Str(A, B, C)` and `Smooth_Triangle_Str(A, NA, B, NB, C, NC)`: These macros take vertex and normal information and return a string representing a triangle in POV-Ray syntax. They are mainly useful for generating mesh files.

Parameters:

 `A, B, C` = Triangle vertex points.
 `NA, NB, NC` = Triangle vertex normals (Smooth_Triangle_Str() only).

`Parse_String(String)`: This macro takes a string, writes it to a file, and then includes that file. This has the effect of parsing that string: "`Parse_String("MyColor")`" will be seen by POV-Ray as "`MyColor`".

Parameters:

 `String` = The string to be parsed.

3.4.9.1.27 Sunpos.inc

 This file only contains the sunpos() macro

`sunpos(Year, Month, Day, Hour, Minute, Lstm, LAT, LONG)`: The macro returns the position of the sun, for a given date, time, and location on earth. The suns position is also globally declared as the vector `SolarPosition`. Two other declared vectors are the `Az` (Azimuth) and `Al` (Altitude), these can be useful for aligning an object (media container) with the sunlight. Assumption: in the scene north is in the +Z direction, south is -Z.

Parameters:

 `Year`= The year in four digits.
 `Month`= The month number (1-12).
 `Day`= The day number (1-31).
 `Hour`= The hour of day in 24 hour format (0-23).
 `Minute`= The minutes (0-59).
 `Lstm`= Meridian of your local time zone in degrees (+1 hour = +15 deg, east = positive, west = negative)
 `LAT`= Lattitude in degrees.decimal, northern hemisphere = positive, southern = negative
 `LONG`= Longitude in degrees.decimal, east = positive, west is negative

Usage:

```

#include "sunpos.inc"

light_source {
 //Greenwich, noon on the longest day of 2000
 SunPos(2000, 6, 21, 12, 2, 0, 51.4667, 0.00)
 rgb 1
 }

cylinder{
 <-2,0,0>,<2,0,0>,0.1
 rotate <0, Az-90, Al> //align cylinder with sun
 texture {...}
 }
```

Note: The default distance of the sun from the origin is 1e+9 units.

3.4.9.1.28 Textures.inc

This file contains many predefined textures, including wood, glass, and metal textures, and a few texture/pattern generation macros.

3.4.9.1.28.1 Stones

Stone Pigments:

`Jade_Map, Jade`
 Drew Wells' superb Jade. Color map works nicely with other textures, too.
`Red_Marble_Map, Red_Marble`
 Classic white marble with red veins. Over-worked, like checkers.
`White_Marble_Map, White_Marble`
 White marble with black veins.
`Blood_Marble_Map, Blood_Marble`
 Light blue and black marble with a thin red vein.
`Blue_Agate_Map, Blue_Agate`
 A grey blue agate -- kind of purplish.
`Sapphire_Agate_Map, Sapphire_Agate`
 Deep blue agate -- almost glows.
`Brown_Agate_Map, Brown_Agate`
 Brown and white agate -- very pretty.
`Pink_Granite_Map, Pink_Granite`
 Umm, well, pink granite.

Stone textures:

`PinkAlabaster`
 Gray-pink alabaster or marble. Layers are scaled for a unit object and relative to each other.

Note: This texture has very tiny dark blue specks that are often mistaken for rendering errors.
 Underlying surface is very subtly mottled with bozo.
 Second layer texture has some transmit values, yet a fair amount of color.
 Veining is kept quite thin in color map and by the largish scale.

3.4.9.1.28.2 Skies

Sky pigments:

`Blue_Sky_Map, Blue_Sky`
 Basic blue sky with clouds.
`Bright_Blue_Sky`
 Bright blue sky with very white clouds.
`Blue_Sky2`
 Another sky.
`Blue_Sky3`
 Small puffs of white clouds.
`Blood_Sky`
 Red sky with yellow clouds -- very surreal.
`Apocalypse`
 Black sky with red and purple clouds.
 Try adding turbulence values from 0.1 - 5.0
`Clouds`
 White clouds with transparent sky.
`FBM_Clouds`
`Shadow_Clouds`
 A multilayered cloud texture (a real texture, not a pigment).

3.4.9.1.28.3 Woods

Wood pigments:

Several wooden pigments by Tom Price:

 `Cherry_Wood`
 A light reddish wood.
 `Pine_Wood`
 A light tan wood whiteish rings.
 `Dark_Wood`
 Dark wood with a,ish hue to it.
 `Tan_Wood`
 Light tan wood with brown rings.
 `White_Wood`
 A very pale wood with tan rings -- kind of balsa-ish.
 `Tom_Wood`
 Brown wood - looks stained.

 `DMFWood1, DMFWood2, DMFWood3, DMFWood4, DMFWood5`
 The scaling in these definitions is relative to a unit-sized object (radius 1).

Note: These wood definitions are functionally equivalent to a log lying along the z axis. For best results, think like a woodcutter trying to extract the nicest board out of that log. A little tilt along the x axis will give elliptical rings of grain like you would expect to find on most boards.

Wood textures:

`DMFWood6`
 This is a three-layer wood texture. Renders rather slowly because of the transparent layers and the two layers of turbulence, but it looks great. Try other colors of varnish for simple variations.
`DMFLightOak`
 Is this really oak? I dunno. Quite light, maybe more like spruce.
`DMFDarkOak`
 Looks like old desk oak if used correctly.
`EMBWood1`
 Wood by Eric Barish

Doug Otwell woods:

`Yellow_Pine`
 Yellow pine, close grained.
`Rosewood`
`Sandalwood`
 makes a great burled maple, too

3.4.9.1.28.4 Glass

`Glass_Finish` is a generic glass finish, `Glass_Interior` is a generic glass interior, it just adds an ior of 1.5.

Glass materials:

`M_Glass`
 Just glass.
`M_Glass2`
 Probably more of a Plexiglas than glass.
`M_Glass3`
 An excellent lead crystal glass!
`M_Green_Glass`

Glass textures contributed by Norm Bowler, of Richland WA. NBglass_finish is used by these materials.

`M_NBglass`
`M_NBoldglass`
`M_NBwinebottle`
`M_NBbeerbottle`

A few color variations on Norm's glass.

`M_Ruby_Glass`
`M_Dark_Green_Glass`
`M_Yellow_Glass`
`M_Orange_Glass`
`M_Vicks_Bottle_Glass`

3.4.9.1.28.5 Metals

Metal finishes:

`Metal`
 Generic metal finish.
`SilverFinish`
 Basic silver finish
`Metallic_Finish`

Metal textures:

`Chrome_Metal, Brass_Metal, Bronze_Metal, Gold_Metal, Silver_Metal, Copper_Metal`
 A series of metallic textures using the Metal finish (except for Chrome_Metal, which has a custom finish). There are identical textures ending in _Texture instead of _Metal, but use of those names is discouraged.
`Polished_Chrome`
 A highly reflective Chrome texture.
`Polished_Brass`
 A highly reflective brass texture.
`New_Brass`
 Beautiful military brass texture!
`Spun_Brass`
 Spun Brass texture for cymbals & such
`Brushed_Aluminum`
 Brushed aluminum (brushed along X axis)
`Silver1`
`Silver2`
`Silver3`
`Brass_Valley`
 Sort of a Black Hills Gold, black, white, and orange specks or splotches.
`Rust`
`Rusty_Iron`
`Soft_Silver`
`New_Penny`
`Tinny_Brass`
`Gold_Nugget`
`Aluminum`
`Bright_Bronze`

3.4.9.1.28.6 Special textures

`Candy_Cane`
Red and white stripes - Looks best on a y axis Cylinder.
It spirals because it's gradient on two axis.
`Peel`
Orange and Clear stripes spiral around the texture to make an object look like it was Peeled. Now, you too can be M.C. Escher!
`Y_Gradient`
`X_Gradient`
`M_Water`
Wavy water material. Requires a sub-plane, and may require scaling to fit your scene.

Warning: Water texture has been changed to M_Water material, see explanation in the glass section of this file.
`Cork`
`Lightning_CMap1, Lightning1, and Lightning_CMap2, Lightning2`
These are just lightning textures, they look like arcing electricity...earlier versions misspelled them as Lightening.
`Starfield`
A starfield texture by Jeff Burton

3.4.9.1.28.7 Texture and pattern macros

`Irregular_Bricks_Ptrn (Mortar Thickness, X-scaling, Variation, Roundness)`: This function pattern creates a pattern of bricks of varying lengths on the x-y plane. This can be useful in building walls that do not look like they were built by a computer. Note that mortar thickness between bricks can vary somewhat, too.

Parameters:

 `Mortar Thickness` = Thickness of the mortar (0-1).
`X-scaling` = The scaling of the bricks (but not the mortar) in the x direction.
`Variation` = The amount by which brick lengths will vary (0=none, 1=100%).
`Roundness` = The roundness of the bricks (0.01=almost rectangular, 1=very round).

`Tiles_Ptrn()`: This macro creates a repeating box pattern on the x-y plane. It can be useful for creating grids. The cells shade continuously from the center to the edges.

Parameters: None.

`Hex_Tiles_Ptrn()`: This macro creates a pattern that is a sort of cross between the hexagon pattern and a repeating box pattern. The hexagonal cells shade continuously from the center to the edges.

Parameters: None.

`Star_Ptrn (Radius, Points, Skip)`: This macro creates a pattern that resembles a star. The pattern is in the x-y plane, centered around the origin.

Parameters:

 `Radius` = The radius of a circle drawn through the points of the star.
 `Points` = The number of points on the star.
 `Skip` = The number of points to skip when drawing lines between points to form the star.
 A normal 5-pointed star skips 2 points. A Star of David also skips 2 points. Skip
 must be less than Points/2 and greater than 0. Integers are preferred but not
 required. Skipping 1 point makes a regular polygon with Points sides.
 `Pigment` = The pigment to be applied to the star.
 `Background` = The pigment to be applied to the background.

3.4.9.1.29 Transforms.inc

Several useful transformation macros. All these macros produce transformations, you can use them anywhere you can use scale, rotate, etc. The descriptions will assume you are working with an object, but the macros will work fine for textures, etc.

`Shear_Trans(A, B, C)`: This macro reorients and deforms an object so its original XYZ axes point along A, B, and C, resulting in a shearing effect when the vectors are not perpendicular. You can also use vectors of different lengths to affect scaling, or use perpendicular vectors to reorient the object.

Parameters:

 `A, B, C` = Vectors representing the new XYZ axes for the transformation.

`Matrix_Trans(A, B, C, D)`: This macro provides a way to specify a matrix transform with 4 vectors. The effects are very similar to that of the Shear_Trans() macro, but the fourth parameter controls translation.

Parameters:

 `A, B, C, D` = Vectors for each row of the resulting matrix.

`Axial_Scale_Trans(Axis, Amt)`: A kind of directional scale, this macro will stretch an object along a specified axis.

Parameters:

 `Axis` = A vector indicating the direction to stretch along.
 Amt = The amount to stretch.

`Axis_Rotate_Trans(Axis, Angle)`: This is equivalent to the transformation done by the vaxis_rotate() function, it rotates around an arbitrary axis.

Parameters:

 `Axis` = A vector representing the axis to rotate around.
 `Angle` = The amount to rotate by.

`Rotate_Around_Trans(Rotation, Point)`: Ordinary rotation operates around the origin, this macro rotates around a specific point.

Parameters:

 `Rotation` = The rotation vector, the same as the parameter to the rotate keyword.
 `Point` = The point to rotate around.

`Reorient_Trans(Axis1, Axis2)`: This aligns `Axis1` to `Axis2` by rotating the object around a vector perpendicular to both axis1 and axis2.

Parameters:

 `Axis1` = Vector to be rotated.
 `Axis2` = Vectors to be rotated towards.

`Point_At_Trans(YAxis)`: This macro is similar to Reorient_Trans(), but it points the y axis along Axis.

Parameters:

 `YAxis` = The direction to point the y axis in.

`Center_Trans(Object, Axis)`: Calculates a transformation which will center an object along a specified axis. You indicate the axes you want to center along by adding "x", "y", and "z" together in the Axis parameter.

Note: This macro actually computes the transform to center the bounding box of the object, which may not be entirely accurate. There is no way to define the center of an arbitrary object.

Parameters:

 `Object` = The object the center transform is being computed for.
 `Axis` = The axes to center the object on.

Usage:

```

object {MyObj Center_Trans(MyObj, x)} //center along x axis
```

 You can also center along multiple axes:

```

object {MyObj Center_Trans(MyObj, x+y)} //center along x and y axis
```

`Align_Trans(Object, Axis, Pt)`: Calculates a transformation which will align the sides of the bounding box of an object to a point. Negative values on Axis will align to the sides facing the negative ends of the coordinate system, positive values will align to the opposite sides, 0 means not to do any alignment on that axis.

Parameters:

 `Object` = The object being aligned.
 `Axis` = A combination of +x, +y, +z, -x, -y, and -z, or a vector where each component is -1, 0, or +1 specifying the faces of the bounding box to align to the point.
 `Point` = The point to which to align the bounding box of the object.

Usage:

```

object {
 MyObj
 Align_Trans(MyObj, x, Pt) //Align right side of object to be
 //coplanar with Pt
 Align_Trans(MyObj,-y, Pt) //Align bottom of object to be
 // coplanar with Pt
 }
```

`vtransform(Vect, Trans)` and `vinv_transform(Vect, Trans)`: The `vtransform()` macro takes a transformation (rotate, scale, translate, etc...) and a point, and returns the result of applying the transformation to the point. The `vinv_transform()` macro is similar, but applies the inverse of the transform, in effect undoing the transformation. You can combine transformations by enclosing them in a transform block.

Parameters:

 `Vect` = The vector to which to apply the transformation.
 `Trans` = The transformation to apply to Vect.

`Spline_Trans(Spline, Time, SkyVector, ForeSight, Banking)`: This macro aligns an object to a spline for a given time value. The Z axis of the object will point in the forward direction of the spline and the Y axis of the object will point upwards.

Parameters:

 `Spline` = The spline that the object is aligned to.
 `Time` = The time value to feed to the spline, for example clock.
 `Sky` = The vector that is upwards in your scene, usually y.
 `Foresight` = A positive value that controls how much in advance the object will turn and bank. Values close to 0 will give precise results, while higher values give smoother results. It will not affect parsing speed, so just find the value that looks best.
 `Banking` = How much the object tilts when turning. The amount of tilting is equally much controlled by the ForeSight value.

Usage:

```

object {MyObj Spline_Trans(MySpline, clock, y, 0.1, 0.5)}
```

3.4.9.1.30 Woods.inc

The file woods.inc contains predefined wood textures and pigments.

The pigments are prefixed with P_, and do not have color_maps, allowing you to specify a color map from woodmaps.inc or create your own. There are two groups, "A" and "B": the A series is designed to work better on the bottom texture layer, and the
B series is designed for the upper layers, with semitransparent color maps. The pigments with the same number were designed to work well together, but you do not necessarily have to use them that way.

The textures are prefixed with T_, and are ready to use. They are designed with the major axis of the woodgrain cylinder aligned along the Z axis. With the exception of the few of the textures which have a small amount of rotation built-in, the textures will exhibit a very straight grain pattern unless you apply a small amount of x-axis rotation to them (generally 2 to 4 degrees seems to work well).

Pigments:

`P_WoodGrain1A, ..., P_WoodGrainA`
`P_WoodGrain1B, ..., P_WoodGrainB`

Textures:

`T_Wood1`
Natural oak (light)
`T_Wood2`
Dark brown
`T_Wood3`
Bleached oak (white)
`T_Wood4`
Mahogany (purplish-red)
`T_Wood5`
Dark yellow with reddish overgrain
`T_Wood6`
Cocabola (red)
`T_Wood7`
Yellow pine (ragged grain)
`T_Wood8`
Dark brown. Walnut?
`T_Wood9`
Yellowish-brown burl (heavily turbulated)
`T_Wood10`
Soft pine (light yellow, smooth grain)
`T_Wood11`
Spruce (yellowish, very straight, fine grain)
`T_Wood12`
Another very dark brown. Walnut-stained pine, perhaps?
`T_Wood13`
Very straight grained, whitish
`T_Wood14`
Red, rough grain
`T_Wood15`
Medium brown
`T_Wood16`
Medium brown
`T_Wood17`
Medium brown
`T_Wood18`
Orange
`T_Wood19, ..., T_Wood30`
Golden Oak.
`T_Wood31`
A light tan wood - heavily grained (variable coloration)
`T_Wood32`
A rich dark reddish wood, like rosewood, with smooth-flowing grain
`T_Wood33`
Similar to T_WoodB, but brighter
`T_Wood34`
Reddish-orange, large, smooth grain.
`T_Wood35`
Orangish, with a grain more like a veneer than a plank

3.4.9.1.31 Woodmaps.inc

The file woodmaps.inc contains color_maps designed for use in wood textures. The M_WoodXA maps are intended to be used in the first layer of a multilayer texture, but can be used in single-layer textures. The M_WoodXB maps contain transparent areas, and are intended to be used in upper texture layers.

Color maps:

`M_Wood1A, ..., M_Wood19A`
`M_Wood1B, ..., M_Wood19B`

### 3.4.9.2 Old Files

These files could be considered either obsolete or deprecated. They have been included for legacy reasons.

3.4.9.2.1 Glass_old.inc

This file contains glass textures for POV-Ray versions 3.1 and earlier.
These textures do not take advantage of the new features introduced with POV-Ray 3.5 and are
included for backwards compatability, you will get better results with the
materials in glass.inc.

Note: As of version 3.7 the definitions in `glass_old.inc` have been `deprecated`. To suppress warnings generated from using these textures you should consider converting them to materials.

Using the following example:

```
texture {T_Glass4} interior {I_Glass caustics 1}
```

should be rewritten as:

```

material {
 texture {
 pigment { color rgbf <0.98, 1.0, 0.99, 0.75> }
 finish { F_Glass4 }
 }
 interior { I_Glass caustics 1 }
 }
```

3.4.9.2.1.1 Glass finishes

`F_Glass1, ..., F_Glass4`

3.4.9.2.1.2 Glass textures

`T_Glass1`
Simple clear glass.
`T_Glass2`
More like an acrylic plastic.
`T_Glass3`
An excellent lead crystal glass.
`T_Glass4`
`T_Old_Glass`
`T_Winebottle_Glass`
`T_Beerbottle_Glass`
`T_Ruby_Glass`
`T_Green_Glass`
`T_Dark_Green_Glass`
`T_Yellow_Glass`
`T_Orange_Glass`
Orange/amber glass.
`T_Vicksbottle_Glass`

3.4.9.2.2 Shapes_old.inc

`Ellipsoid, Sphere`
 Unit-radius sphere at the origin.

`Cylinder_X, Cylinder_Y, Cylinder_Z`
 Infinite cylinders.

`QCone_X, QCone_Y, QCone_Z`
 Infinite cones.

`Cone_X, Cone_Y, Cone_Z`
 Closed capped cones: unit-radius at -1 and 0 radius at +1 along each axis.

`Plane_YZ, Plane_XZ, Plane_XY`
 Infinite planes passing through the origin.

`Paraboloid_X, Paraboloid_Y, Paraboloid_Z`
 y^2 + z^2 - x = 0

`Hyperboloid, Hyperboloid_Y`
 y - x^2 + z^2 = 0

`UnitBox, Cube`
 A cube 2 units on each side, centered on the origin.

`Disk_X, Disk_Y, Disk_Z`
 Capped cylinders, with a radius of 1 unit and a length of 2 units, centered on the origin.

3.4.9.2.3 Stage1.inc

This file simply contains a camera, a light_source, and a ground plane, and includes colors.inc, textures.inc, and shapes.inc.

3.4.9.2.4 Stdcam.inc

This file simply contains a camera, a light_source, and a ground plane.

3.4.9.2.5 Stones1.inc

`T_Grnt0`
 Gray/Tan with Rose.
`T_Grnt1`
 Creamy Whites with Yellow & Light Gray.
`T_Grnt2`
 Deep Cream with Light Rose, Yellow, Orchid, & Tan.
`T_Grnt3`
 Warm tans olive & light rose with cream.
`T_Grnt4`
 Orchid, Sand & Mauve.
`T_Grnt5`
 Medium Mauve Med.Rose & Deep Cream.
`T_Grnt6`
 Med. Orchid, Olive & Dark Tan mud pie.
`T_Grnt7`
 Dark Orchid, Olive & Dark Putty.
`T_Grnt8`
 Rose & Light Cream Yellows
`T_Grnt9`
 Light Steely Grays
`T_Grnt10`
 Gray Creams & Lavender Tans
`T_Grnt11`
 Creams & Grays Kahki
`T_Grnt12`
 Tan Cream & Red Rose
`T_Grnt13`
 Cream Rose Orange
`T_Grnt14`
 Cream Rose & Light Moss w/Light Violet
`T_Grnt15`
 Black with subtle chroma
`T_Grnt16`
 White Cream & Peach
`T_Grnt17`
 Bug Juice & Green
`T_Grnt18`
 Rose & Creamy Yellow
`T_Grnt19`
 Gray Marble with White feather Viens
`T_Grnt20`
 White Marble with Gray feather Viens
`T_Grnt21`
 Green Jade
`T_Grnt22`
 Clear with White feather Viens (has some transparency)
`T_Grnt23`
 Light Tan to Mauve
`T_Grnt24`
 Light Grays
`T_Grnt25`
 Moss Greens & Tan
`T_Grnt26`
 Salmon with thin Green Viens
`T_Grnt27`
 Dark Green & Browns
`T_Grnt28`
 Red Swirl
`T_Grnt29`
 White, Tan, w/ thin Red Viens

`T_Grnt0a`
 Translucent T_Grnt0
`T_Grnt1a`
 Translucent T_Grnt1
`T_Grnt2a`
 Translucent T_Grnt2
`T_Grnt3a`
 Translucent T_Grnt3
`T_Grnt4a`
 Translucent T_Grnt4
`T_Grnt5a`
 Translucent T_Grnt5
`T_Grnt6a`
 Translucent T_Grnt6
`T_Grnt7a`
 Translucent T_Grnt7
`T_Grnt8a`
 Aqua Tints
`T_Grnt9a`
 Transmit Creams With Cracks
`T_Grnt10a`
 Transmit Cream Rose & light yellow
`T_Grnt11a`
 Transmit Light Grays
`T_Grnt12a`
 Transmit Creams & Tans
`T_Grnt13a`
 Transmit Creams & Grays
`T_Grnt14a`
 Cream Rose & light moss
`T_Grnt15a`
 Transmit Sand & light Orange
`T_Grnt16a`
 Cream Rose & light moss (again?)
`T_Grnt17a`
 ???
`T_Grnt18a`
 ???
`T_Grnt19a`
 Gray Marble with White feather Viens with Transmit
`T_Grnt20a`
 White Feather Viens
`T_Grnt21a`
 Thin White Feather Viens
`T_Grnt22a`
 ???
`T_Grnt23a`
 Transparent Green Moss
`T_Grnt24a`
 ???

`T_Crack1`
 T_Crack & Red Overtint
`T_Crack2`
 Translucent Dark T_Cracks
`T_Crack3`
 Overtint Green w/ Black T_Cracks
`T_Crack4`
 Overtint w/ White T_Crack

The StoneXX textures are the complete textures, ready to use.

`T_Stone1`
 Deep Rose & Green Marble with large White Swirls
`T_Stone2`
 Light Greenish Tan Marble with Agate style veining
`T_Stone3`
 Rose & Yellow Marble with fog white veining
`T_Stone4`
 Tan Marble with Rose patches
`T_Stone5`
 White Cream Marble with Pink veining
`T_Stone6`
 Rose & Yellow Cream Marble
`T_Stone7`
 Light Coffee Marble with darker patches
`T_Stone8`
 Gray Granite with white patches
`T_Stone9`
 White & Light Blue Marble with light violets
`T_Stone10`
 Dark Brown & Tan swirl Granite with gray undertones
`T_Stone11`
 Rose & White Marble with dark tan swirl
`T_Stone12`
 White & Pinkish Tan Marble
`T_Stone13`
 Medium Gray Blue Marble
`T_Stone14`
 Tan & Olive Marble with gray white veins
`T_Stone15`
 Deep Gray Marble with white veining
`T_Stone16`
 Peach & Yellow Marble with white veining
`T_Stone17`
 White Marble with gray veining
`T_Stone18`
 Green Jade with white veining
`T_Stone19`
 Peach Granite with white patches & green trim
`T_Stone20`
 Brown & Olive Marble with white veining
`T_Stone21`
 Red Marble with gray & white veining
`T_Stone22`
Dark Tan Marble with gray & white veining
`T_Stone23`
Peach & Cream Marble with orange veining
`T_Stone24`
 Green & Tan Moss Marble

3.4.9.2.6 Stones2.inc

`T_Stone25, ..., T_Stone44`

### 3.4.9.3 Other Files

There are various other files in the include files collection. For example font files, color maps, and images for use in height fields or image maps.

3.4.9.3.1 Font Files

The fonts cyrvetic.ttf and timrom.ttf were donated to the POV-Team by their creator, Ted Harrison (CompuServe:70220,344) and were built using his FontLab for Windows by SoftUnion, Ltd. of St. Petersburg, Russia.

The font crystal.ttf was donated courtesy of Jerry Fitzpatrick, Red Mountain Corporation, redmtn [at] ix.netcom.com

The font povlogo.ttf is created by Fabien Mosen and based on the POV-Ray logo design by Chris Colefax.

`crystal.ttf`
A fixed space programmer's font.
`cyrvetic.ttf`
A proportional spaces sans-serif font.
`timrom.ttf`
A proportional spaces serif font.
`povlogo.ttf`
Only contains the POV-Ray logo.

Note: In version 3.7 these fonts were built-in to the application. See the `text` object for more details.

3.4.9.3.2 Color Map Files

These are 255-color color_maps, and are in individual files because of their size.

`ash.map`
`benediti.map`
`bubinga.map`
`cedar.map`
`marbteal.map`
`orngwood.map`
`pinkmarb.map`
`rdgranit.map`
`teak.map`
`whiteash.map`

3.4.9.3.3 Image Files

`bumpmap_.png`
A color mandelbrot fractal image, presumably intended for use as a bumpmap.
`fract003.png`
Some kind of fractal landscape, with color for blue water, brown land, and white peaks.
`maze.png`
A maze.
`mtmand.pot`
A grayscale mandelbrot fractal.
`mtmandj.png`
A 2D color julia fractal.
`plasma2.png, plasma3.png`
Plasma fractal images, mainly useful for landscape height fields. The file plasma3.png
is a smoother version of plasma2.png, plasma1.png does not exist.
`povmap.png`
The text "Persistance of Vision" in green on a blue background, framed in black and red.
`test.png`
A test image, the image is divided into 4 areas of different colors (magenta, yellow,
cyan, red) with black text on them, and the text "POV-Ray" is centered on the image in white.
`spiral.df3`
A 3D bitmap density file. A spiral, galaxy shape.

window.__CF$cv$params={r:'a5aa30d8ba176e2f',t:'MTc5NDcwMzA4Mg==',u:'01a1dca3c3ac73f68bfd50d5e254594c',ut:'NQHbxb1VvNyq_9rlsRd3XpzDDHpibZgVJRMpfe8QXNs-1794703082-1.2.1.1-1rLaqnKh6qBTFObsomAm4wXgOUWR368imVo0g9VPVqpLLgvbvmGVuEJlJxVafnbmRqaJz3BqMzLQXSEoTj1zwqvGXixaXi6YFFeUWEDao8s',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30d8ba176e2f',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();
