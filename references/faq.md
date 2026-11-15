# POV-Ray FAQ & HowTo Collection
Source: POV-Ray wiki (wiki.povray.org) — cached copies (Wayback crawls
2025-06-14 and 2026-04-11..15). This file merges the wiki's
Knowledgebase 'Language Questions and Tips' and all HowTo articles.

## Language: Questions and Tips

### Topic 1

How do I make a visible light source? or: Although I put the camera just in front of my light source, I can't see anything. What am I doing wrong?

A light source in POV-Ray is only a concept. When you add a light source to the scene, you are actually saying to POV-Ray hey, there is light coming from this point. As the name says, it's a light source, not a physical light (like a light bulb or a bright spot like a star). POV-Ray doesn't add anything to that place where the light is coming, ie. there is nothing there, only empty space. It's just a kind of mathematical point POV-Ray uses to make shading calculations.

To make the light source visible, you have to put something there. There is a `looks_like` keyword in the `light_source` block which allows to easily attach an object to the light source. This object implicitly doesn't cast any shadows. You can make something like this:

```
light_source
{ <0,0,0>, 1
 looks_like
 { sphere
 { <0,0,0>,0.1
 pigment { rgb 1 }
 finish { ambient 1 }
 }
 }
 translate <10,20,30>
}
```

Note: It's a good idea to define both things, the `light_source` and the `looks_like` object, at the origin, and then translate them to their right place, also the `finish { ambient 1 }` which makes the sphere to apparently glow (see also the next question).

You can also get visible light sources using other techniques: Media, lens flare (available as 3rd party include file), glow patch, etc.

End of Topic: Go Back to the Table of Contents

### Topic 2

How do I make bright objects, which look like they are emitting light?

There is a simple trick to achieve this: Set the ambient value of the object to 1 or higher. This makes POV-Ray to add a very bright illumination value to the object so the color of the object is in practice taken as is, without darkening it due to shadows and shading. This results in an object which seems to glow light by itself even if it's in full darkness (useful to make visible light sources, or small lights like leds which do not cast any considerable light to their surroundings but can be easily seen even in the darkness).

A more sophisticated method would be using an emitting media inside the object (and making the object itself transparent or semi-transparent).

End of Topic: Go Back to the Table of Contents

### Topic 3

How do I move the camera in a circular path while looking at the origin?

There are two ways to make this: The easy (and limited) way, and the more mathematical way.

The easy way:

```
camera
{ location <0,0,-10>
 look_at 0
 rotate <0,clock*360,0>
}
```

This puts the camera at 10 units in the negative Z-axis and then rotates it around the Y-axis while looking at the origin (it makes a circle of radius 10).

The mathematical way:

```
camera
{ location <10*sin(2*pi*clock),0,-10*cos(2*pi*clock)>
 look_at 0
}
```

This makes exactly the same thing as the first code, but this way you can control more precisely the path of the camera. For example you can make the path elliptical instead of circular by changing the factors of the sine and the cosine (for example instead of 10 and 10 you can use 10 and 5 which makes an ellipse with the major radius 10 and minor radius 5).

An easier way to do the above is to use the vrotate() function, which handles the sin() and cos() stuff for you, as well as allowing you to use more complex rotations.

```
camera
{ location vrotate(x*10, y*360*clock)
 look_at 0
}
```

To get an ellipse with this method, you can just multiply the result from vrotate by a vector, scaling the resulting circle. With the last two methods you can also control the look_at vector (if you don't want it looking just at the origin).

You could also do more complex transformations combining translate, scale, rotate, and matrix transforms by replacing the vrotate() call with a call of the vtransform() function found in `functions.inc` (new in POV-Ray 3.5).

End of Topic: Go Back to the Table of Contents

### Topic 4

How do I use an image to texture my object?

The answer to this question can be easily found in the POV-Ray documentation, so I will just quote the syntax:

```
pigment
{ image_map
 { gif "image.gif"
 map_type 1
 }
}
```

Note: that in order for the image to be aligned properly, either the object has to be located at the origin when applying the pigment or the pigment has to be transformed to align with the object. It is generally easiest to create the object at the origin, apply the texture, then move it to wherever you want it.

Substitute the keyword `gif` with the type of image you are using (if it isn't a GIF): `tga, iff, ppm, pgm, png or sys`.

A `map_type 0` gives the default planar mapping.

A `map_type 1` gives a spherical mapping (maps the image onto a sphere).

With `map_type 2` you get a cylindrical mapping (maps the image onto a cylinder).

Finally `map_type 5` is a torus or donut shaped mapping (maps the image onto a torus).

See the documentation for more details.

End of Topic: Go Back to the Table of Contents

### Topic 5

How can I generate a spline, for example for a camera path for an animation?

POV-Ray 3.5 has a splines feature that allows you to create splines. This is covered in the documentation and there are demo files showing examples of use. There exist also third party include files for spline generation that have greater flexibility than the internal splines, for example the spline macro by Chris Colefax.

End of Topic: Go Back to the Table of Contents

### Topic 6

How can I simulate motion blur in POV-Ray?

The official POV-Ray 3.7 doesn't support motion blur calculations, but there are some patched versions which do. (eg. the so-called MegaPov, which at the time of writing this documentation was still based on POV-Ray 3.5 code). See the MegaPov site.

You can also use other tools to make this. One way to simulate motion blur is calculating a small animation and then averaging the images together. This averaging of several images can be made with third party programs, such as ImageMagick. (`convert -average frame*.png output.png`)

End of Topic: Go Back to the Table of Contents

### Topic 7

How can I find the size of a text object / center text / justify text?

You can use the `min_extent()` and `max_extent()` functions (new in POV-Ray 3.5) to get the corners of the bounding box of any object. While this is sometimes not the actual size of the object, for text objects this should be fairly accurate, enough to do alignment of the text object.

End of Topic: Go Back to the Table of Contents

### Topic 8

How do make extruded text in POV-Ray?

POV-Ray has true type font support built in that allows you to have 3D text in your scenes (see the documentation about the text object for more details).

There are also some outside utilities that will import true type fonts and allow user manipulation on the text. One of these programs is called Elefont.

End of Topic: Go Back to the Table of Contents

### Topic 9

How do I make an object hollow?

This question usually means How do I make a hollow object, like a waterglass, or a jug?

Before answering that question, let me explain some things about how POV-Ray handles objects.

Although the POV-Ray documentation talks about solid and hollow objects, that's not how it actually works. Solid and hollow are a bit misleading terms to describe the objects. You can also make an object `hollow` with that same keyword, but it's not that simple.

Firstly: POV-Ray only handles surfaces, not solid 3D-objects. When you specify a sphere, it's actually just a spherical surface. It's only a surface and it's not filled by anything. This can easily be seen by putting the camera inside the sphere or by clipping a hole to one side of the sphere with the `clipped_by` keyword (so you can look inside).

People often think that POV-Ray objects are solid, really 3D, with solid material filling the entire object because they make a `difference` CSG object and it seems like the object is actually solid. What the difference CSG actually does is to cut away a part of the object and add a new surface in the place of the hole, which completely covers the hole, so you can't see inside the object (this new surface is actually the part of the second object which is inside the first object). Again, if you move the camera inside the object, you will see that actually it's hollow and the object is just a surface.

So what's all this solid and hollow stuff the documentation talks of, and what's the `hollow` keyword used for?

Although objects are actually surfaces, POV-Ray handles them as if they were solid. For example, fog and media do not go inside solid objects. If you put a glass sphere into the fog, you will see that there's no fog inside the sphere.

If you add the `hollow` keyword to the object, POV-Ray will no longer handle it as solid, so fog and atmosphere will invade the inside of the object. This is the reason why POV-Ray issues a warning when you put the camera inside a non-hollow object (because, as it says, fog and other atmospheric effects may not work as you expected).

If your scene does not use any atmospheric effect (fog or media) there isn't any difference between a solid or hollow object.

So all the objects in POV-Ray are hollow. But the surface of the objects is always infinitely thin, and there's only one surface. With real world hollow objects you have always two surfaces: an outer surface and an inner surface.

Usually people refer to these kind of objects when they ask for hollow objects. This kind of objects are easily achieved with a difference CSG operation, like this:

```
// A simple water glass made with a difference:
difference
{ cone { <0,0,0>,1,<0,5,0>,1.2 }
 cone { <0,.1,0>,.9,<0,5.1,0>,1.1 }
 texture { Glass }
}
```

The first cone limits the outer surface of the glass and the second cone limits the inner surface.

End of Topic: Go Back to the Table of Contents

### Topic 10

How can I fill a glass with water or other objects?

As described in the hollow objects question above, hollow objects have always two surfaces: an outer surface and an inner surface. If we take the same example, a simple glass would be like:

```
// A simple water glass made with a difference:
#declare MyGlass=
difference
{ cone { <0,0,0>,1,<0,5,0>,1.2 }
 cone { <0,.1,0>,.9,<0,5.1,0>,1.1 }
 texture { Glass }
}
```

The first cone limits the outer surface of the glass and the second cone limits the inner surface.

If we want to fill the glass with water, we have to make an object which coincides with the inner surface of the glass. Note that you have to avoid the coincident surfaces problem so you should scale the water object just a little bit smaller than the inner surface of the glass. So we make something like this:

```
#declare MyGlassWithWater=
union
{ object { MyGlass }
 cone
 { <0,.1,0>,.9,<0,5.1,0>,1.1
 scale .999
 texture { Water }
 }
}
```

Now the glass is filled with water. But there's one problem: There's too much water. The glass should be filled only up to certain level, which should be definable. Well, this can be easily made with a CSG operation:

```
#declare MyGlassWithWater=
union
{ object { MyGlass }
 intersection
 { cone { <0,.1,0>,.9,<0,5.1,0>,1.1 }
 plane { y,4 }
 scale .999
 texture { Water }
 }
}
```

Now the water level is at a height of 4 units.

End of Topic: Go Back to the Table of Contents

### Topic 11

How can I bend an object?

There's no direct support for bending in POV-Ray, but you can achieve acceptable bending with the Object Bender by Chris Colefax.

Some objects can be bent by just modelling it with other objects. For example a bent cylinder can be more easily (and accurately) achieved using the intersection of a torus and some limiting objects.

It might be a bit strange why most renderers support bending but POV-Ray doesn't. To understand this one has to know how other renderers (the so-called scanline-renderers work):

In the so-called scanline renders all objects are modelled with triangle meshes (or by primitives such as NURBS or bezier patches which can be very easily converted to triangles). The bending is, in fact, achieved by moving the vertices of the triangles.

In this context the term bending is a bit misleading. Strictly speaking, bending a triangle mesh would also bend the triangles themselves, not only move their vertices. No renderer can do this. (It can be, however, simulated by splitting the triangles into smaller triangles, and so the bending effect is more accurate, although not yet perfect.) What these renderers do is not a true bending in the strict mathematical sense, but only an approximation achieved by moving the vertices of the triangles.

This difference might sound irrelevant, as the result of this kind of fake bending usually looks as good as a true bending. However, it's not irrelevant from the point of view of POV-Ray. This is because POV-Ray does not represent the objects with triangles, but they are true mathematical surfaces. POV-Ray can't fake a bending by moving vertices because there are no vertices to move. In practice bending (and other non-linear transformations) would require the calculation of the intersection of the object surface and a curve (instead of a straight line), which is pretty hard and many times analytically not possible.

Note that isosurface objects can be modified with proper functions in order to achieve all kinds of transformations (linear and non-linear) and thus they are not really bound to this limitation. However, achieving the desired transformation needs some knowledge of mathematics.

See also the variable ior question.

End of Topic: Go Back to the Table of Contents

### Topic 12

The focal blur is very grainy. Can I get rid of the graininess?

Yes. Set `variance` to 0 (or to a very small value, like for example 1/100000) and choose a high enough `blur_samples`. The rendering will probably slow down quite a lot, but the result should be very good.

End of Topic: Go Back to the Table of Contents

### Topic 13

Is blurred reflection possible in POV-Ray

In the unofficial POV-Ray patch called MegaPov there is a feature which allows calculating blurred reflection (which works by shooting many reflected rays and averaging the result instead of shooting just one, as POV-Ray does by default). People were a bit disappointed when this feature was not included in POV-Ray 3.5.

However, is there any way to get blurred reflection in 3.5? Perhaps a bit surprisingly, the answer is yes. You can get blurred reflection which works pretty much like the one in MegaPov, and which can even look better and be a bit faster to render.

The trick is to use averaged textures with differing normals.

When POV-Ray calculates the color of an averaged texture map, it has to calculate the color of each texture individually before it can average them. If the textures are reflective, it has to shoot a reflected ray for each texture in order to get its color. The trick exploits this behaviour in order to achieve blurred reflection by shooting many reflected rays: The idea is to create many identical textures but with slightly differing normals and then average them, which causes POV-Ray to shoot a reflected ray for each one of them.

So you can implement this trick like this:

```
#declare BlurAmount = .2; // Amount of blurring
#declare BlurSamples = 40; // How many rays to shoot

object
{ MyObject
 texture
 { average texture_map
 { #declare Ind = 0;
 #declare S = seed(0);
 #while(Ind < BlurSamples)
 [1 // The pigment of the object:
 pigment { ObjectPigment }
 // The surface finish:
 finish { ObjectFinish }
 // This is the actual trick:
 normal
 { bumps BlurAmount
 translate <rand(S),rand(S),rand(S)>*100
 scale 1000
 }
 ]
 #declare Ind = Ind+1;
 #end
 }
 }
}
```

There are basically two ways of using this trick: scaling the normals very big (as in the example above) or scaling them very small (ie. with something like `scale .001`). These two give slightly different results with their own advantages and disadvantages:

- Scaling very big will usually produce much smoother results, but if the amount of blurriness (`BlurAmount`) is high it often requires quite many samples to avoid banding artifacts. It works best when the blur amount is small. Another disadvantage is that it may sometimes be rather slow when using heavy antialiasing.

- Scaling very small will usually produce a rather grainy result which may or may not look good. The advantage of this is that a heavy antialiasing will make the blurriness look better. If you are going to use extreme antialiasing settings for your final rendering, you can often lower the blur samples amount for extra speed and still get a good-looking result.

Also note that this exact same trick can be used to get blurred refraction. It's also possible to get uneven blur, eg. the reflection is blurred more in the x-axis direction than in the other directions (this is achieved by scaling the normals unevenly), and countless variations of this (eg. the amount and direction of the blur may vary throughout the surface of the object) which makes this trick even more powerful than the reflection blur in MegaPov.

If you also want to use a normal modifier in the object in addition to the blurred reflection, you can add it to the `normal` block as an averaged normal map or similar, for example like this:

```
 normal
 { average normal_map
 { [1 bumps BlurAmount
 translate <rand(S),rand(S),rand(S)>*100
 scale 1000
 ]
 [1 MyNormal]
 }
 }
```

Note: this will diminish the amount of blur so `BlurAmount` will have to be doubled. Also the depth of `MyNormal` should be double from what it normally would be.

End of Topic: Go Back to the Table of Contents

### Topic 14

I have really thin lines or very small details and I'm getting jagged lines and heavy moire patterns no matter what antialiasing settings I use. Is there any way of getting the best possible antialiasing, no matter how long it takes to render?

You can achieve a very high-quality antialiasing by using the antialiasing settings `+a0.0 +am2`. To increase the quality even further, you can increase the value of the `+r` parameter from its default value (which is 3), for example `+r4` (or higher).

Note: that this can take really long to render because it makes POV-Ray to calculate antialiasing for every pixel in the image. You can speed up the rendering by giving the `+a` parameter a value larger than `0.0`, but any such value will cause jagginess/moire effects if you have lots of really small sub-pixel-sized details in your image. (Usually images don't contain significant amounts of such details and that's why a larger threshold value is enough for most images.)

End of Topic: Go Back to the Table of Contents

### Topic 15

If I use an image map with a cylindrical map type (map_type 2) the image is used only once around the cylinder. Is there any way to repeat the image several times around it instead of just once?

It is indeed only possible to put the image once around a cylinder when using `map_type 2`.

This is a typical example:

```
cylinder
{ -y, y, 1
 pigment
 { image_map { jpeg "myimage" map_type 2 }
 translate -y*.5
 scale 2
 }
}
```

The cylinder is wrapped with the image only once. There is a way to circumvent this limitation, though, and it's by using the cylindrical warp transformation. The idea is to use default planar mapping in the image map, scale it down in the proper axis and then apply a cylindrical warp to the pigment, like this:

```
cylinder
{ -y, y, 1
 pigment
 { image_map { jpeg "myimage" }

 // The trick:
 scale <1/8, 1, 1> // repeat 8 times
 warp { cylindrical }

 translate -y*.5
 scale 2
 }
}
```

This same trick can be used to apply the image only to a part of the cylinder instead of wrapping it around it completely. To achieve this, just add the keyword `once` inside the `image_map` block. The parts not covered by the image will be transparent (this is very useful when using layered textures, with the image map on top of another texture).

Note: the cylindrical warp can be used to apply any pigment (or texture) around the cylinder, not just image maps. This is sometimes quite useful when wanting to apply something to the surface of a cylinder, such as a text pattern, like this:

```
camera { location -z*6 look_at 0 angle 35 }
light_source { <10, 20, -50>, 1 }

cylinder
{ -y, y, 1
 pigment
 { object
 { text { ttf "crystal", "Hello", 1.1, 0 }
 // Note: Thickness must be larger than cylinder radius
 rgb 1, rgb x
 }
 scale <1/8, 1, 1>
 warp { cylindrical }
 translate -y*.2
 }
 rotate y*145
}

plane { y, -1.001 pigment { checker rgb 1, rgb .5 } }
```

End of Section: Go Back to the Table of Contents

### Topic 16

When I put several transparent objects one in front of another or inside another, POV-Ray calculates a few of them, but the rest are completely black, no matter what transparency values I give.

Short answer: Try increasing the `max_trace_level` value in the `global_settings` block (the default is 5).

Long answer:

Raytracing has a peculiar feature: It can calculate reflection and refraction. Each time a ray hits the surface of an object, the program looks if this surface is reflective and/or refractive. If so, it shoots another ray from this point to the appropriate direction.

Now, imagine we have a glass sphere. Glass reflects and refracts, so when the ray hits the sphere, two additional rays are calculated, one outside the sphere (for the reflection) and one inside (for the refraction). Now the inside ray will hit the sphere again, so two new rays are calculated, and so on and so on...

You can easily see that there must be a maximum number of reflections/refractions calculated, because otherwise POV-Ray would calculate that one pixel forever.

This number can be set with the `max_trace_level` option in the `global_settings` block. The default value is 5, which is enough for most scenes. Sometimes it isn't enough (specially when there are lots of semitransparent objects one over another) so you have to increase it.

So try something like:

```
global_settings
{
 max_trace_level 10
}
```

End of Topic: Go Back to the Table of Contents

### Topic 17

When I make an image with POV-Ray, it seems to use just a few colors since I get color banding or concentric circles of colors or whatever where it shouldn't. How can I make POV-Ray to use more colors?

POV-Ray always writes true color images (ie. with 16777216 colors, ie. 256 shades of red, 256 shades of green and 256 shades of blue) (this can be changed when outputting to PNG or to B/W TGA but this is irrelevant when answering to this question).

So POV-Ray is not guilty. It always uses the maximum color resolution available in the target image file format.

This problem usually happens when you are using windows with 16-bit colors (ie. only 65536 colors, the so-called hicolor mode) and open the image created by POV-Ray with a program which doesn't dither the image. The image is still true color, but the program is unable to show all the colors, but shows only 65536 of them (dithering is a method that fakes more colors by mixing pixels of two adjacent colors to simulate the in-between colors).

So the problem is not in POV-Ray, but in your image viewer program. Even if POV-Ray shows a poor image while rendering because you have a resolution with too few colors, the image file created will have full color range.

End of Topic: Go Back to the Table of Contents

### Topic 18

When I rotate an object, it dissapears from the image or moves very strangely. Why?

You need to understand how rotation works in POV-Ray.

Objects are always rotated around the axes. When you rotate, for example, `<20,0,0>`, that means that you are rotating around the X-axis 20 degrees (counter-clockwise). This is independent of the location of the object: It always rotates around the axis (what's the center of the object anyways? How do you locate it?). This means that if the object is not centered in the axis, it will orbit this axis like the Moon orbits the Earth (showing always the same side to the Earth).

It's a very good practice to define all objects centered at the origin (ie. its center is located at `<0,0,0>`). Then you can rotate it arbitrarily. After this you can translate it to its proper location in the scene. It's a good idea to do this to every object even if you don't rotate it (because you can never say if you will rotate it some day nevertheless).

What if, after all, you have a very complex object defined, but its center is not at the origin, and you want to rotate it around its center? Then you can just translate it to the origin, rotate it and then translate it back to its place. Suppose that the center of the object is located at `<10,20,-30>`; you can rotate it this way:

```
translate -<10,20,-30>
rotate <whatever>
translate <10,20,-30>
```

End of Topic: Go Back to the Table of Contents

### Topic 19

If I tell POV-Ray to render a square image or otherwise change the aspect ratio, the output image is distorted. What am I doing wrong?

The problem is that the camera is set to an aspect ratio of 4/3, while the picture you are trying to render has a different aspect ratio from that (like 1/1 for a square image).

You can set the aspect ratio with the `right` keyword in the camera block. For example, suppose you want to render an image at a resolution of 1024x400 (and assuming square pixels). You can change the aspect ratio of the camera like this:

```
camera
{ right x*1024/400
 (other camera settings...)
}
```

This keyword can also be used to change the handedness of POV-Ray (see the question about Moray and POV-Ray handedness for more details).

Note: One could think "Why doesn't POV-Ray always set automatically the aspect ratio of the camera according to the resolution of the image?".

It is actually not a good idea to automatically set the aspect ratio of the camera (assuming square pixels) for reasons described below. Note, however, that it would be possible to automatize this using the `image_width` and `image_height` keywords, like this:

```
camera
{ right x*image_width/image_height // Not recommended, read below
 (other camera settings...)
}
```

While this may sound very tempting, and in many cases it would indeed be a handy solution, in general this is not recommended. There are several reasons for this:

- If the aspect ratio of the image is adjusted automatically like this, it means that if someone renders the image with a resolution having a different aspect ratio than what the author intended, the resulting image will be either cropped or parts of the scene not seen in the original will come into view. In many cases this is a bad thing because it affects (usually negatively) the composition of the scene: Important parts of the scene may get cropped out of view, or parts of the scene not intended to be seen (eg. because of lack of modelling) suddenly come into view. If someone does this he might often not even notice that there's something wrong with the image.

- If the viewing angle is adjusted with the `angle` keyword (as is rather usual), it introduces a rather unwanted effect when used in addition to the automatic aspect ratio setting with `right`: The aspect ratio of the image will in this case always be done in the vertical direction, either by cropping or extending the image in the upper and lower sides. This means that if you try to eg. render a widescreen version of the scene, you will only end up cropping the image from the upper and lower sides (instead of adding new scenery to the left and right sides). More often than not, scenes are designed to be rather panoramic in nature, ie. the image can be expanded horizontally. However using the automatic aspect ratio correction along with a fixed `angle` setting will not achieve this.

- The automatic aspect ratio correction given above assumes that pixels are square. There are cases where one wants to render an image for a resolution with non-square pixels (examples include the Windows98 startup screen, which uses a resolution of 320x400, and a DVD movie, where the video has always an aspect ratio of 1.78:1 although it can then be stretched to the intended aspect ratio, eg. 2.35:1 by the player). If the automatic aspect ratio correction is used, it would not be possible to render an image with non-square pixels without modifying the scene.

- The `image_width` and `image_height` keywords break the boundary between the frontend and the backend. What this means in practice is that there's a high probability that they will be deprecated in the future, thus heavily relying on their use may not be a good idea.

Using a fixed aspect ratio setting like `right x*1024/400` will make sure that the composition of the image is unchanged and that the image can be rendered with non-square pixels. If someone renders the image with a different aspect ratio than what was intended, there's a much higher chance for him to see that there's something wrong with the rendering settings than if the image would simply get cropped or extended vertically.

End of Topic: Go Back to the Table of Contents

### Topic 20

Why are there strange dark pixels or noise on my CSG object?

This is the typical coincident surfaces problem. This happens when two surfaces are exactly at the same place. For example:

```
union
{ box { <-1,0,-1>,<1,-2,1> texture { Texture1 } }
 box { <-2,0,-2>,<2,-1,2> texture { Texture2 } }
}
```

The top surface of the first box is coincident with the top surface of the second box. When a ray hits this area, POV-Ray has to decide which surface is closest. It can't, since they are exactly in the same place. Which one it actually chooses depends on the float number calculations, rounding error, initial parameters, position of the camera, etc, and varies from pixel to pixel, causing those seemingly random pixels.

One solution to the problem is to decide which surface you want to be on top and translate that surface just a bit, so it extends slightly past the unwanted surface. For example, if the second box should be the top surface:

```
union
{ box { <-1,0,-1>,<1,-2,1> texture { Texture1 } }
 box { <-2,0.001,-2>,<2,-1,2> texture { Texture2 } }
}
```

Scaling an object by a small amount using either `scale 1.0001` or `scale 0.9999` also works in certain situations.

Note: A similar problem appears when a light source is exactly on a surface: POV-Ray can't calculate accurately if it's actually inside or outside the surface, so dark (shadowed) pixels appear on every surface that is illuminated by this light.

End of Topic: Go Back to the Table of Contents

### Topic 21

Why won't the textures in stars.inc work with my sky_sphere?

The only thing that works with a `sky_sphere` is pigments. Textures and finishes are not allowed. Don't be discouraged though because you can still use the textures in stars.inc with the following method:

Extract only the pigment statement from the declared textures. For example:

```
texture
{ pigment
 { color_map { [0 rgb ...][.5 rgb ...][1.0 rgb ...] }
 scale ...
 }
 finish { ... }
}
```

becomes:

```
pigment
{ color_map { [0 rgb ...][.5 rgb ...][1.0 rgb ...] }
 scale ...
}
```

The reason for this is that `sky_sphere` doesn't have a surface, it isn't an actual object. It is really just a fancy version of the background feature which extracts a color from a pigment instead of being a flat color. Because of this, normal and finish features, which depend on the characteristics of the surface of an object for their calculations, can't be used. The textures in `stars.inc` were intended to be mapped onto a real sphere, and can be used something like this:

```
sphere
{ 0, 1
 hollow // So it doesn't interfere with any media
 texture { YourSkyTexture }
 scale 100000
}
```

End of Topic: Go Back to the Table of Contents

### Topic 22

When I use filter or transmit with my .tga image map nothing happens

POV-Ray can only apply filter or transmit to 8 bit 256 color palleted images. Since most `.tga`, `.png`, and `.bmp` images are 24bit and 16 million colors they do not work with filter or transmit. If you must use filter or transmit with your image maps you must reduce the color depth to a format the supports 256 colors such as the `.gif` image format.

You might also check the POV-Ray docs on using the alpha channel of `.png` files if you need specific areas that are transparent.

End of Topic: Go Back to the Table of Contents

### Topic 23

My isosurface is not rendering properly: there are holes or random noise or big parts or even the whole isosurface just disappears.

The most common reason for these type of phenomena with isosurfaces is a too low `max_gradient` value. Use `evaluate` to make POV-Ray calculate a proper `max_gradient` for the isosurface (remember to specify a sensible `max_gradient` even when you use `evaluate` or else the result may not be correct).

Sometimes a too high `accuracy` value can also cause problems even when the `max_gradient` is ok. If playing with the latter doesn't seem to help, try also lowering the `accuracy`.

Remember that specifying a `max_gradient` which is too high for an isosurface, although it gives the correct result, is needlessly slow, so you should always calculate the proper `max_gradient` for each isosurface you make.

Note: that there are certain pathological functions where no `max_gradient` or `accuracy` will help. These functions usually have discontinuities or similar ill-behaving properties. With those you just have to find a solution which gives the best quality/speed tradeoff. Isosurfaces work best with functions which give smooth surfaces.

End of Topic: Go Back to the Table of Contents

### Topic 24

When I scale something very large/small or locate something at very far distances the objects dissapear. What is causing this?

POV-Ray uses internally 64-bit floating point numbers for almost all of its calculations. Floating point numbers are a good and flexible way of using non-integer numbers, and since they are directly supported by hardware, they are extremely fast.

The good thing about floating point numbers is that it's possible to represent very large and very small numbers, with quite many decimals. For example the maximum value representable with a 64-bit integer is about 1019, while the maximum value of a 64-bit floating point number is about 10308 (and naturally with integers you can only represent integer numbers, but with floating point numbers you can represent decimal values; for example the smallest positive number closest to 0 which can be represented is something like 10-308).

But the bad news: As is logical, floating point numbers can't magically increase the total amount of different numbers representable with a 64-bit value (you can represent 264 different values, period). Floating point numbers increase the maximum value scales, but this is done at the cost of accuracy. That is, when you begin approaching the extreme values, the accuracy of the number decreases (ie. the values will make larger and larger jumps).

After this (longish) introduction, the answer: Floating point numbers are inaccurate near the extreme value ranges, which can potentially cause rendering artifacts. To avoid going to these extremes, some limits have been imposed for very large and small scales.

In some cases these artificial limits might appear way too restrictive (after all, 64-bit floating point numbers can often represent much larger numbers with good-enough accuracy). However, even with the current limits it's still possible to reach the accuracy limits of floating point numbers. For example, this simple scene shows what happens when these limits are reached (the sphere is full of lighting artifacts caused by floating point number inaccuracies):

```
camera { location -z*1e5 look_at 0 angle .002 }
light_source { <10,20,-30>, 1 }
sphere { 0,1 pigment { rgb 1 } }
```

This is not a bug in POV-Ray. This is just a side-effect of floating point number inaccuracy. There's no fix for this (the only fix would be using floating point numbers with more bits, eg. 128 bits, but this is not possible with current hardware).

End of Topic: Go Back to the Table of Contents

### Topic 25

Sometimes when I use radiosity, I get big round artifacts in corners. What's wrong?

``

| The image at the right is part of a larger image using radiosity, which shows the radiosity artifact in question (indicated by the red arrow). This is actually quite a tricky problem with the radiosity algorithm of POV-Ray. In short, the radiosity algorithm works like this: At certain intervals in a surface (the interval size being calculated using an algorithm which parameters can be fine-tuned in the radiosity block of the scene) POV-Ray will take radiosity samples. These samples are taken by shooting rays at all directions away from the surface in question (ie. in the directions of an imaginary half-sphere on that surface). Sometimes this sampling point happens to be located just at the junction of two surfaces, like for example the inner corner of a room like in the image. What happens is that some of the rays shot from this point will actually miss the other surface and go outside the room. (Since the coloration of the outside is very different from the inside, usually eg. black, this will have a strong effect on the lighting of that point.) The reason why this produces a large wrongly-colored circle instead of a tiny point is because of the interpolation algorithm the radiosity engine uses (ie. instead of sampling at each point in the surface it only samples at certain intervals and interpolates the rest for speed). | |

In a way, this problem is very similar to the coincident surfaces problem: When the sampling point is exactly at the junction of two surfaces, POV-Ray has no way of knowing which side is the inside which should be sampled. (A similar problem happens if you put eg. a light source in such a junction point.)

Unlike the coincident surfaces/light sources problem, this radiosity version of the problem has no trivial solution. This is because the location of the sampling points are calculated automatically and it's not possible to manually modify one of them to avoid the problem. It's also very difficult to enhance the algorithm itself to detect such situations.

Note: that not all radiosity artifacts are caused by this phenomenon. This artifact has the characteristic of usually being a clear, lone circle at the junction of two surfaces.

One simple trick which is worth trying and which often solves the problem is to move the camera a very tiny amount to some direction. This usually makes POV-Ray to take the radiosity samples also shifted by the tiny amount and if we are lucky the problem will not happen.

A more elaborated kludge (which can't be applied in all cases, though), is to make the problematic wall (in this example the wall at the right) shadowless and put a copy of the wall behind it (within a small distance). This way the sample rays leaving the room will hit this second wall and get correct illumination.

A third solution is to avoid sharp angles altogether: if corners are slightly rounded (with eg. a section of an inverted cylinder), the artifact should not happen. (Of course this starts requiring modelling skills, specially if the surfaces are not at 90 degrees of each other...)

End of Section: Go Back to the Table of Contents

### Topic 26

How do I turn animation on? I have used the clock variable in my scene, but POV-Ray still only calculates one frame

The easiest way is to just specify the appropriate command line parameter on the command line or in the command line field in the rendering settings menu (in the Windows version). For example, if you want to create 20 frames, type this: `+kff20`

This will create 20 frames with the `clock` variable going from 0 to 1. The other command line parameters are found in the POV-Ray documentation.

Ken Tyler has also another good solution for this:

In the directory that you installed POV-Ray into you will find a subdirectory called scenes and another inside that called animate. You will find several example files showing you how to write your scene to use the clock variable. You will still need to activate POV-Ray's animation feature by using an `.ini` file with the correct info or with command line switches. I personaly like to use the ini file method. If you try this open the master `povray.ini` file from the tools menu and add the following lines:

```
;clock=1
;Initial_Frame=1
;Final_Frame=20
;Cyclic_Animation = on
;Subset_Start_Frame=6
;Subset_End_Frame=9
```

Save the file and close it. When you need to use the animation feature simply go in and edit the `povray.ini` file and uncomment out the functions you want to use. At a minimum you will need to use the `initial_frame` and `final_frame` option to make it work. Once you have stopped rendering your series of frames be sure to comment out the clock variables in the ini file. After you have rendered a series of individual frames you will still need to compile them into the animation format that you wish to use such as AVI or MPEG. See my links pages for programs that can help you do this. POV-Ray has no internal ability to do this for you except on the Macintosh platform of the program.

The Mac version normally doesn't use `.ini` files and lacks any command line, but uses a completely graphical interface instead. To activate animation, choose the render settings item from the Edit menu (right under Preferences, it will be titled FILENAME Settings, where FILENAME is the name of your file), click on the Animation tab, and enter the needed information in the text boxes.

End of Topic: Go Back to the Table of Contents

### Topic 27

What is Photon Mapping?

Photon mapping uses forward raytracing (ie. sending rays from light sources) calculate reflecting and refracting light (aka. caustics), which is a new feature in POV-Ray 3.5.

The following is from the homepage of the developer:

My latest fun addition to POV is the photon map. The basic goal of this implementation of the photon map is to render true reflective and refractive caustics. The photon map was first introduced by Henrik Wann Jensen. It is a way to store light information gathered from a backwards ray-tracing [sic] step in a data structure independent from the geometry of a scene.

It is surprisingly fast and efficient. How is this possible when forward raytracing is so inefficient? For several reasons:

- Photon mapping is only used to calculate illumination, ie. lighting values, not to render the actual scene. Lighting values do not have to be as accurate as the actual rendering (it doesn't matter if your reflected light bleeds a bit out of range; actually this kind of bleeding happens in reality as well (due to light diffusing from air), so the result is not unrealistic at all).

- Photon mapping is calculated only for those (user-specified) objects that need it (ie. objects that have reflection and/or refraction).

- The rays are not shot to all directions, towards the entire scene, but only towards those specified objects. Many rays are indeed shot in vain, without them affecting the final image in any way, but since the total amountof rays shot is relatively small, the rendering time doesn't get inacceptably longer.

- The final image itself is rendered with regular backwards raytracing (the photon mapping is a precalculation step done before the actual rendering). The raytracer doesn't need to use forward raytracing in this process (it just uses the precalculated lighting values which are stored in space).

As you have seen, for the photon mapping to work in an acceptable way, you have to tell the program which objects you want to reflect/refract light and which you don't. This way you can optimize a lot the photon mapping step.

End of Topic: Go Back to the Table of Contents

### Topic 28

Can POV-Ray use multiple processors?

The new POV-Ray 3.7 supports multiple processors. It uses available processors automatically so you don't have to explicitly turn the feature on.

Background:

Previously there was an explanation here about why supporting multiple processors is very difficult in a raytracer like POV-Ray. The problems were indeed many, and a great amount of work has been put into redesigning the internal structure of POV-Ray to support parallel rendering. Especially the radiosity algorithm has been greatly reworked in order to make it possible to render in parallel (but much of the work has been put into internal code structuring not visible to the end user).

You might have heard of parallel processing patches for pov3.6 and earlier. The problem with these patches is that they basically just start several copies of POV-Ray, giving them different parts of the image to render. This is problematic because the amount of memory needed to render the scene increases directly in relation to the amount of parallel processes. This means that if you eg. render with two processors using such patch, the amount of memory needed to render the scene gets doubled. The new pov3.7, however, is able to render in parallel using as many processors as desired without any significant increase in memory
usage. This means that if a scene requires 1 gigabyte of memory to render, rendering the scene using 8 processors still requires just 1 gigabyte of memory (with the old third-party patches it would have required 8 gigabytes).

End of Topic: Go Back to the Table of Contents

### Topic 29

Is there a way to generate a wireframe output image from a POV scene file?

Short answer: No.

Long answer:

You have to understand the difference between a modeller like 3D-Studio and POV-Ray in the way they handle objects. Those modellers always use triangle meshes (and some modellers use also NURBS which can be very easily converted into triangles). Triangle meshes are extremely simple to represent in a wireframe format: Just draw a line for each triangle side.

However, POV-Ray handles most of the objects as mathematical entities, not triangle meshes. When you tell POV-Ray to create a sphere, POV-Ray only handles it as a point and a radius, nothing else (besides the possible matrix transform applied to it). POV-Ray only has a notion of the shape of the object as a mathematical formula (it can calculate the intersection of a line and the sphere).

For wireframe output there should be a way to convert that mathematical representation of the object into actual triangles. This is called tesselation.

For some mathematical objects, like the sphere, the box, etc, tesselation is quite trivial. For other entities, like CSG difference, intersection, etc, it's more difficult (although not impossible). For other entities it's completely impossible: infinite non-flat surfaces like paraboloids and hyperboloids (well, actually it is possible if you limit the size of the surface to a finite shape; still the amount of triangles that needs to be created would be extremely high).

There have been lots of discussions about incorporating tesselation into POV-Ray. But since POV-Ray is just a renderer, not a modeller, it doesn't seem to be worth the efforts (adding tesselation to all the primitives and CSG would be a huge job).

(Of course tesselation could give some other advantages, like the ability to fake non-uniform transformations to objects like most triangle mesh modellers do...)

If you just want fast previews of the image, you can try to use the quality parameter of POV-Ray. For example setting quality to 0 (+q0) can give a very fast render. See also the rendering speed question.

End of Topic: Go Back to the Table of Contents

### Topic 30

Can I specify variable IOR for an object? Is there any patch that can do this? Is it possible?

Short answer: No.

Long answer:

There are basically two ways of defining variable IOR for an object: IOR changing on the surface of the object and IOR changing throughout inside the object.

The first one is physically incorrect. For uniform IOR it simulates physical IOR quite correctly since for objects with uniform density the light bends at the surface of the object and nowhere else. However if the density of the object is not uniform but changes throughout its volume, the light will bend inside the object, while travelling through it, not only on the surface of the object.

This is why variable IOR on the surface of the object is incorrect and the possibility of making this was removed in POV-Ray 3.1.

From this we can deduce that a constant IOR is kind of property of the surface of the object while variable IOR is a property of the interior of the object (like media in POV-Ray). Of course the physically correct interpretation of this phenomenon is that IOR is always a property of the whole object (ie. its interior), not only its surface (and this is why IOR is now a property of the interior of the object in POV-Ray); however, the effect of a constant IOR has effect only at the surface of the object and this is what POV-Ray does when bending the rays.

The correct simulation for variable IOR, thus, would be to bend the ray inside the object depending on the density of the interior of the object at each point.

This is much harder to do than one may think. The reasons are similar to why non-uniform transformations are too difficult to calculate reasonably (as far as I know there exists no renderer that calculates true non-uniform transformations; mesh modellers just move the vertices, they don't actually transform the object; a true non-uniform transformation would bend the triangles). Moreover: Non-uniform transformations can be faked if the object is made of many polygons (you can move the vertices as most mesh modellers do), but you can't fake a variable IOR in this way.

Variable IOR is (mostly) impossible to calculate analytically (ie. in a mathematically exact way) at least in a reasonable time. The only way would be to calculate it numerically (usually by super-sampling).

Media in POV-Ray works in this way. It doesn't even try to analytically solve the color of the media, but supersamples the media along the ray and averages the result. This can be pretty inaccurate as we can see with the media method 1 (the only one which was supported in POV-Ray 3.1). However some tricks can be used to make the result more accurate without having to spend too much time, for example antialiasing (which is used by the media method 3 in POV-Ray 3.5). This is a quite easy calculation because the ray is straight, POV-Ray knows the start and end points of the ray and it knows that it doesn't intersect with anything along the ray (so it doesn't have to make ray-object intersection calculations while supersampling).

Variable IOR is, however, a completely different story. Here the program would have to shoot a LOT of rays along the path of the bending light ray. For each ray it would have to make all the regular ray-object intersection calculations. It's like having hundreds or thousands of transparent objects one inside another (with max_trace_level set so high that the ray will go through all of them). You can easily test how slow this is. It's VERY slow.

One could think that Hey, why not just shoot a few tens of rays and then use some kind of antialiasing to get the fine details, like in media method 3.

Well, it might work (I have never seen it tested), but I don't think it will help much. The problem is the inaccuracy of the supersampling (even when using antialiasing). In media it's not a big problem; if a very small shadowed area in the media is not detected by the supersampling process, the result will not differ very much from the correct one (since the shadowed area was so small it would have diminished the brightness of that ray just a bit but no more) and it will probably still look good.

With IOR this isn't anymore true. With IOR even very, very small areas may have very strong effect in the end result, since IOR can drastically change the direction of the ray thus making the result completely different (even very small changes can have great effect if the object behind the current refracting object is far away).

This can have disastrous effects. The ior may change drastically from pixel to pixel almost at random, not to talk from frame to frame in an animation. To get a more or less accurate result lots of rays would be needed; just a few rays is not enough. And shooting lots of rays is an extremely slow process.

End of Topic: Go Back to the Table of Contents

### Topic 31

Do I need inside_vector to use interior or media in a mesh?

Short answer: No. `inside_vector` and `interior` are completely unrelated. `inside_vector` is used only for CSG calculations and nothing else. You don't need to use it for defining an `interior` (eg. containing media) to a mesh. The `interior` block works for any closed surface regardless of its solidness.

Long answer:

The features of the `interior` property of an object (eg. media, fading, etc) do not need to determine whether a point is inside the object or not. They are solely based on intersection pairs (that is, `interior` properties are calculated for each ray segment which intersects the object in two places). For this reason eg. media works just ok for closed meshes and bicubic patches without any additional tweaking. In other words, `inside_vector` is not needed for `interior` to work.

CSG, however, is a different issue. Some CSG calculations require solving whether a given point is inside an object or not, and this cannot be solved by default for meshes. However, this is where the `inside_vector` kicks in.

The vector given as parameter to this keyword defines a direction. In theory any (non-zero) vector is ok. What POV-Ray does is that when it needs to know whether a certain point is inside the mesh or not, it shoots a ray from this point to the direction defined by this vector and then counts how many triangles of the mesh the ray hits. If the ray hits an odd number of triangles, POV-Ray assumes the point is inside the mesh, else outside.

In theory it wouldn't be necessary for the user to give a specific vector. POV-Ray could just internally create some random vector. However, the reason why the user is given the option of defining the vector himself is that this algorithm might not be absolutely flawless in all cases. For example, if the vector happens to have the exact same direction as the tangent of a triangle, or if the rays happen to hit lots of edges of the mesh, the CSG operation might suffer from some jittering at some places due to the limited accuracy of floating point numbers (not asimilar to what happens with the infamous coincident-surfaces problem). If this would happen, changing the direction of the `inside_vector` a bit may well fix the problem.

End of Section: Go Back to the Table of Contents

-scanned for dead links StephenS 01:12, 28 April 2010 (UTC)

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

## HowTo Articles

### Build a basic scene

Sometimes it's helpful to have a sort of "ruler" to gauge how far things are apart and if you're close to the "floor" and have a checker pattern you can scale the checkers as '10' on a square but if you're up in the air and don't have the floor for a reference you can use an object you build yourself to gauge the distance. Here's how to build a simple ruler:

```
// ruller.inc as of 07Feb2009 // yeah, I know I've misspelled ruler. It's a way to prevent name collision.

#ifndef( Ruller_Inc_Temp)
#declare Ruller_Inc_Temp = version;

#include "colors.inc" // comment this out when you're through with it
#declare DisplayFont = "arial.ttf"

#declare MyRuller = text {
 ttf // font type (only TrueType format for now)
 DisplayFont,
 "0.....|....20....|......40.....|....60.....|.....80.....|.....100", // the string to create
 1, // the extrusion depth
 0 // inter-character spacing
 //rotate 90*y
 scale <5,5,1>
 translate <0,2,0>
 texture{pigment{Light_Purple}}
}
#end
```

Your ruler will display aligned along the x-axis and you can rotate / translate it to where you need it.

You can calibrate the ruler by comparing it to the checkered floor and adjust by adding or removing '.'s between the numbers. I use it when I'm building a scene.

Alternately, you might need a quick reference to which axis is where and I've built an axis indicator to use myself. Here it is:

```
// origin axis display this file is axis.inc as of 07Feb2009

#ifndef( ORIGIN_Inc_Temp)
#declare ORIGIN_Inc_Temp = version;

#include "colors.inc"

#declare DisplayFont = "arial.ttf"

// let's label the origin of the scene with a black sphere
#declare MyOrigin = object {
 sphere {
 <0, 0, 0> // center of sphere <X Y Z>
 1.0 // radius of sphere
 texture{
 pigment{Black}
 }
 }
}
//---------------------------------------------
// X-axis pointer:
#declare MyXPointer = union {
cylinder {
 0*x, 10*x, 1
 open
 texture{
 pigment{ Red }
 }
}
cone {
 10*x, 1.0,
 11*x, 0.0
 texture{pigment{ Red}}
}

text {
 ttf // font type (only TrueType format for now)
 DisplayFont,
 "X", // the string to create
 1, // the extrusion depth
 0 // inter-character spacing
 //rotate 90*y
 scale <5,5,1>
 translate <11,2,0>
 texture{pigment{Red}}
}
}
//------------------------------------------------
// y-axis pointer:
#declare MyYPointer =
union {
cylinder {
 0*y, 10*y, 1
 open
 texture{
 pigment{ White }
 }
}
cone {
 10*y, 1.0,
 11*y, 0.0
 texture{pigment{ White}}
}

text {
 ttf // font type (only TrueType format for now)
 DisplayFont,
 "Y", // the string to create
 1, // the extrusion depth
 0 // inter-character spacing
 //rotate 90*y
 scale <5,5,1>
 translate <5,9,5>
 texture{pigment{White}}

}
}

//------------------------------------------------
// z-axis pointer:
#declare MyZPointer =
union {
cylinder {
 0*z, 10*z, 1
 open
 texture{
 pigment{ Blue }
 }
}
cone {
 10*z, 1.0,
 11*z, 0.0
 texture{pigment{ Blue}}
}

text {
 ttf // font type (only TrueType format for now)
 DisplayFont,
 "Z", // the string to create
 1, // the extrusion depth
 0 // inter-character spacing
 //rotate 90*y
 scale <5,5,1>
 translate <2,5,11>
 texture{pigment{Blue}}

}
}
#declare MyAxisDisplay =
union {
 object {MyOrigin }
 object {MyXPointer}
 object {MyYPointer}
 object {MyZPointer}
}

#end
```

You build an axis display by declaring : object { MyAxisDisplay [modifiers go here] }

Also helpful is a blueprint or 3-views to guide your work. You can put an image_map on a simple box to show you where the various components go like this:

```
object {
 box{
 <0,0,0>,<1,1,1>
 pigment{
 image_map{ gif "side view.gif"
 }
 }
 scale<940,300,0.1>
 no_shadow

 }

}
```

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Compile POV-Ray 2.2

Notice: This guide was tested with Debian 12 & FreeBSD 13.2, exact instructions may vary depending on your operating system. However, if you have even a basic understanding of software compilation you should be able to follow along without issue.

## Contents

- 1 Preparation

- 2 Build setup

- 3 Fixing random number generation

- 4 Enabling extra optimization

- 5 Compiling POV-Ray 2.2

- 6 Rendering a scene

- 7 External links

## Preparation

Before we can build POV-Ray we need to sort out some prerequisites. First of all grab the source code from POV-Ray's website: https://www.povray.org/ftp/pub/povray/Old-Versions/Official-2.2/

The files we need are:

| Linux, BSD | Windows |
| POVDOC.TAR.Z | POVDOC.ZIP |
| POVSCN.TAR.Z | POVSCN.ZIP |
| POVSRC.TAR.Z | POVSRC.ZIP |

These archives will provide us with documentation, example scenes and the program sources, extract them to a destination of your choosing.

Now that we have the code, we need something to compile it with, on Debian GNU/Linux and derivatives (for example Ubuntu, Linux Mint, Pop!_OS) this is as easy as installing the build-essential package. To do that you can run the following command in a terminal (make sure you have root privileges):

```
apt install build-essential
```

This will pull in the GNU Compiler Collection (GCC) & GNU Make (BSD users should install and use gmake for this guide), our compiler and build tool respectively. With that we have everything we need.

## Build setup

POV-Ray 2.2 can't be compiled straight away, this is because some important files aren't yet where they should be. If you navigate to the place where you extracted POVSRC you will see two folders (Windows users note that the ZIP archives contain upper case filenames), machine & source. machine has more folders with system specific source files covering various platforms of the time period, whereas source contains the actual source code for POV-Ray, we need to copy the correct machine files to source in order to make our executable.

Inside machine you will see a folder called unix in there you will find the files we need. Copy all of the files from unix (but not the unix directory itself) to the source folder from before (note that despite being for UNIX-compatible systems, these files will work for Windows users too, as long as you use the MinGW development environment when compiling). With the system files in place we need to rename the file unixconf.h to config.h or the build tool won't find it, so do that now.

It's now possible for us to build POV-Ray 2.2. However, before we do that we're going to make a couple of changes to the source code, one of which will make sure that we don't have a certain issue later on.

## Fixing random number generation

POV-Ray uses random numbers in various parts of the rendering process, most notably in procedural texture generation. In the 2.x series these random numbers are by default sourced from your system RNG, which is usually a good idea (especially in scenarios involving cryptography where you want things to be as random as possible) but in this context it creates a couple of issues for us.

The first is that when you render the same scene on different operating systems, the final image can vary considerably, as seen here with "Planet" (planet.pov) by Ville Saari:

-

planet.pov on Debian 12 with system RNG

-

planet.pov on FreeBSD 13.2 with system RNG

This is quite a problem if aspects of a scene were designed around the way a procedural texture was generated. The other even worse issue is that sometimes, your system RNG will cause POV-Ray 2.2 to crash during rendering, demonstrated here with "Pawns" (pawns.pov) by Douglas Otwell:

-

pawns.pov on Debian 12 with system RNG

-

pawns.pov on Debian 12 with fixed RNG

Fortunately, the POV-Ray developers were aware of these consistency issues and mention it in the documentation, their suggested fix is to incorporate a deterministic RNG found in the IBM-compatible machine files. Head to the machine folder and this time look inside the ibmpc directory, there you will see a file called ibm.c, open it in your editor of choice. Navigate to line 2784 and you should find the following block of code:

```
/* ANSI Standard pseudo-random number generator */

int

rand
(
void
);

void

srand
(
int
);

static

unsigned

long

int

next

=

1
;

int

rand
()

{

next

=

next

*

1103515245L

+

12345L
;

return

((
int
)

(
next

/

0x10000L
)

&

0x7FFF
);

}

void

srand
(
seed
)

int

seed
;

{

next

=

(
unsigned

long

int
)
seed
;

}
```

This is what we need, so copy it to your clipboard. In the source folder open unix.c, then paste the code at the end of the file, don't forget to save your changes and voila! Now there's just one more thing to sort out before building.

## Enabling extra optimization

Although modern computers are orders of magnitude faster than the '90s machines POV-Ray 2 was written for, more complex scenes can still take quite some time to render. Fortunately though there's a free performance boost that we can employ with just a small edit.

In the source folder open unix.mak, at line 21 you should see the following:

```
CFLAGS
=
 -c -O

LFLAGS

=
 -o povray -O
```

These are the parameters (flags) that will be given to our compiler. By default our build will only have basic optimization, we're going to change this so that we create a level 2 optimized build (level 3 also exists but can sometimes cause bugs or even sacrifice performance compared to level 2, depending on the program). All you have to do is change the -O parameter on both lines so that it reads -O2:

```
CFLAGS
=
 -c -O2

LFLAGS

=
 -o povray -O2
```

## Compiling POV-Ray 2.2

It's time to actually compile our build. This is the easiest part of the process, just run this command in the source folder:

```
make -f unix.mak
```

If you would like to speed things up and have a multi-core CPU, you can add -jX to the end of that command, where X is the number of jobs to run simultaneously, set this to the number of cores & threads you have for a faster compilation time. Note that while compiling you will see a number of warnings displayed on the terminal, these can be ignored as they don't cause any issues with the program.

Once the build has finished you should have a file called povray, this is our program. To run it, first we'll make sure it's marked as an executable:

```
chmod +x povray
```

That changes the file permissions so that we can actually run it. Now let's see if it works:

```
./povray
```

You should see some authorship and basic usage information printed to your terminal, if so then you've successfully built POV-Ray 2.2!

## Rendering a scene

Now we have a binary let's test it out. Navigate to where you extracted POVSCN, inside there you will see a folder called povscn (not present in the ZIP archive) and under that you'll find the folder scenes, descend into it and from there head into the level2 directory. We can either copy our povray binary to the level2 folder or add it to our PATH, programs in our PATH can be run from any location on the system, allowing us to simply type povray in a terminal to execute it. On Debian and derivatives, if the folder structure .local/bin/ exists in our home directory it will be added to the PATH when we login, if it doesn't exist you can create it like so (logout and in after creating it to update your PATH):

```
mkdir -p ~/.local/bin/
```

Copy povray to bin and optionally rename it to povray2 so it doesn't conflict with a system-wide POV-Ray installation, such as if you have the povray package installed.

One last thing before rendering, we need to provide POV-Ray with the location of a folder called include to avoid some missing file errors. Head to where you extracted POVDOC and look inside, enter the povdoc folder (not present in the ZIP archive) and you'll see the include directory we're looking for. Make a note of its location and continue on.

We'll be rendering planet.pov from earlier, this time with the RNG fixed. Run the following command in the level2 folder (make sure you provide the location of include) and let POV-Ray 2.2 do its thing:

```
povray2 +L<Location of
"include"
 directory> +Iplanet.pov +Oplanet.tga +FT +W640 +H480 +A0.1 +V
```

Once the render has finished you should have a planet.tga file, if it looks like these examples then you've correctly followed this guide:

-

planet.pov on Debian 12 with fixed RNG

-

planet.pov on FreeBSD 13.2 with fixed RNG

## External links

Original version of this tutorial

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Configure your favorite text editor to work with POV-Ray

BSD

- neXtgen Povray Editor

- Vimfeatures POV highlighting and POV CTags support file can be found on the newsgroups

- Blender has a text editor with POV features when native addon is activated
Linux

- eclipse with povclipse plugin

- neXtgen Povray Editor

- KPovModeler

- Kate

- Vim

- Blender's text editor
Windows

- Qwined

- SciTE

- neXtgen Povray Editor

- ConTEXT using a language extension

- Vim

- Blender's text editor

## Instructions

SciTE on Windows (other platforms should be similar)

Syntax highlighting of POV-Ray SDL is already supported by default. However, an option for POV-Ray is missing from some of the necessary menus and dialogs. To fix this:

- Select the "Options" menu. Select "Open Global Options File". A text file with a bunch of SciTE configuration settings will open in the editor window.

- Scroll down to the section that looks like this:
```
source.files=*.asm;*.c;*.cc;*.cpp;*.cxx;*.cs;*.h;*.hh;*.hxx;*.hpp;\
```

```
*.idl;*.odl;*.rc;*.rc2;*.dlg;*.def;\
```

```
*.vb;*.vbs;*.bas;*.frm;*.cls;*.ctl;\
```

```
*.java;*.js;*.py;*.pl;*.rb;*.cgi;*.lua;*.conf;\
```

```
make*;*.mak;\
```

```
*.properties;*.html;*.xml;*.iface;*.bat;*.el
```

- Add the common POV-Ray file extensions to the list. These include the POV, INC, INI and MCR extensions. It should now look like this:
```
source.files=*.asm;*.c;*.cc;*.cpp;*.cxx;*.cs;*.h;*.hh;*.hxx;*.hpp;\
```

```
*.idl;*.odl;*.rc;*.rc2;*.dlg;*.def;\
```

```
*.vb;*.vbs;*.bas;*.frm;*.cls;*.ctl;\
```

```
*.java;*.js;*.py;*.pl;*.rb;*.cgi;*.lua;*.conf;\
```

```
make*;*.mak;\
```

```
*.properties;*.html;*.xml;*.iface;*.bat;*.el;*.pov;*.inc;*.ini;*.mcr
```

- After restarting SciTE Povray source files should now be listed in the "Open File", "Save File", etc. dialogs.

- Scroll to the section labeled "Define the Lexer menu". Uncomment the line that reads:
```
#P&OV-Ray SDL|pov||\
```

- It should now look like this:
```
P&OV-Ray SDL|pov||\
```

- After restarting SciTE an entry for POV-Ray SDL should now appear in the "Language" menu.

To set up SciTE to send files to POV-Ray for editing:

- Select the "Options" menu and select "Open pov.properties". A text file with POV-Ray-specific configuration settings will open in the editor window.

- Scroll down to where it says:
```
command.compile.*.pov=pvengine.exe $(FileName)
```

- Replace the line with the following. Also change the file path to reflect the location of your POV-Ray installation. On my system (Windows 7) it ended up being:
```
command.build.*.pov=cmd /c start "" "C:\Users\...username...\AppData\Roaming\POV-Ray\v3.6\bin\pvengine64.exe" /EDIT "$(FilePath)"
```

- Note: The cmd /c start "" part is necessary to keep SciTE from waiting for POV-Ray to quit.

- After restarting SciTE pressing the F7 key should now automatically send the file to POV-Ray for editing.

To set up SciTE to send files to POV-Ray for rendering:

- If not already open, select the "Options" menu and select "Open pov.properties". A text file with POV-Ray-specific configuration settings will open in the editor window.

- Scroll down to where it says:
```
command.go.*.pov=pvengine.exe $(FileName)
```

- Replace the line with the following. Also change the file path to reflect the location of your POV-Ray installation. On my system it ended up being:
```
command.go.*.pov=cmd /c start "" "C:\Users\...username...\AppData\Roaming\POV-Ray\v3.6\bin\pvengine64.exe" /RENDER "$(FilePath)"
```

- Note: The cmd /c start "" part is necessary to keep SciTE from waiting for POV-Ray to quit.

- After restarting SciTE pressing the F5 key should now automatically send the file to POV-Ray for rendering.

To set up SciTE for context-sensitive POV-Ray help:

- Select the "Options" menu. Select "Open pov.properties". A text file with POV-Ray-specific configuration settings will open in the editor window.

- Scroll down to where it says:
```
command.help.$(file.patterns.pov)=$(CurrentWord)!...
```
change the file path to reflect the location of your POV-Ray installation. On my system it was:
```
command.help.$(file.patterns.pov)=$(CurrentWord)!C:\Users\...username...\AppData\Roaming\POV-Ray\v3.6\help\povray36.chm
```

- After restarting SciTE pressing the F1 key should now automatically open the POV-Ray help file to the correct entry based on the keyword you have highlighted in the scene description file.

## External links

- Comfortable editor for POV-Ray under Debian Linux?

- Useful tools for POV-Ray for Unix

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Create anaglyph images

I have been working with 'stereo' imagery for many years - I come from the Dan Dare and Weetabix days - and made a study of it at Cambridge University in the sixties.

There are a great many ways of seeing the world in stereo, but perhaps the simplest is using parallel images. You may prefer to view them cross-eyed, but personally I have never managed this.

On a computer, these are very easy to manipulate as you can easily resize the pair of images to suit your eyes - starting off small and increasing the size and resolution as your eyes become accustomed to viewing such images.

Stereo pairs are created by putting together two 'photos' taken at the positions where your two eyes would be - and viewing one with each eye. Obviously eyes vary in separation, but as scenes are scaled arbitrarily the distance between 'photos' is also arbitrary. You can adjust the separation to fit the scene, and also adjust for effect rather than scientific reality.

The simplest way to perform this in POVRay is to move the camera (and viewpoint) using the clock variable.

Use the parameter +KFI0 +KFF1 in the command line box, and
camera { location < clock - 0.5, 5, 10> look_at < clock - 0.5, 0, 0> } in your scene, which will move the camera by one unit between shots.

The rest of the scene can be as simple or as complex as you wish - this is the scene shown above. . .

plane { <0, 1, 0>, -10 pigment { checker color rgb<1,1,0>, color rgb<0,1,1> scale 20 } }

box { < 0, 0, 0>, < 2, 2, 2> texture { pigment { color rgb<.5, .5, .5> } } rotate <30,30,30> }

light_source { <0,100,100> rgb<2,1,1> }

Running the scene (TEST.POV in this case) will produce TEST0.bmp and TEST1.bmp. I copied these into a photoeditor and placed them side by side saving them as a single JPG shown above.

The advantage of side-by-side images is that they can be viewed in full-colour, although prolonged abuse of the eyes can be painful and ultimately damaging to your eyes.

An ANAGLYPH is the term usually used for a combination of a stereo pair of greyscale images which you view through special glasses - typically red-green or red-cyan.

The two greyscale images are tinted to the colours of the lenses, and merged into a single image. Each eye will only see one of the images. The 3-d impresssion is less than perfect, but more relaxing on the eyes in the long run, and can be considerably larger than side-by-side images.

You can create these images with sophisticated photo-manipulation programs like Photoshop, but it is much easier to use a dedicated tool (search for Stereo Photomaker on the web or download from here ) which does the one job very well.

The special red-green or red-cyan glasses are readily available on eBay for 'pennies', and shouldn't be confused with LCD shutter glasses (which cost about 25GBP/40EUR/50USD) or Polaroid glasses (which are about 10GBP/15EUR/20USD).

Polaroid glasses are designed for projected images (using a pair of projectors) and shutter glasses are designed for use with certain video cards on CRT monitors (television screens rather than flat panels). Both of these produce excellent full-colour 3d impressions without eye-strain but require specialist equipment.

Once you get the hang of creating anyglyphic images, you can easily move up to animations, although the clock method doesn't work here.

I produce a suitable animation with a constant in the header that will move the camera to a left-eye or right-eye position, and perform two runs, creating left and right-eye image sets.

I merge these sets into side-by-side videos using VIDEOMACH by Gromada which has tools for making an AVI file from a set of BMPs, and for producing multipane presentations from a set of videos. There is also a version of the StereoPhotomaker which produces anaglyphic movies from a pair of videos.

There are some sample video animations on DivX's Stage 6 in the HALFWITS collection - I will be uploading the anaglyphic images shortly.

Pat Crabb MA (Cantab)

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Create animations

Animation is the rapid display of a sequence of images in order to create an illusion of movement (see Wikipedia:Animation). POV-Ray can be used to create such a sequence of images. Creating these images in POV-Ray may be very different from using other graphical tools that you are used to. As with other forms of animation, small sequences can be done quite easily, but when animations grow, a little planning and preparation goes a long way so:

- Define what it is that interests you (what type of animations do you want to produce)

- Establish which categories of tool you will need

- Identify applications that address those needs, attempting to minimise the number of different applications you'll have to learn

- Join the various communities that use those tools so that you can keep abreast of changes that may impact you

## Contents

- 1 Basics

- 2 Animating objects

- 3 Animating object properties

- 4 Animating the camera

- 5 Creating complex behaviour

- 5.1 State variables

- 5.2 Drawing the objects

- 5.3 Rules

- 5.4 Detailed control

- 5.5 More to try

- 6 Cyclic animations

## Basics

POV-Ray provides a number of features to help produce a sequence of individual still images but does not put them together into an animation. You'll therefore need a separate piece of software to assemble the animation. There are lots of application programs available that will do this, including several free and open source products. You'll need to pick one that can generate the animation file format you wish to produce, e.g. AVI, MPEG, FLI/FLC, QuickTime, or animated GIF files (popularly used on Web pages). Try Googling animation utilities.

When animating, we use the ini-file (or equivalent command line options) to tell the renderer what frames to render, and what clock values the renderer should use. Our scene file can then use settings such as the clock variable to control the generation of a particular image or 'frame' in the sequence.

```
+
W150

+
H150

Input_File_Name
=
robot
.
pov

Initial_Frame
=
1

Final_Frame
=
30

Initial_Clock
=
0

Final_Clock
=
1
```

When using the above INI-file, POV-Ray will render 30 frames and the clock variable will run from 0 to 1. The clock setting as defined in the ini-file or on the command line will be available when your scene is parsed at render time as the variable 'clock'. If not specified, the value of the clock variable will pass from 0 to 1 through the animation sequence. The frame number will be available to your scene file as the variable 'frame_number'. To be of interest something should change during the animation, often with each frame. This change is usually controlled by using the clock variable or the frame_number variable in your scene file.

## Animating objects

To animate a ball we could use the clock variable to control its attributes, position, orientation etc. For example:

```
sphere

{

<
0
,
clock
,
0
>,
1

pigment

{
rgb

1
}}

or

sphere

{

<
0
,
0
,
0
>,
1

translate

<
0
,
clock
,
0
>

pigment

{
rgb

1
}}
```

Both examples move a sphere up the Y-axis by a fixed amount per frame.

```

sphere

{

<
0
,
0
,
0
>,
0.5
*
(
1
+
clock
)

pigment

{
rgb

1
}}
```

Increases the radius of the sphere from 0.5 units to 1 unit as the animation sequence progresses.

## Animating object properties

More interesting behaviour is possible is you imagine everything can be animated. Allthough impossible in the real world you can show how a piece of glass would look if it's index of refraction would slowly change.

## Animating the camera

Animating a camera requires some planning when complex movements are required. Smooth movement is possible if you can specify a 'neat' set of position functions, and a flyby look-and-feel is possible if you also take into account gravity, speed and acceleration, so you can adjust the 'roll' of the camera according to the current down direction.
This dynamical downdirection is a lineair combination of the gravity vector, and the centrifugal forces caused by direction changes.

## Creating complex behaviour

When you want to show complex behaviour in your animation, you will have a lot of programming to do. Sometimes however it is not necessary to define everything in detail. Sometimes simple sets of rules (working on a simple set of variables) wil exhibit complex behavior which will nonetheless be recognizable.

It is possible to define an array of object, say birds or fish, setting up variables for position, orientation and speed, and creating a macro to draw them (with this orientation and speed). Now every time a frame is rendered you call the macro which draws all birds or fish, and call a macro which calculates the new state for every object (position, speed) and stores it for use in the next frame. This calculation macro is in fact a simple set of -more or less- high-level rules. Changing these rules, you setup behavior, not exact positions for the objects. You can 'guide' each bird, not exactly, but with simple rules like "stay above the ground" and "if you are going to collide, turn left a bit" you have some control. These sets of rules will generate almost random behavior, which has recognizable dynamics like flocking for birds, traffic-jamming for cars, or bending in the 'wind' for trees.

### State variables

The state variables are the core of this method, and you will need to carefully analyse the behavior of the object you are trying to mimic. You the capture these in a simple data structure. You might create something like this:

```
#declare

Blocation

=

array
[
max_birds
];

#declare

Bvelocity

=

array
[
max_birds
];

#declare

Bphase

=

array
[
max_birds
];
```

The amount of detail in the dissemination of the object tends to grow in time, see below for further explanation of the phase-parameter.

To make this work you will need to 'transfer' the state of the objects (birds) from each frame to the next. This can easily be done using macro's to write out, and readback the variables to a file on disk. The following macro's can be easily extended to save and load more complex data-sets.

```
#macro

SaveState
()

#fopen

wfile

"state.txt"

write

#local

i

=

0
;

#

while

(
i
<
max_birds
)

#write

(

wfile
,

Blocation
[
i
],

","
,

Bvelocity
[
i
],

","

,

Bphase
[
i
],

","
)

#local

i
=
i
+
1
;

#end

#fclose

file

#end

#macro

LoadState
()

#fopen

rfile

"state.txt"

read

#local

i

=

0
;

#while

(
i
<
max_birds
)

#read

(

rfile
,

Blocation
[
i
]

,
Bvelocity
[
i
],

Bphase
[
i
]

)

#local

i
=
i
+
1
;

#end

#fclose

file

#end
```

### Drawing the objects

You will need a macro or set of macros to draw the objects into the scene. The macro to draw the object will have to take into account all relevant parameters. For instance a bird rolls when taking a turn, according with it's changing down-vector. Also the wings will have a current state (a phase) which determines where the wings are in the up-down direction.

### Rules

In the main file you will want to execute some 'business rules' for every bird to define it's solitary behaviour, and maybe you will want to run some other rules for each bird-pair, so social behaviour can be controlled. These rules act on the state variables, but can also gather information elsewhere provided the variables needed are in scope.

You can embed these rules in a macro which you run each frame. In this macro you could create some intermediate variables, deduced from the state variables to make the rule definition somewhat more comfortable. In the example above, we could define a speed, which is easier to compare than a velocity vector.

```
#local Speed = vlength(Bvelocity[i]);
```

Now further on you can create a rule to stop birds from stalling:

```
// Do not stall

#if

(
Speed
<
1
)

#declare

Bvelocity
[
i
]
=
Bvelocity
[
i
]
*
1.2
;

#end
```

### Detailed control

As said you cannot control these objects at the position-level using these rules. At rendertime you have access to all the variables so you could do extra checks and 'force then around'. However this will not work at parse-time. Suppose you create a scene with mountains and buildings, and want to add some birds. Now when the birds calculate their 'next move' the information about buildings and mountains is NOT available. Detailed interaction with the scene requires capturing behaviour in rules, and creating an environment where data about say buildings and mountains can be stored. Now the extra rules can act on this data.

### More to try

- You could consider animating things like fish, cars, trees, houses and much more.

- Try animating different species with specific interactions.

## Cyclic animations

Suppose you want to make an animation that loops, so that after the last frame has been rendered it should hop back to the first frame and play everything again. To make the jump from the last to the first frame seamless, the animation must end with exactly the same scene as it started with, i.e. parsing the file with clock = 0 and clock = 1 must generate the same image. When animating this however, you will find a slight glitch in the animation: since the first and last frame are identical it will look like like the animation stops for a while. You could prevent this simply by deleting the last frame after you have rendered them, or only render a subset of the frames to leave out the last one, but POV-ray offers a much better solution. Simply add

```
Cyclic_Animation
=
On
```

to the INI-file, or +KC to the command line. POV-ray will now automatically do the following:

- Increase the number of frames by one (so if you chose to have 50 frames in the animation, POV-ray will make it 51)

- but not render the last frame (so you'll still only get 50 images rendered, since the 51st one would be identical to the first)

When these images are compiled into a looping animation, you'll get a nice, smooth, and glitch-free animation. For example, here's how to create an Ogg Theora video file.

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Encode animations as Ogg Theora Video

As illustrated in the other tutorials, POV-Ray only renders the individual frames of an animation. It is up to you to compile these frames into an animation file that you can view and share with others. Ogg Theora is a good choice if you need higher image quality than GIF can provide. Theora is a non-proprietary video format so you won't need to worry about licensing and royalties if you go commercial with your work.

## Contents

- 1 Animate Your Frames

- 2 Encoding Under Linux

- 2.1 Install the tools

- 2.2 Encode!

- 3 Encoding On other Operating Systems

- 4 View It!

- 5 (Example)

- 5.1 cube.pov

- 5.2 cube.ini

## Animate Your Frames

The following instructions assume you have a directory containing the image frames created with POV-Ray. See the bottom of this page if you need help with this step.

## Encoding Under Linux

Instructions for encoding on the Linux platform:

### Install the tools

You'll need the following command-line tools installed:

- png2yuv (Part of the MJPEG Tools project. Under Ubuntu/Debian simply install the mjpegtools package.)

- ffmpeg2theora (ffmpeg2theora home page. Under Ubuntu/Debian simply install the ffmpeg2theora package.)

### Encode!

1) Open a command shell to the directory containing your image frames. (Note: the frames must be in PNG format (POV-Ray default).)

2) Compile the frames to the intermediate YUV4MPEG stream format:

```
png2yuv -I p -f 25 -j cube%02d.png -b 1 > tmp.yuv
```

Explanation of command:

- -I p - no interlacing

- -f 25 - Frame-rate (25 fps). 25 fps is good for motion video.

- cube%02d.png - Frame file name pattern. This says our files are named cube00.png through cube99.png. (png2yuv will stop when it finds no more numbers). If you had hundreds of frames you would specify: %03d, thousands: %04d, and so on.

- -b 1 - Start on cube01.png rather than cube00.png (because we don't have cube00.png)

- > tmp.yuv - Redirect the output to a file.

3) Compress into Theora format:

```
ffmpeg2theora --optimize --videoquality 10 --videobitrate 16778 -o MyAnim.ogv tmp.yuv
```

Explanation of command:

- --optimize --videoquality 10 --videobitrate 16778 - This is my attempt to give the highest video quality. Type ffmpeg2theora --help for a description of the parameters (or man ffmpeg2theora).

- -o MyAnim.ogv - The output file name. (I believe .ogv is the conventional extension for Ogg Theora video)

- tmp.yuv - the input file (the one we created with png2yuv).

## Encoding On other Operating Systems

TODO. This is a wiki so contributors are welcome to fill this in.

## View It!

You should now have MyAnim.ogv waiting to be viewed. I used the Totem program to view it under Linux. Here's a page listing other players.

Have fun!

## (Example)

If you need an example pov script to create the animation frames...

- Save the following two files to a temporary folder

- Open a command shell to the folder and type:

- povray cube.ini +W320 +H240

- After rendering you should 50 png image files (cube01.png - cube50.png)

- Use above instructions to encode the frames

### cube.pov

```
global_settings

{

assumed_gamma

1.5

noise_generator

2

}

box

{

<
-
0.5
,

-
0.5
,

-
0.5
>,

<
0.5
,

0.5
,

0.5
>

pigment

{

color

rgb

<
0.3
,

1
,

0.3
>

}

scale

1

rotate
<
0
,

360
*
(
clock
+
0.00
),

0
>

translate

y
*
0.5

}

light_source

{

<
4
,

5
,

-
5
>,

rgb

<
1
,

1
,

1
>

}

camera

{

perspective

location

<
2
,

2
,

2
>

sky

<
0
,

1
,

0
>

direction

<
0
,

0
,

1
>

right

<
1.3333
,

0
,

0
>

up

<
0
,

1
,

0
>

look_at

<
0
,

0
,

0
>

}

plane

{

<
0
,

1
,

0
>,

0

pigment

{

color

rgb

<
0
,

0.784314
,

1
>

}

finish

{

reflection

{

rgb

<
1
,

1
,

1
>

}

}

scale

1

rotate

<
0
,

0
,

0
>

translate

y
*
(
-
0.25
)

}
```

### cube.ini

```
; POV-Ray animation ini file

Antialias
=
On

Antialias_Threshold
=
0.1

Antialias_Depth
=
2

Input_File_Name
=
cube.pov

Initial_Frame
=
1

Final_Frame
=
50

Initial_Clock
=
0

Final_Clock
=
1

Cyclic_Animation
=
on

Pause_when_Done
=
off
```

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Extract 2D screen coordinates from 3D scenes

- 1 Introduction

- 2 Steps

- 3 Sample Scene #1

- 4 Sample Scene #2

## Introduction

At some point in your POV-Ray career you may need to retrieve high precision 2D screen coordinates of a point in a 3D POV-Ray render. Simply measuring the number of pixels in MS Paint is rarely a good solution because the precision is too low, even for very large renders. Instead, you need to use POV-Ray's own mechanisms to calculate and output finer measurements.

I faced this problem when designing two images for Wikipedia. For the first image, I needed to overlay 2D vector elements on top of a 3D POV-Ray render in order to show a series of measurements I made of important angles in the 3D bitmap. For the second image, I used the points outputted from a POV-Ray render to create a brand new 2D vector image from scratch. In both cases I used GeoGebra to create the 2D vector elements, but you could use InkScape for this purpose as well.

(The first image is named "Graphical projection comparison.png". The second image is named "Various projections of cube above plane.svg". I can't link to them from here because the anti-spam captcha widget on this wiki is broken.)

## Steps

The first thing you will need to do is download the updated version of "screen.inc" I created several years back, and place it somewhere in your library path. (Again, I cannot link to the file, but the thread is named "Updated screen.inc" and is located in the "povray.text.scene-files" newsgroup. Make sure to grab the script from the latest post.) Once you have copied the script file to the correct directory, you will need to include it in your scene.

```
#include "screen.inc"
```

The next step is to reconfigure your camera code to make it compatible with "screen.inc". Here is some camera code I created for a test scene:

```
#local cam_dist = 8;
#local cam_move = 1/2;
#local cam_area = 2;
#local cam_loca = -z * cam_dist;
#local cam_look = 0;
#local cam_angl = 45;
#local cam_tran = transform
{
 rotate +x * asind(tand(30))
 rotate +y * 045
 translate +y * cam_move
}
Set_Camera_Orthographic(false)
Set_Camera_Transform(cam_tran)
Set_Camera(cam_loca, cam_look, cam_angl)
```

The updated script can also handle orthographic renders, as well as input values that take the form of location, direction, up and right instead of location, look_at, angle and sky. For example:

```
#local cam_aspc = image_width/image_height;
#local cam_dist = 8;
#local cam_move = 1/2;
#local cam_area = 2;
#local cam_loca = -z * cam_dist;
#local cam_dirc = +z;
#local cam_rgvc = +x * cam_area * cam_aspc;
#local cam_upvc = +y * cam_area;
#local cam_tran = transform
{
 rotate +x * asind(tand(030))
 rotate +y * 045
 translate +y * cam_move
}
Set_Camera_Orthographic(true)
Set_Camera_Transform(cam_tran)
Set_Camera_Alt(cam_loca, cam_dirc, cam_rgvc, cam_upvc)
```

From that point onward, all you need to do is choose which 3D points you want to capture and output them as 2D screen coordinates. For instance, maybe there is a light source in your scene that you want to record the 2D position of:

```
#local light_location_3d = <+30, +30, -30>;

light_source
{
 light_location_3d
 color rgb <1, 1, 1>
 parallel
 point_at 0
}
```

Now that you have the 3D coordinates, just plug them into the Get_Screen_XY macro, like this:

```
#local light_location_2d = Get_Screen_XY(light_location_3d);
```

and send the result to the Messages window like this:

```
#debug concat("light_location_2d = (", vstr(2, light_location_2d, ",", 0, -1), ")\n")
```

As you render the scene, additional text will appear in the Messages window, looking similar to this:

```
light_location_2d = (205.036679,371.407180)
```

You can now use these coordinates to place 2D objects in a vector image or mathematics program such as GeoGebra or InkScape.

## Sample Scene #1

Here is a very simple sample scene I created for this tutorial, using a perspective camera:

```
// Persistence of Vision Ray Tracer Scene Description File
// This file is licensed under the terms of the CC-LGPL.
// File: screen_cube.pov
// Vers: 3.7
// Desc: Screen and camera test
// Date: 08/30/2018
// Updt: 09/01/2018
// Auth: Michael Horvath
//

#version 3.7;

// requires the latest version appearing in this thread:
// http://news.povray.org/povray.text.scene-files/thread/%3C5b889a96%40news.povray.org%3E/
#include "screen.inc"

global_settings {
 assumed_gamma 1.0
}

// -------------------------------------

// perspective camera
#local cam_dist = 12;
#local cam_loca = -z * cam_dist;
#local cam_look = 0;
#local cam_angl = 45;
#local cam_tran = transform
{
 rotate +x * asind(tand(30))
 rotate +y * 300
}
Set_Camera_Orthographic(false)
Set_Camera_Transform(cam_tran)
Set_Camera(cam_loca, cam_look, cam_angl)

background {color srgb <1,1,1>}

light_source {
 <0, 0, 0>
 color rgb <1, 1, 1>
 translate <+30, +30, -30>
 parallel
 point_at 0
}

light_source {
 <0, 0, 0>
 color rgb <1, 1, 1>
 translate <+30, +30, -30>
 rotate y * 30
 parallel
 point_at 0
}

// -------------------------------------

box {
 <-1,-1,-1>, <+1,+1,+1>
 pigment {
 color srgbt <1,1,1,1/4>
 }
}

#debug "\n"

#local SphereLoc_1 = <+1,+1,+1>;
#local ScreenLoc_1 = Get_Screen_XY(SphereLoc_1);
sphere {SphereLoc_1, 0.1 pigment {color srgb SphereLoc_1}}
#debug concat("ScreenLoc_1 = (", vstr(2, ScreenLoc_1, ",", 0, -1), ")\n")

#local SphereLoc_2 = <+1,+1,-1>;
#local ScreenLoc_2 = Get_Screen_XY(SphereLoc_2);
sphere {SphereLoc_2, 0.1 pigment {color srgb SphereLoc_2}}
#debug concat("ScreenLoc_2 = (", vstr(2, ScreenLoc_2, ",", 0, -1), ")\n")

#local SphereLoc_3 = <+1,-1,+1>;
#local ScreenLoc_3 = Get_Screen_XY(SphereLoc_3);
sphere {SphereLoc_3, 0.1 pigment {color srgb SphereLoc_3}}
#debug concat("ScreenLoc_3 = (", vstr(2, ScreenLoc_3, ",", 0, -1), ")\n")

#local SphereLoc_4 = <+1,-1,-1>;
#local ScreenLoc_4 = Get_Screen_XY(SphereLoc_4);
sphere {SphereLoc_4, 0.1 pigment {color srgb SphereLoc_4}}
#debug concat("ScreenLoc_4 = (", vstr(2, ScreenLoc_4, ",", 0, -1), ")\n")

#local SphereLoc_5 = <-1,+1,+1>;
#local ScreenLoc_5 = Get_Screen_XY(SphereLoc_5);
sphere {SphereLoc_5, 0.1 pigment {color srgb SphereLoc_5}}
#debug concat("ScreenLoc_5 = (", vstr(2, ScreenLoc_5, ",", 0, -1), ")\n")

#local SphereLoc_6 = <-1,+1,-1>;
#local ScreenLoc_6 = Get_Screen_XY(SphereLoc_6);
sphere {SphereLoc_6, 0.1 pigment {color srgb SphereLoc_6}}
#debug concat("ScreenLoc_6 = (", vstr(2, ScreenLoc_6, ",", 0, -1), ")\n")

#local SphereLoc_7 = <-1,-1,+1>;
#local ScreenLoc_7 = Get_Screen_XY(SphereLoc_7);
sphere {SphereLoc_7, 0.1 pigment {color srgb SphereLoc_7}}
#debug concat("ScreenLoc_7 = (", vstr(2, ScreenLoc_7, ",", 0, -1), ")\n")

#local SphereLoc_8 = <-1,-1,-1>;
#local ScreenLoc_8 = Get_Screen_XY(SphereLoc_8);
sphere {SphereLoc_8, 0.1 pigment {color srgb SphereLoc_8}}
#debug concat("ScreenLoc_8 = (", vstr(2, ScreenLoc_8, ",", 0, -1), ")\n")

#debug "\n"
```

Here is what a rendering of the above sample scene should look like:

SVG files are not working properly on this wiki, so here is a bitmap image of what a very simple GeoGebra image might look like.

Note that the direction of the y axis in GeoGebra is reversed when compared to most image editing programs. You will need to compensate for this by multiplying each of the y values by -1.

## Sample Scene #2

Here is another sample scene I created for this tutorial, this time using an orthographic camera and a series of additional transforms:

```
// Persistence of Vision Ray Tracer Scene Description File
// This file is licensed under the terms of the CC-LGPL.
// File: czech_hedge_hog.pov
// Vers: 3.7
// Desc: Screen and camera test
// Date: 08/30/2018
// Updt: 09/01/2018
// Auth: Michael Horvath
//

#version 3.7;

#include "colors.inc"
// requires the latest version appearing in this thread:
// http://news.povray.org/povray.text.scene-files/thread/%3C5b889a96%40news.povray.org%3E/
#include "screen.inc"

global_settings {
 assumed_gamma 1.0
}

// ----------------------------------------

// orthographic camera
#local cam_aspc = image_width/image_height;
#local cam_dist = 8;
#local cam_move = 1/2;
#local cam_area = 2;
#local cam_loca = -z * cam_dist;
#local cam_dirc = +z;
#local cam_rgvc = +x * cam_area * cam_aspc;
#local cam_upvc = +y * cam_area;
#local cam_tran = transform
{
 rotate +x * asind(tand(030))
 rotate +y * 045
 translate +y * cam_move
}
Set_Camera_Orthographic(true)
Set_Camera_Transform(cam_tran)
Set_Camera_Alt(cam_loca, cam_dirc, cam_rgvc, cam_upvc)

sky_sphere {
 pigment {
 gradient y
 color_map {
 [0.0 rgb <0.6,0.7,1.0>]
 [0.7 rgb <0.0,0.1,0.8>]
 }
 }
}

light_source {
 <0, 0, 0>
 color rgb <1, 1, 1>
 translate <+30, +30, -30>
 parallel
 point_at 0
}

// ----------------------------------------

plane {
 +y, 0
 pigment {
 checker
 pigment {Gray}
 pigment {White}
 }
}

#local TempAngle = pi/2 - atan(1/cos(pi/4));
#local TempTrans = transform
{
 rotate +x * 45
 rotate +z * degrees(TempAngle)
 translate +y * sin(TempAngle)
 translate +y * 1/10
}

union {
 cylinder {-x,+x,0.1 pigment {Red}}
 cylinder {-y,+y,0.1 pigment {Green}}
 cylinder {-z,+z,0.1 pigment {Blue}}
 transform {TempTrans}
}

#debug "\n"

#local SphereLoc_1 = vtransform(-x, TempTrans);
#local ScreenLoc_1 = Get_Screen_XY(SphereLoc_1);
sphere {SphereLoc_1, 0.1 pigment {Cyan}}
#debug concat("ScreenLoc_1 = (", vstr(2, ScreenLoc_1, ",", 0, -1), ")\n")

#local SphereLoc_2 = vtransform(+x, TempTrans);
#local ScreenLoc_2 = Get_Screen_XY(SphereLoc_2);
sphere {SphereLoc_2, 0.1 pigment {Cyan}}
#debug concat("ScreenLoc_2 = (", vstr(2, ScreenLoc_2, ",", 0, -1), ")\n")

#local SphereLoc_3 = vtransform(-y, TempTrans);
#local ScreenLoc_3 = Get_Screen_XY(SphereLoc_3);
sphere {SphereLoc_3, 0.1 pigment {Magenta}}
#debug concat("ScreenLoc_3 = (", vstr(2, ScreenLoc_3, ",", 0, -1), ")\n")

#local SphereLoc_4 = vtransform(+y, TempTrans);
#local ScreenLoc_4 = Get_Screen_XY(SphereLoc_4);
sphere {SphereLoc_4, 0.1 pigment {Magenta}}
#debug concat("ScreenLoc_4 = (", vstr(2, ScreenLoc_4, ",", 0, -1), ")\n")

#local SphereLoc_5 = vtransform(-z, TempTrans);
#local ScreenLoc_5 = Get_Screen_XY(SphereLoc_5);
sphere {SphereLoc_5, 0.1 pigment {Yellow}}
#debug concat("ScreenLoc_5 = (", vstr(2, ScreenLoc_5, ",", 0, -1), ")\n")

#local SphereLoc_6 = vtransform(+z, TempTrans);
#local ScreenLoc_6 = Get_Screen_XY(SphereLoc_6);
sphere {SphereLoc_6, 0.1 pigment {Yellow}}
#debug concat("ScreenLoc_6 = (", vstr(2, ScreenLoc_6, ",", 0, -1), ")\n")

#debug "\n"
```

Here is what a rendering of the above sample scene should look like:

The center points of the spheres at the end of each arm are the points I am recording. Note the many instances of the transform block and the vtransform function. If you apply transformations to objects in your scene, then you're likely going to need to apply the same transformations to your point coordinates as well! The vtransform function makes this possible.

SVG files are not working properly on this wiki, so here is a bitmap image of what a very simple GeoGebra image might look like.

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Fix artifacts

On occasion, some objects in your scene may have speckles or what looks like random noise on the surface. Below are a few possible causes:

## Contents

- 1 Coincident Surfaces

- 2 Radiosity

- 3 Transparent and Reflective Surfaces

- 4 Other Causes

- 5 Floating-point expressions

## Coincident Surfaces

Main article: Documentation:Tutorial Section 2.1#Co-incident Surfaces

Two or more surfaces occupy the same plane. The choice of which surface gets rendered varies from pixel to pixel according to the whim of POV-Ray. Translating one surface by 0.0001 out of the plane will usually fix this. This is probably the most common cause of 1px speckles on specific objects in a scene.

If you are trying to model a single object with both an inside and outside texture, you might try using the interior_texture keyword.

## Radiosity

In scenes using radiosity, if the artifacts show up only when radiosity is turned on, then they are caused by radiosity sampling errors. If you haven't already done so, check the Official Documentation on Radiosity or the HowTo: tutorial on radiosity.

## Transparent and Reflective Surfaces

Scenes that many reflective/transparent surfaces may run afoul of the default max_trace_level setting. Check the messages from the render, as POV-ray will tell you if the max_trace_level was reached during a render. If it was reached, raise the max_trace_level to a higher value.

## Other Causes

You've rendered a scene using certain textures with anti-aliasing turned off. The difference caused by the use of anti-aliasing can be startling, especially with a texture which is supposed to have weird speckles! ;-) Note that the appearance of speckles on a surface is not only controlled by the pigment statement, but can be the result of a small, bumpy normal, or the use of crand in the finish statement.

## Floating-point expressions

You've created an expression where some fractional or decimal values are operated upon to create an integer, but the expression doesn't evaluate to the correct amount. This problem is related to the one of co-incident surfaces, in that floating-point values are cut-off at a level that is less precise than the one you are checking for.

The underlying problem is the same as if you'd try to compute 1/3 + 1/3 + 1/3 + ... using decimal numbers with a certain limit in precision; as you can't exactly represent 1/3 this way, you'd get e.g.:

```
 0.33 + 0.33 = 0.66
 0.66 + 0.33 = 0.99
 0.99 + 0.33 = 1.32
 ...
```

In practice, if you have a fixed value to add, you can usually "re-normalize" the whole problem to use integers for your loop variable; e.g. instead of:

```
 #local Foo = 0.0;
 #while (Foo <= 1.0)
 ...
 #local Foo = Foo + MySmallValue
 #end
```

you can use:

```
 #local Bar = 0.0;
 #while (Bar * MySmallValue <= 1.0)
 #local Foo = Bar * MySmallValue;
 ...
 #local Bar = Bar + 1.0;
 #end
```

Somewhat more generally, any integer divided by any power of 2 is safe to add (unless it's extremely small compared to the "target value").

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Fix old scenes to work with the new gamma system

Note: The title of this page and part of its contents are somewhat outdated, as POV-Ray 3.7.0 proper will now support virtually full backward compatibility for legacy scenes, and in this sense there will be almost nothing to fix. The information in this page rather applies to migrating a scene to use the physically realistic `assumed_gamma 1.0`

Before "assumed_gamma 1.0" or the most recent 3.7 betas, the colour math was simply wrong. So in order to get exactly the same result, you'd have to mimic that wrong colour math - which 3.7 refuses to, except for legacy scenes using "#version 3.6" (or anything smaller than 3.7) and "assumed_gamma 2.2".

If you do want to port the scene to the right math, here's what I expect to be the most important steps:

- In the ini-file, set the "Display_Gamma" to whatever fits your system; set "File_Gamma" to the same. (Ideally, your system should have a Display_Gamma of 2.2.)

- In the scene file, use "#version 3.7", and remove any "assumed_gamma" statement.

- Gamma-adjust all colour literals, by raising the R,G,B components to the power of 2.2 (or wait for the next beta, and use "srgb" instead of "rgb"). Check your scene for colour values that are computed from some other values - you may (or may not) want to gamma-adjust these as well.

- Check your scene for input image files, and whether they are handled properly regarding gamma. For images that are used as anything non-color (e.g. height field data, bump map, texture map or what-have-you), explicitly specify "gamma 1.0" after the filename. For any images that do wind up being used as a colour, try the default first (i.e. no "gamma" statement); if the colours appear too washed-out or too strong, try explicitly specifying "gamma srgb".

- Toy around with overall brightness and/or brightness of individual light sources, until you feel comfortable with the illumination. You may also want to tweak some of the colours in your scene (both pigment and light sources). If you're using self-made input image files, you may also want to tweak their gamma setting, but try to avoid this with 3rd party input images (you should usually find a way to make them work by tweaking the scene rather than the image).

### Color literals

As of version 3.7.0.beta.41, POV-Ray natively supports gamma-adjustment of color literals, provided they conform to the sRGB standard:

```
pigment

{
color

srgb

<
110
,
160
,
008
>
/
255

}

pigment

{
color

srgbft

<
1
,
1
,
1
,
0
,
1
>

}

pigment

{
color

srgbt

<
1
,
1
,
1
,
1
>

}
```

If you are still using version 3.6, or need more flexibility of the gamma parameter:

```
#macro

gamma_color_adjust
(
in_color
,

out_gamma
)

rgbft

<

pow
(
in_color
.
red
,

out_gamma
),

pow
(
in_color
.
green
,

out_gamma
),

pow
(
in_color
.
blue
,

out_gamma
),

in_color
.
filter
,

in_color
.
transmit

>

#end

pigment

{
color

gamma_color_adjust
(
rgb

<
110
,
160
,
008
>
/
255
,

2.2
)}

pigment

{
color

gamma_color_adjust
(
rgbft

<
1
,
1
,
1
,
0
,
1
>,

2.2
)}

pigment

{
color

gamma_color_adjust
(
rgbt

<
1
,
1
,
1
,
1
>,

2.2
)}

pigment

{
color

gamma_color_adjust
(
red

1

green

1

blue

1

transmit

1
,

2.2
)}

light_source

{

-
z

*

40960

color

gamma_color_adjust
(
rgb

<
240
,
240
,
240
>
/
255
,

2.2
)

}
```

### Transparency

Colors with partial transparency (filter and/or transmit) suffer additional degradation that is not resolved by the above fix. For instance, Chris Colefax's lens flare macro. It is not clear yet how to resolve this issue. (See here.)

## See also

- Converting pre-gamma scenes to assumed_gamma = 1? - discussion thread

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Fix unexpected image output

A scene will appear black if:

- You've forgotten to add a camera to your scene.

- You've forgotten to add a light source to your scene (and none of your objects have their own ambience).

- There was an error in an include file and you've pressed <Alt-G> (render) while the include has focus. POV-Ray renders the include file which either has no objects instantiated or no lighting.

- Your camera is pointing away from your scene.

- Your camera is nowhere near your scene.

- Your camera is right up close to a black object.

- Your camera is inside an object with ambient 0 in its finish statement and no light source within.

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Fix unexpected invisibility

### Objects

One thing to check is whether your object is declared AND instanciated in the scene. So in the case of pre declared objects you could often find something like this :

```
// -- The bare necessities are here : Camera & light --

camera

{

location

<
0.0
,

0.5
,

-
4.0
>

direction

1.5
*
z

right

x
*
image_width
/
image_height

look_at

<
0.0
,

0.0
,

0.0
>

}

light_source

{

<
0
,

0
,

0
>

// light's position (translated below)

color

rgb

<
1
,

1
,

1
>

// light's color

translate

<
-
30
,

30
,

-
30
>

}

// ----------------------------------------

// The object to be shown is declared here:

#declare

MySphere

=

sphere

{

0
,

0.5

pigment

{
color

rgb

<
0.9
,
0.5
,
0.3
>}

}

// ----------------------------------------

// But if one ommited the line below or commented it out... :

object

{

MySphere

}

//Sidenote: Some transforms could follow MySphere

//...The object would only be declared but never instanciated
```

### Media

Any emissive media IS totaly transparent by design. It adds to whatever is behind, without filtering anything out. So if your media just disappears over the background, what you can do is :

- To add some absorbing media with `absorbtion<0,1,1>` ...Removes the blue and green, leaving the red.

- Add some scattering media and a light_source.

- Change the pigment to `rgbt<1,1,1,0.9>` or `rgbf<1,1,1,0.9>`

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### High precision heightfields

### Overview

The following is a tentative method of generating 24- and 32-bit heightfields in POV-Ray using standard raster image output formats such as PNG. It is based on some discussion that occurred in povray.general toward the end of 2008.

The technique involves encoding different levels of heightfield precision in each color channel. The first 8 bits of precision are in the blue channel; the second 8 bits in the green channel; and the third 8 bits in the red channel. The channels are then concatenated to produce a single 24 bit number.

The benefit of this technique is that it results in standard 24-bit bitmaps that can be imported into regular image editing software. (By regular image editing software, I actually mean Photoshop and a few other select programs. More on that below.) I think that Leveller uses this format to export its heightfields. Of course, this technique can be extended to 32 bits as well (as you'll see below).

### Sample code

A sample scene with the 24-bit function applied as a pigment to a one-unit-high cone is as follows. You should be able to render the scene and import the resulting image back into POV-Ray to generate a new heightfield.

```
camera
{
 orthographic
 location -z * 10
 direction +z
 up +y * 2
 right +x * 2 * image_width/image_height
 rotate +x * 90
}

#local hf_pigment_function_pow = function
{
 y * pow(256,3)
}
#local hf_pigment_function_b = function
{
 mod(hf_pigment_function_pow(x,y,z), pow(256,1))
}
#local hf_pigment_function_g = function
{
 mod(hf_pigment_function_pow(x,y,z)
 - hf_pigment_function_b(x,y,z), pow(256,2))
}
#local hf_pigment_function_r = function
{
 mod(hf_pigment_function_pow(x,y,z)
 - hf_pigment_function_b(x,y,z)
 - hf_pigment_function_g(x,y,z), pow(256,3))
}

#local hf_pigment_b = pigment
{
 function {hf_pigment_function_b(x,y,z) / pow(256,1)}
 color_map {[0 rgb 0][1 rgb z * 3]}
}
#local hf_pigment_g = pigment
{
 function {hf_pigment_function_g(x,y,z) / pow(256,2)}
 color_map {[0 rgb 0][1 rgb y * 3]}
}
#local hf_pigment_r = pigment
{
 function {hf_pigment_function_r(x,y,z) / pow(256,3)}
 color_map {[0 rgb 0][1 rgb x * 3]}
}

// This is the one you want
#local hf_pigment_rgb = pigment
{
 average
 pigment_map
 {
 [hf_pigment_b]
 [hf_pigment_g]
 [hf_pigment_r]
 }
}

cone
{
 0, 1, y, 0
 texture
 {
 pigment {hf_pigment_rgb}
 finish {ambient 1}
 }
}
```

The 32-bit version of the function (which I haven't tested by importing back into POV-Ray yet) is as follows. Make sure to turn on the alpha channel in the output image by modifying the corresponding INI setting.

```
camera
{
 orthographic
 location -z * 10
 direction +z
 up +y * 2
 right +x * 2 * image_width/image_height
 rotate +x * 90
}

#local hf_pigment_function_pow = function
{
 y * pow(256,4)
}
#local hf_pigment_function_t = function
{
 mod(hf_pigment_function_pow(x,y,z), pow(256,1))
}
#local hf_pigment_function_b = function
{
 mod(hf_pigment_function_pow(x,y,z)
 - hf_pigment_function_t(x,y,z), pow(256,2))
}
#local hf_pigment_function_g = function
{
 mod(hf_pigment_function_pow(x,y,z)
 - hf_pigment_function_t(x,y,z)
 - hf_pigment_function_b(x,y,z), pow(256,3))
}
#local hf_pigment_function_r = function
{
 mod(hf_pigment_function_pow(x,y,z)
 - hf_pigment_function_t(x,y,z)
 - hf_pigment_function_b(x,y,z)
 - hf_pigment_function_g(x,y,z), pow(256,4))
}

#local hf_pigment_t = pigment
{
 function {hf_pigment_function_t(x,y,z) / pow(256,1)}
 color_map {[0 rgbt 0][1 rgbt <0,0,0,4,>]}
}
#local hf_pigment_b = pigment
{
 function {hf_pigment_function_b(x,y,z) / pow(256,2)}
 color_map {[0 rgbt 0][1 rgbt <0,0,4,0,>]}
}
#local hf_pigment_g = pigment
{
 function {hf_pigment_function_g(x,y,z) / pow(256,3)}
 color_map {[0 rgbt 0][1 rgbt <0,4,0,0,>]}
}
#local hf_pigment_r = pigment
{
 function {hf_pigment_function_r(x,y,z) / pow(256,4)}
 color_map {[0 rgbt 0][1 rgbt <4,0,0,0,>]}
}

// This is the one you want
#local hf_pigment_rgbt = pigment
{
 average
 pigment_map
 {
 [hf_pigment_t]
 [hf_pigment_b]
 [hf_pigment_g]
 [hf_pigment_r]
 }
}

cone
{
 0, 1, y, 0
 open
 texture
 {
 pigment {hf_pigment_rgbt}
 finish {ambient 1}
 }
}
```

### Issues

You may notice that when importing the rendered image back into POV-Ray as a heightfield, the heightfield looks less smooth than it really should. This is not due to a deficiency in the technique as far as I can tell, but probably rather due to the gamut POV-Ray (as well as most image editing programs, but excluding Photoshop and a few others) uses when outputting bitmap images. This issue is discussed in more detail here. Heightfield images outputted by Leveller don't seem to suffer from this problem, and I suspect the 16-bit "hf_gray_16" format already natively supported by POV-Ray gets around this issue somehow as well.

Criticisms of this technique include:

- While such images are easy to view in standard imaging software, they are more difficult to modify than a simple grayscale image.

- Such a high level of precision is unnecessary. 16-bit heightfields should be more than sufficient.

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Install POV

- 1 Installation

- 1.1 Windows

- 1.2 Linux

- 1.2.1 Slackware

- 1.2.2 Termux

- 1.3 Mac OS/X

# Installation

Diclaimer : Official instructions to build POV-Ray can be found on the Github POV-Ray source repository (pay particular attention to the list of dependencies):

- In the /unix/README.md file for Unixes

- In the /windows/README.md file for Windows

These two links apply to the 3.8 development version of POV-Ray they ship with... which, by the way, is not yet an official stable version. Rather go here or for this relative to current stable version.
So be aware that the below recipes are in no way official instructions. They may even be outdated, and are just here to be considered as rather particular cases feedback that might come in handy to some lucky users.

### Windows

Binaries are compiled for every new version of POV-Ray and are available on the POV-Ray website.

Running the setup.exe installer with standard options is enough to set POV-Ray up and in working order, though we do recommend
also downloading the editor DLL's when prompted by the installer, as without the editor you will need to use another program
to edit .POV source files.

Some users recommend installing POV-Ray as administrator (available in a menu from a right click on the installer) as the installer does not give you the option itself. The include files and the rest are installed under the profile of the user doing the installing by default. Make sure to explicitly switch the destination to the "Public" folder or your non-admin account during installation.

### Linux

- Download the source from GitHub.

- On older Linux distributions you may have to query-replace march=native to march=corei7 in the configure file (tested with Ubuntu 12.04 LTS 64-bit, gcc 4.6.3, and i7-4770K processor).

- Navigate from a terminal into the folder where you uncompressed it and type:

```
./configure
COMPILED_BY
=
"your name <email@address>"
```

if you get an error, whatever will follow the "configure: error: " is the names of missing libraries on your system, it could be boost, libpng, or anything... Go to your package manager such as Synaptic or whatever, and install each of them preferably the specific version which is asked for. At the end of the configure command, this should be written:

```
Unix man page:/usr/local/share/man
```

- then type:

```
make
make install
```

That's all, you should then have POV-Ray 3.7 installed.

[/content/User:Le_Forgeron/vault/Compilation Here] is a page with more options for compiling.

Slackware

Below is the recipe used to compile POV-Ray for Slackware:

```
cd
 /media/space/software/Source/povray/povray-3.7.0/povray
git config core.sparsecheckout
true

echo
 source/ >> .git/info/sparse-checkout

echo
 unix/ >> .git/info/sparse-checkout

echo
 vfe/ >> .git/info/sparse-checkout

echo
 distribution/ >> .git/info/sparse-checkout

echo
 doc/ >> .git/info/sparse-checkout
git remote add -f origin https://github.com/POV-Ray/povray
git pull origin master

#From https://github.com/POV-Ray/povray

# * [new branch] 3.7-stable -> origin/3.7-stable

# * [new branch] master -> origin/master

# * [new tag] v3.7.0.0 -> v3.7.0.0

#Backup /media/space/software/Source/povray/povray-3.7.0/povray to

/media/space/software/Source/povray/povray-3.7.0/povray.tar.gz

cd
 unix/
../prebuild.sh

cd
 ../
../configure
COMPILED_BY
=
"your name"
 --with-boost-thread
=
boost_thread

#===============================================================================

#POV-Ray 3.7.0 has been configured.

#Built-in features:

# I/O restrictions: enabled

# X Window display: enabled (using SDL)

# Supported image formats: gif tga iff ppm pgm hdr png jpeg tiff openexr

# Unsupported image formats: -

#Compilation settings:

# Build architecture: x86_64-unknown-linux-gnu

# Built/Optimized for: x86_64-unknown-linux-gnu (using -march=native)

# Compiler vendor: gnu

# Compiler version: g++ 4.7.1

# Compiler flags: -pipe -Wno-multichar -Wno-write-strings

-fno-enforce-eh-specs -s -O3 -ffast-math -march
=
native -pthread

#Type 'make check' to build the program and run a test render.

#Type 'make install' to install POV-Ray on your system.

#The POV-Ray components will be installed in the following directories:

# Program (executable): /usr/local/bin

# System configuration files: /usr/local/etc/povray/3.7

# User configuration files: /home/dad/.povray/3.7

# Standard include files: /usr/local/share/povray-3.7/include

# Standard INI files: /usr/local/share/povray-3.7/ini

# Standard demo scene files: /usr/local/share/povray-3.7/scenes

# Documentation (text, HTML): /usr/local/share/doc/povray-3.7

# Unix man page: /usr/local/share/man

#===============================================================================

make -j2
make check
#the biscuit + coffee render fine

#As root

checkinstall
# this does "make install" and also creates a Slackware package

#Edit 3 - Version to be 3.7.0

#This package will be built according to these values:

#1 - Summary: [ povray ]

#2 - Name: [ povray ]

#3 - Version: [ 3.7.0 ]

#4 - Release: [ 1 ]

#5 - License: [ GPL ]

#6 - Group: [ Applications/System ]

#7 - Architecture: [ x86_64 ]

#8 - Source location: [ povray ]

#9 - Alternate source location: [ ]
```

Termux

Below is what was tried and finally used (# is a comment sign) ...to compile some unofficial and official POV-Ray 3.8 for Termux on an armv7 achitecture, (32bits) of the Samsung Galaxy note 3 (both N900A and N9005, respectively with android 5 and 10):
Android versions below 7 do not have X11 repo available and therefore no libxpm. Using --without-x is thus necessary for these versions
...But more recent terminals using android v 7 and above are welcome to remove and test for some way to have the X/sdl based preview working...!

```
termux-setup-storage
```

```

#pkg install x11-repo #no more needed --without-x, theoretically the below libxpm neither
```

```
pkg upgrade
pkg install -y libllvm llvm-tools clang autoconf automake boost imagemagick git pkg-config #libxpm #sdl
git clone https://github.com/POV-Ray/povray.git
```

or to use HGPovray branch, replace the last line with:

```
git clone https://github.com/LeForgeron/povray.git
```

then, get into the directory

```
cd /data/data/com.termux/files/home/povray/unix/
bash prebuild.sh
cd ../
```

```

#--disable-shared creates a portable app with included libraries

#--without-openexr is advised to create non-redistributable only,

# so we still need to find a Termux package with this library.

# also add -libsdl to LIBS flag when testing for the preview display...

# remove --without-x in case the above works
```

```
./configure COMPILED_BY="your name <email@address>" --without-x --disable-io-restrictions LIBS="-lboost_system -lboost_thread" CXX=clang++ CC=clang CFLAGS="-mcpu=native" CXXFLAGS="-mcpu=native"#--disable-shared for portable version
```

then (the"check" is optional):

```
make check
```

the biscuit + coffee render fine.

When trying

```
make install
```

something's wrong with permissions so more work is required.

(DON'T FORGET TO REPLACE your name <email@address> WITH YOUR NAME AND EMAIL)
Termux does not follow Filesystem Hierarchy Standard unlike majority of Linux distributions. You cannot find directories like /bin, /etc, /usr, /tmp and others at usual location. Thus, all programs should be patched and recompiled to meet requirements of the Termux environment otherwise they will not be able to find their configuration files or other data. In the case of POV, This limitation is not a show stopper, since command line accepts all the needed arguments.
But please do share if you know what (probably small) modification would allow the "make install" to work.
Meanwhile a command to launch a render from povray folder working directory looks like this, outputting image to (dcim) camera gallery:

```
./unix/povray +I./distribution/scenes/advanced/grenadine/grenadine.pov +W1080 +H1920 +Fj +L./include +L./distribution/scenes/advanced/grenadine +O$HOME/storage/dcim/grenadine
```

### Mac OS/X

Even though there is a Homebrew package for Persistence of Vision, it is not yet as up to date as the below alternative :

You can download some Mac builds of POV-Ray here:
http://megapov.inetart.net/povrayunofficial_mac/

Intel-based computers with system 10.6.8 and later (included 10.9)
are supported.

Once you have downloaded the zip-file, move it to the desktop and open your Terminal application. Type in the following lines, executing each one by pressing the Return-key:

```

cd
 desktop
unzip POV-Ray3_7_CommandLineMacV2.zip

cd
 PovrayCommandLineMacV2
mv Povray37UnofficialMacCmd povray
sudo mkdir /usr/local/bin
sudo mv povray /usr/local/bin
```

After opening a new Terminal window you are ready to use the command line version of POV-Ray, by just typing the command:

```

povray
```

which should give you the standard POV-Ray startup messages.
To do batch renders:

```

povray render_one.ini
;
 povray render_two.ini
```

Should you need to pause a render, then use the following command:

```

killall -STOP povray
```

To resume the rendering again, use:

```

killall -CONT povray
```

You have a render that needs to be done with a high(er) priority than the render(s) you have currently running? Simple, in a new Terminal window, type the following line:

```

killall -STOP povray
;
 povray priority_render.ini
;
 killall -CONT povray
```

And last but not least: you can use "Fire and forget" renders! Normally you would have to keep the Terminal window open while a render is in progress, however, when you type:

```

tcsh
```

And then type the following line:

```

povray test.ini
;
 zip -m test.zip *.tga
;
 uuencode test.zip test.zip
|
 /usr/bin/mail -s
"Rendered Image"
 your_email@address.com
&
```

You may now close the Terminal window if need be, as long as the computer stays on, the render will continue in the background. You will even get the resulting file sent to you in your email!

Or you can have a little fun too, having your Mac talk to you, when a render has finished, by telling you so:

```

povray test.ini
;
 say -v samantha
"Your render has finished"
```

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Known Issues

### Known Issues

Quick list of general known issues with the version 3.7, the 3.7.1 pre-release and the current master branch(3.7.2):

- Unix users. Due to problems with X11 window libraries, some special command line parameters, such as -geometry, are not available. These were previously enabled via X11 itself. However, a preview display can still be enabled if building from source and the optional SDL Simple Directmedia Layer (V1.2) development library is available to link against.

- While utf-8 support is available in scene files, there are still issues with file names using non-ASCII characters. Meaning a user running in utf-8 or equivalent must take care to use only ASCII characters for all scene related file and directory names. See: github issue 152

- Agressive Anti-Aliasing (AA) usually addresses bright and dark speckles sometimes seen with media where rays glance - tangentially - to a mathematical surface in a way which confuses interval determination for sampling. Further, while using 'sturm on' is usually a help, it tends to make such media speckling worse. Search the newsgroups for more detail.

- The internal .gray | .grey, color to gray conversion is not adjusted for assumed_gamma as the current conversion was set up for other than the recommended assumed_gamma 1 setting. See the newsgroup discussion at: "Assumed_gamma and .gray". The following macro, as suggested in the news thread, can be used by assumed_gamma 1 users:

```
#macro DotGray (Color)
 #local C = color Color;
 (0.2126 * C.red + 0.7152 * C.green + 0.0722 * C.blue) // ITU-R BT.709
#end
```

The developer's issue tracking can be found at Github Issue Tracking System

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Make A Bug Report

- 1 Reporting bugs in POV-Ray

- 2 Guidelines for opening new tasks

- 3 Text formatting tips

- 4 Miscellaneous

- 5 See also

Reporting bugs in POV-Ray

Bug reports are an essential part of development, and the developers need you to let them know about programming defects in POV-Ray. However, there are a few things that you need to keep in mind when you are thinking about submitting a bug report or requesting a new feature. Please take a few moments to read this brief list of guidelines before you open a new task in our Github Issue Tracking System.

Note: All issues still open in our previous FlySpray Bug Reporting System have been ported to the new Github Issue Tracking System. Please exclusively use Github for new issues and updates to open issues.
Guidelines for opening new tasks

- Take some minutes of your time and read this document, it will teach you how to report a bug correctly.

- If you're not sure it's a bug, or how to report it, chat with the folks in the beta-test forum.

- We cannot accept bug reports for modified versions of POV-Ray.

- In almost all cases where a rendering bug is being reported, we require a minimal sample scene causing the bug be submitted, as well as an image showing the problem.

- Always perform a search on both open and closed 'issues' or 'tasks' before opening a new one. Your bug might already have been reported, or your feature requested.

- Use the English language in any tasks you open. Nothing will get done if we can't understand you.

- Only report one bug or request one feature per task.

- If you report a bug, and we mark it as 'requires testing', then please test it and add a comment to the task telling us how well it works.

- If a task is closed as fixed then try the development version before requesting that it be re-opened.

- If we deny your request to re-open a task, check the history/comments for our reason. If you are unsatisfied with the reason, you are welcome to discuss it in the forums.

- Be polite. Remember that you got POV-Ray for free.
Text formatting tips

Github allows for a lot more formatting within task descriptions and comments. Here are a few helpful essentials:

- To quote some other comment, use the github quote feature after marking the text to be quoted.

- To make code, use the mark code feature of github after marking your code.

- Refer to github issues and pull request by number prefixed with # and github will create the link. Please refer to older Flyspray tasks by their numeric id preceded by "FS#" (e.g. FS#123) and perhaps insert the URL for as github will create the corresponding link.

- Do make use of the preview button.
Miscellaneous

- To illustrate your bug report or comment with images, test scenes or zip files, please do make use of the "attach file" functionality, rather than linking to some 3rd party image hosting site. This way the uploaded files will remain accessible as long as the github issue persists. Note when uploading scene related files the POV-Ray extensions of .pov and .inc are not supported; needing an additional .txt suffix before upload. Further .tar and .gz files are not supported as of this writing, but rather only .zip.

## See also

- Knowledgebase:POV-Ray Feature Requests

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Manage your variables

The requirements page for the POV-Ray Object Collection has some good recommendations:

Variable names exposed to the user should also be prefixed with the contribution name or the unique prefix and should be directly followed by an underscore ('_'). This includes named objects, textures, pigments, normals etc. For example the 'Netball' object exposes the NB_BallRadius variable to enable the radius of the ball to be controlled. You may use the #local directive to define variables with a local context within macros etc., in which case you are not obliged to adhere to this naming standard within those macros, because the local context should avoid naming conflicts.

By following these recommendations you ensure that your objects can be used in others' scenes with minimal modification.

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Manual focal blur

From a discussion on the newsgroups:

"I render the scene multiple times (often 200, 400 or more times), but each pass
has a very simplified, but at the same time completely randomised, set of
paramters. When all those renders are summed together into one image, the random
elements all combine to give a final scene that looks like one with a much more
complex set of parameters.

Critically, when there are multiple "features" that all usually interact to push
render times sky-high, I try to construct a scene that has the interaction as
low as possible, and can render a few hundred images very quickly (each pass
taking anything from 10 seconds to maybe a minute or two).

On multi-core machines I simply fire off as many POV processes as I have CPUs
(or is that ALUs?).

I really must post a set of examples of each kind of effect that created with
the technique, and then some examples of combining them all together.

A really really old example of multiple area lights, blurred floor reflection,
anti-aliasing, focal blur and blurred refraction all done with stochastic
rendering:

One pass: http://www.flickr.com/photos/26722540@N05/3094525065/

400 passes: http://www.flickr.com/photos/26722540@N05/3094524731/

Cheers,

Edouard."

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Migrate old scenes to work with the new gamma system

Note: The title of this page and part of its contents are somewhat outdated, as POV-Ray 3.7.0 proper will now support virtually full backward compatibility for legacy scenes, and in this sense there will be almost nothing to fix. The information in this page rather applies to migrating a scene to use the physically realistic `assumed_gamma 1.0`

Before "assumed_gamma 1.0" or the most recent 3.7 betas, the colour math was simply wrong. So in order to get exactly the same result, you'd have to mimic that wrong colour math - which 3.7 refuses to, except for legacy scenes using "#version 3.6" (or anything smaller than 3.7) and "assumed_gamma 2.2".

If you do want to port the scene to the right math, here's what I expect to be the most important steps:

- In the ini-file, set the "Display_Gamma" to whatever fits your system; set "File_Gamma" to the same. (Ideally, your system should have a Display_Gamma of 2.2.)

- In the scene file, use "#version 3.7", and remove any "assumed_gamma" statement.

- Gamma-adjust all colour literals, by raising the R,G,B components to the power of 2.2 (or wait for the next beta, and use "srgb" instead of "rgb"). Check your scene for colour values that are computed from some other values - you may (or may not) want to gamma-adjust these as well.

- Check your scene for input image files, and whether they are handled properly regarding gamma. For images that are used as anything non-color (e.g. height field data, bump map, texture map or what-have-you), explicitly specify "gamma 1.0" after the filename. For any images that do wind up being used as a colour, try the default first (i.e. no "gamma" statement); if the colours appear too washed-out or too strong, try explicitly specifying "gamma srgb".

- Toy around with overall brightness and/or brightness of individual light sources, until you feel comfortable with the illumination. You may also want to tweak some of the colours in your scene (both pigment and light sources). If you're using self-made input image files, you may also want to tweak their gamma setting, but try to avoid this with 3rd party input images (you should usually find a way to make them work by tweaking the scene rather than the image).

### Color literals

As of version 3.7.0.beta.41, POV-Ray natively supports gamma-adjustment of color literals, provided they conform to the sRGB standard:

```
pigment

{
color

srgb

<
110
,
160
,
008
>
/
255

}

pigment

{
color

srgbft

<
1
,
1
,
1
,
0
,
1
>

}

pigment

{
color

srgbt

<
1
,
1
,
1
,
1
>

}
```

If you are still using version 3.6, or need more flexibility of the gamma parameter:

```
#macro

gamma_color_adjust
(
in_color
,

out_gamma
)

rgbft

<

pow
(
in_color
.
red
,

out_gamma
),

pow
(
in_color
.
green
,

out_gamma
),

pow
(
in_color
.
blue
,

out_gamma
),

in_color
.
filter
,

in_color
.
transmit

>

#end

pigment

{
color

gamma_color_adjust
(
rgb

<
110
,
160
,
008
>
/
255
,

2.2
)}

pigment

{
color

gamma_color_adjust
(
rgbft

<
1
,
1
,
1
,
0
,
1
>,

2.2
)}

pigment

{
color

gamma_color_adjust
(
rgbt

<
1
,
1
,
1
,
1
>,

2.2
)}

pigment

{
color

gamma_color_adjust
(
red

1

green

1

blue

1

transmit

1
,

2.2
)}

light_source

{

-
z

*

40960

color

gamma_color_adjust
(
rgb

<
240
,
240
,
240
>
/
255
,

2.2
)

}
```

### Transparency

Colors with partial transparency (filter and/or transmit) suffer additional degradation that is not resolved by the above fix. For instance, Chris Colefax's lens flare macro. It is not clear yet how to resolve this issue. (See here.)

## See also

- Converting pre-gamma scenes to assumed_gamma = 1? - discussion thread

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### ODS

Omni­directional stereo (ODS) is a projection model for stereo 360 degree videos.
It’s designed for VR viewing with a head­mounted display (HMD).
More information in this Google PDF.

## Contents

- 1 How to render

- 2 Caveats

- 3 Best practice

- 4 Recommended Resolutions

- 5 Rendering Animation/Video

- 6 Interesting Links

- 7 QUICKRES.INI

# How to render

Currently (2016-03-11), render ODS image require an alpha version of POV-Ray, that support user_defined camera. Download from here

Use the following code for an ODS top-bottom (left eye on top, right eye on bottom):

```
// ODS Top/Bottom

#declare

odsIPD

=

0.065
;

// Interpupillary distance

#declare

odsVerticalModulation

=

0.2
;

// Use 0.0001 if you don't care about Zenith & Nadir zones.

#declare

odsLocationX

=

0
;

#declare

odsLocationY

=

0
;

#declare

odsLocationZ

=

0
;

#declare

odsHandedness

=

-
1
;

// "-1" for left-handed or "1" for right-handed

#declare

odsAngle

=

0
;

// Rotation, clockwise, in degree.

camera

{

user_defined

location

{

function

{

odsLocationX

+

cos
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
(
select
(
y
,

1
-
2
*
(
y
+
0.5
),

1
-
2
*
y
)
*
pi
),

odsVerticalModulation
))
*
select
(
-
y
,
-
1
,
+
1
)

}

function

{

odsLocationY

}

function

{

odsLocationZ

+

sin
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
(
select
(
y
,

1
-
2
*
(
y
+
0.5
),

1
-
2
*
y
)
*
pi
),

odsVerticalModulation
))
*
select
(
-
y
,
-
1
,
+
1
)

*

odsHandedness

}

}

direction

{

function

{

sin
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
select
(
y
,

1
-
2
*
(
y
+
0.5
),

1
-
2
*
y
)

*

pi
)

}

function

{

sin
(
pi

/

2

-

select
(
y
,

1
-
2
*
(
y
+
0.5
),

1
-
2
*
y
)

*

pi
)

}

function

{

-
cos
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
select
(
y
,

1
-
2
*
(
y
+
0.5
),

1
-
2
*
y
)

*

pi
)

*

odsHandedness

}

}

}
```

Use the following code for an ODS side-by-side:

```
// ODS Side-by-Side

#declare

odsIPD

=

0.065
;

// Interpupillary distance

#declare

odsVerticalModulation

=

0.2
;

// Use 0.0001 if you don't care about Zenith & Nadir zones.

#declare

odsLocationX

=

0
;

#declare

odsLocationY

=

0
;

#declare

odsLocationZ

=

0
;

#declare

odsHandedness

=

-
1
;

// "-1" for left-handed or "1" for right-handed

#declare

odsAngle

=

0
;

// Rotation, clockwise, in degree.

camera

{

user_defined

location

{

function

{

odsLocationX

+

cos
(((
select
(
x
,
2
*
(
x
+
0.5
),
2
*
x
)
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
((
1
-
(
y
+
0.5
))
*
pi
),

odsVerticalModulation
))
*
select
(
x
,
-
1
,
1
)

}

function

{

odsLocationY

}

function

{

odsLocationZ

+

sin
(((
select
(
x
,
2
*
(
x
+
0.5
),
2
*
x
)
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
((
1
-
(
y
+
0.5
))
*
pi
),

odsVerticalModulation
))
*
select
(
x
,
-
1
,
1
)

*

odsHandedness

}

}

direction

{

function

{

sin
(((
select
(
x
,
2
*
(
x
+
0.5
),
2
*
x
)
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
(
1
-
(
y
+
0.5
))

*

pi
)

}

function

{

sin
(
pi

/

2

-

(
1
-
(
y
+
0.5
))

*

pi
)

}

function

{

-
cos
(((
select
(
x
,
2
*
(
x
+
0.5
),
2
*
x
)
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
(
1
-
(
y
+
0.5
))

*

pi
)

*

odsHandedness

}

}

}
```

```
// ODS Single Eye

#declare

odsIPD

=

0.065
;

// Interpupillary distance

#declare

odsVerticalModulation

=

0.2
;

// Use 0.0001 if you don't care about Zenith & Nadir zones.

#declare

odsLocationX

=

0
;

#declare

odsLocationY

=

0
;

#declare

odsLocationZ

=

0
;

#declare

odsHandedness

=

-
1
;

// "-1" for left-handed or "1" for right-handed

#declare

odsAngle

=

0
;

// Rotation, clockwise, in degree.

#declare

odsEye

=

-
1
;

// -1 for Left eye, +1 for Right eye

camera

{

user_defined

location

{

function

{

odsLocationX

+

cos
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
((
1
-
(
y
+
0.5
))
*
pi
),

odsVerticalModulation
))
*
odsEye

}

function

{

odsLocationY

}

function

{

odsLocationZ

+

sin
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)
*
(
odsIPD
/
2
*
pow
(
sin
((
1
-
(
y
+
0.5
))
*
pi
),

odsVerticalModulation
))
*
odsEye

*

odsHandedness

}

}

direction

{

function

{

sin
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
(
1
-
(
y
+
0.5
))

*

pi
)

}

function

{

sin
(
pi

/

2

-

(
1
-
(
y
+
0.5
))

*

pi
)

}

function

{

-
cos
(((
x
+
0.5
+
odsAngle
/
360
))

*

2

*

pi

-

pi
)

*

cos
(
pi

/

2

-
(
1
-
(
y
+
0.5
))

*

pi
)

*

odsHandedness

}

}

}
```

# Caveats

- Camera Direction is actually not supported. It’s always look_at<0,0,1>. You can use the odsAngle parameter for a rotation (degree) around Y axis.

- Zenith & Nadir Zones

Base ODS algorithm have spiral/singularities towards the zenith and nadir points.To avoid that, i modulate the stereoscopic eye separation such that it begins at normal eye separation near the horizon, and is smoothly decreased, reaching zero by the time either the zenith or nadir points on the polar axis are visible to the user, producing a monoscopic image.
I use this formula:

where 0.02 it’s the odsVerticalModulation and 0.065 the default IPD. Play with this value to understand how IPD are reduced near the Zenith (x:-0.5) and Nadir (x:0.5).

In general, it’s recommended to avoid objects at zenith & nadir points, and use a odsVerticalModulation near 0 (0.0001), to obtain a perfect IPD / 3d effect.
If you have objects at zenith & nadir points, use a odsVerticalModulation near 1 can be a good compromise.

Other approach:
Domemaster3D (Shader for 3DS Max, Maya, Softimage etc) recommend a texture to reduce the effect. link

SolidAngle/Arnold use mixed approach. link

Another kind of modulation: link

# Best practice

- Objects should remain at least 60cm from the camera (relative to an IPD of 6.5cm).

Use this code to check (it’s auto adapt based on IPD):

```
sphere

{

<
odsLocationX
,
odsLocationY
,
odsLocationZ
>,

0.6
*
odsIPD
/
0.065

pigment

{

color

<
1
,
0
,
0
>

filter

0.97

}

hollow

}
```

- Objects appearing directly above or below the camera should remain at least 3.5m from the camera (relative to an IPD of 6.5cm).

- Antialiasing is very very very important on VR headset.

# Recommended Resolutions

I recommend, at least for the current (year 2016) generation of VR headset (GearVR, Oculus Rift, HTC Vive), at least 6480 x 6480 pixels in top/bottom for static images. For videos, see below.

Resolution must have 2:1 aspect ratio (standard equirectangular/latlong), that become 4:1 for side-by-side or 1:1 for a top-bottom.

It’s difficult to estimate a good resolution.
VR headset do a distortion for lenses, any every VR headset can have different lenses, different FOV, different panel resolution etc.

The GearVR for example has a 90° FOV on a 2560×1440 panel, but the center pixel covers 0.06° after distortion. This value is sometime called “pixel coverage” or “pixel density” or “pixel per display pixel” or “eye buffer scaling”. So, for the GearVR, 0.06° pixels means we need 360/0.06 = 6000 pixels to cover one monoscopic turn.

# Rendering Animation/Video

This is actually problematic.

In theory, the resolution must be at least as explained above for images.

Any VR headset works with high frame-rate:

Oculus Rift DK2 (Development Kit 2): 75 FPS
Oculus Rift CV1 (Customers Version 1): 90 FPS
HTC Vive: 90 FPS
Sony Playstation PSVR : 120 FPS
In general, future-generation VR headset: expected 120 FPS

But H264 don’t have any level profile compatible with this kind of resolution.
Also HEVC/H265 have the same problem.

But we also need a coded that is hardware-accelerated to obtain the high FPS requested, and generally only H264/H265 are optimized for this.

Anyway, if you build a video at 30 FPS, for example if played in a 90 FPS HMD it don't cause nausea (because the head-tracking still works at 90 FPS).

# Interesting Links

POV-Ray newsgroup, topic about the approach

POV-Ray newsgroup, topic about contents

Oculus Forum Topic

Renderman/Pixar approach

Bernie Roehl POV-Ray approach

Blender approach

Scott Singer approach

# QUICKRES.INI

Reference resolutions for POV-Ray quickres.ini

```
[
ODS

TB

Quick

Test

-

512

x

512
]

Width
=
512

Height
=
512

Antialias
=
Off

[
ODS

TB

Test

-

1024

x

1024
]

Width
=
1024

Height
=
1024

Antialias
=
Off

[
ODS

TB

Minimum

-

3600

x

3600
]

Width
=
3600

Height
=
3600

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

High

-

6480

x

6480
]

Width
=
6480

Height
=
6480

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

Ultra

-

12288

x

12288
]

Width
=
12288

Height
=
12288

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

1440
p

-

2560

x

1440
]

Width
=
2560

Height
=
1440

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

UHD
-
1

2160
p

-

3840

x

2160
]

Width
=
3840

Height
=
2160

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

DCI

4
K

-

4096

x

2160
]

Width
=
4096

Height
=
2160

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

8
K

UHD

-

7680

x

4320
]

Width
=
7680

Height
=
4320

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

Minimum

-

7200

x

1800
]

Width
=
7200

Height
=
1800

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

High

-

12960

x

3240
]

Width
=
12960

Height
=
3240

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

Ultra

-

24576

x

6144
]

Width
=
24576

Height
=
6144

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

Low

-

7200

x

1800
]

Width
=
7200

Height
=
1800

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

Normal

-

12960

x

3240
]

Width
=
12960

Height
=
3240

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

LR

High

-

24576

x

6144
]

Width
=
24576

Height
=
6144

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

Low

-

3600

x

3600
]

Width
=
3600

Height
=
3600

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

Normal

-

6480

x

6480
]

Width
=
6480

Height
=
6480

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

High

-

12288

x

12288
]

Width
=
12288

Height
=
12288

Antialias
=
On

Antialias_Threshold
=
0.3

[
ODS

TB

YouTube

-

3840

x

2160
]

Width
=
3840

Height
=
2160

Antialias
=
On

Antialias_Threshold
=
0.3
```

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### POV-Ray Feature Requests

We all want to see POV-Ray improved, and from time to time it may occur to us that there's a feature that we would like to see, or something we'd like to see improved. The proper place for "feature wishlists" is the bugtracking and feature-request system on Github, as that's the primary place where the developers will look for them.

Note: All issues still open in our previous FlySpray Bug Reporting System have been ported to the new Github Issue Tracking System. Please exclusively use Github for new issues and updates to open issues.

When making feature requests, a word of advice:

Please do read HowTo:Make A Bug Report and How to Report Bugs Effectively before submitting any entries to the issue tracker, and do heed the advice presented there. By following those rules - or at least keeping them in mind as you file issues - you make life a lot easier for the people you hope to address the issues.

Some other points raised in [1] and [2]:

- If discussion of a feature request has resulted in a summary or conclusion that differs from the original request, a new feature request should not be made. Instead, the existing one should be changed. This implies contributing ideas to the feature request discussion, and then coming up with a single coherent idea, not leaving it at two half-finished ideas.

- Closely-related feature requests should be grouped together; e.g. requests for Windows UI enhancements with respect to the editor such as "Delete", "Reload" and "Rename" commands could reasonably be put into a single request (all at once or via editing a bug later).

- There is a feature to allow other users and/or developers to comment on bugs & feature requests on github, but this is primarily intended for providing additional information, or feedback on the current status. For proper discussions, it lacks the necessary publicity. (When was the last time anyone just browsed the github just to see if there was some interesting discussion going on in some of the entries?) The POV-Ray community is strongest in the newsgroups. That said, the problem with discussions about feature requests is that they tend to have no clear-cut result whatsoever, as they tend do be free brainstorming sessions - unless there's someone with the necessary will and authority (e.g. because he has started the discussion in the first place) "driving" the discussion to a proper conclusion (ideally by summarizing the discussion and result in a proper place). In situations like these, github excels.

- A sensible rule for opening a new feature request might then be that it must be discussed in the newsgroups first, to weed out crackpot ideas and to refine ideas by feedback from what users actually need. After discussion, the request should only be entered if a member of the POV-Ray team deems the request reasonable (note that a request should not be deemed unreasonable at this point just because it would be too much effort to implement right now).

- Excessive use/misuse of the newsgroups and github may be considered "vandalism".

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Plan your scenes

- 1 Planning Your Scenes

- 2 What

- 3 How

- 3.1 Objects

- 3.1.1 Position

- 3.2 Colors

- 3.3 Camera Angle

- 3.3.1 Action

- 3.3.2 Perspective

- 3.4 Lighting

- 4 Sketching

- 5 Finalizing

- 6 Building

- 7 Compiling

- 8 Notes

## Planning Your Scenes

There's really a lot of different ways to do this. Some people have best practices that don't exactly coincide with others. Here's a few.

## What

The biggest thing in a part of an image is most notably, what. The picture has to be about something, or OF something. So the first thing one would need to decide for themselves, is what type of image do they want to portray. Here's a few questions you can ask yourself to garner some ideas.

- Is it an object, an idea, or a full fledged scene?

- Does it have a mood you wish to get across?

- Do you want your picture to have a purpose or a message?

- Is your image abstract?

- If So, go back to 2 and 3 and think about what those would be.

- Is it a depiction of real life, or some fantasy?

These are all questions artists in general, from painters to writers, have to think about when starting a new project. Do you want to be accurate? Realistic? Phantasmagoric? What?

## How

As soon as you figure out what you want to say, you gotta figure out how you want to say it. This is the real meat of actually planning a scene and involves many steps.

### Objects

What objects are you gonna have in your scene? Is it just a simple Wine Goblet? Or a re-envisioning of a WWII battle? Figure out what you want to use to express your idea.

#### Position

An interesting question is to ask yourself, what's the best way to show off the most detail of this object and still get the message across? Positioning, along with everything else is key to getting an idea or a point across. Instead of having a birds-eye view on an old west gun battle, where the guys are more spread out and buffered from each other, group them up nice and close, make them seem almost personal with each other, and then you can mess with the camera angle some more to better get your emotion across.
But then again, sometimes close-knit groups aren't what's meant to be. Some of the most renowned art out there is just of one person, a face, a profile, streaks on canvas that convey a message so powerful that you just want to re-enact it yourself.

### Colors

The biggest role in conveying a message or feeling in an image by far is the colors. It's what people first see when they look at an image. They don't focus in on the objects until something has caught their eye. With this, comes an inherently good knowledge of the color wheel. Reds are cooled by purples, but warmed by oranges. The opposite of Blue is orange and can be used to warm up the scene a little bit. The hardest part about figuring out colors however, is trying to find the best balance out there. There's more than a lot of taste involved in this decision, and is in general left up to personal feelings at the time of conception.

### Camera Angle

Not as big as colors, and just a bit more important than light setup, is camera angle. A jarring, new, weird angle is more likely to get someone's attention, than an angle of say, top-down.

#### Action

The biggest thing that comes up when deciding camera angle, is action. Is there movement? Is there something happening? Is it just a still life? Not every case needs an angle from the ground up with the camera tilted at 37 degrees around the looking angle. Yet a slight... Jarring of the camera, if you will can do wonders for making a dull, boring picture actually seem interesting.

#### Perspective

Perspective is huge. Something in the background can help the eye of the viewer flow around the image and notice the true beauty of the image itself, despite the biggest focus being on the object in the foreground. Sometimes a small distortion, like the use of a macro-lens effect can do wonders for bringing focus onto an object, but still keeping something going on in the background interesting.

### Lighting

If it weren't for Camera Angle, Lighting would be right up there with just objects and colors in general. Sometimes colors are even governed by lights, you never know. The reflection of a light blue light versus the dull tinted yellow refraction of a sun can add volume to a once flat object. Shadows can add a lot of drama to an image, half a face hidden in the dark is much more scary and tense-feeling in general than the full face viewable in the light.

## Sketching

To a lot of professional artists, be they 3D game artists, or starving artists who paint, most of the planning comes through sketching of what they invision. It's much easier to just makes strokes of a pencil, no matter how rough, to just conceptualize an object or an image layout. That way they get the basic idea out of their head and immortalized on paper, and from there, they can improve on it, change things around, and make things better in general. At this stage, you have something called Concept Art.

## Finalizing

The final step of sketching, you finalize what you want, what you don't want, and how you want it. The steps from Sketching to Finalizing are taking into consideration the elements of the image, and everything under the "how" section.

## Building

After all that hard work of building your scene in your head and forcing onto the page this image and continually making it better and more awesome, it's time to take it all apart. Take every object in the scene and figure out how you're gonna build it. Whether that's through an external automatic mesh builder (for a tree or a bush), or by hand doing the math yourself, is up to you. There's a group of people that says you build it by hand or not at all, and another group that automatic is easier, and then the majority are the ones that balance the two. This step also includes things like texturing, and testing out how it looks combined in certain areas.

## Compiling

After you have everything in your scene built up to how you want it (make sure you use the same units when measuring the sizes), it's time to bring it all together. This is where your concept art comes back into play. Following that you build your original version of the scene, and then repeat the process of refining and improving your scene. Something you thought would be awesome actually isn't and needs a different texture, a different placement or to just be removed all together. Maybe the camera angle was off and it needs to change. The lighting doesn't portray the right mood and needs to be changed to be from dusk-like to mid-morning.

## Notes

- Please remember, this is only a general guide, and does not have to be followed to the step. Sometimes scenes can be so easy, the sketching and the planning isn't even needed. It could be as simple as making a sword, and then portraying that in a way that it looks awesome. You don't need to sketch that out, because in general the image in your head won't change.

- The true thing about making a good image is not in the planning. It's in your love for the image, and your fun in doing it. You'd be surprised how many images look like crap because someone just doesn't wanna do it. The flip side is that you get practice and more thoughts and ideas on how to make images better, which is essential to getting to a high level of excellence.

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Take full advantage of the Insert menu

This article is for users of all levels of experience, and applies to Win32-bit and Win64-bit POV-Ray

As noted in the documentation, it is possible to edit the Insert Menu. This is not an insignificant thing! Adding your own code to the Insert Menu or changing existing code to better suit your needs can make setting up a scene much easier.

## Contents

- 1 About the Insert Menu

- 1.1 Editing the Insert Menu

- 1.2 Sorting Your Menu Items

- 1.3 Adding a Separator

- 1.4 Insert Menu Pictures

- 2 Changes You Should Consider Making

- 2.1 Making Changes to Fit Your Coding Style

- 2.2 Adding Your Own Blocks of Code

## About the Insert Menu

 From the documentation:

### Editing the Insert Menu

The Insert Menu is built dynamically from a directory called 'Insert Menu', which is in the POV-Ray for Windows installation directory. Each sub-menu in the Insert Menu is a directory (also called a 'folder' these days). To create a new sub-menu, just create a new directory. The submenu will have the same name as the directory.

The actual items that you can insert in your scene files are simply text files. POVWIN recognizes them by the extension .TXT. Any .TXT file in the Insert Menu directory tree will appear in the Insert Menu. The name shown in the Insert Menu will be the filename of the .TXT file, minus the .TXT extension. If you select this file from the Insert Menu, the contents of the corresponding text file will be inserted into your scene file at the current cursor location.

You can edit the Insert Menu at any time, even while POVWIN is running, and POVWIN will automatically pick up the fact that it's been changed and re-load it.

### Sorting Your Menu Items

It is possible to order the insert menu entries any way you like, both for folders and for the files (or sub-folders) within them. Normally, the entries are added to the menu in alphabetical order, with all folders being added before files. However, you can influence the ordering of each group (folders and files) by prepending a 'NN - ' to a folder or file, where 'NN' is any letter or digit (note however that this does not permit you to place a file before a folder).

When the editor code builds the insert menu (which happens each time you change it), any file or folder that starts with a prefix of 'NN - ' (note the trailing space) will be sorted using the prefix, but displayed without the prefix.

### Adding a Separator

Creating a file called '-.txt' (a hyphen followed by .txt) usually prefixed by a 'NN - ' (e.g. '30 - -.txt'), will cause a menu separator to be drawn instead of inserting a new item into the menu. The contents of the file are ignored.
To create a separator between sub-menus, do the same thing as above, but create a folder instead of a file and leave off the '.txt' extension.

### Insert Menu Pictures

Each Insert Menu text file may optionally have an associated BMP file that provides a preview of the item that the text file can generate. To add this for your own changes, place a BMP file that shares the same name as the text file for your menu item.

Preview images can be of any bit-depth, but if you want to distribute them we strongly recommend that they be no more than 8-bit. Additionally, they should be no larger than 640x480 pixels.

## Changes You Should Consider Making

If you use POV-Ray frequently or just want to save time while composing scenes, here are some areas that you might benefit from changing.

### Making Changes to Fit Your Coding Style

Over time I've developed some strong preferences when it comes to coding styles that I feel enhances a scene's readability, and I am sure others have also. When adding a lot of things to your code at once, reorganizing and cleaning up your code can take a while. With that in mind, why not change the code you frequently insert to fit your style?

Example: Insert Menu's standard version of a point light_source

```
// create a regular point light source

light_source

{

0
*
x

// light's position (translated below)

color

rgb

<
1
,
1
,
1
>

// light's color

translate

<
-
20
,

40
,

-
20
>

}
```

My version of a point light_source, changed to match my coding style and the way I typically use them in a scene:

```
//**** SUN (light source name)

light_source

{

<
100
,

100
,

100
>

*

10

color

rgb

<
1
,
1
,
1
>

}
```

I prefer to use tabs instead of spaces for indents and I don't need the comments anymore because I know what the parameters do.

This may seem like a very minor change, but cleanliness and uniformity in code helps to increase readability. If you change all of the code you frequently insert to match your style, it will be much easier to rapidly enter understandable code.

### Adding Your Own Blocks of Code

For starters, I recommend either adding your own top level menu item with submenus, or appropriating the Scene Templates menu.

Open the most recent POV-Ray scene you were working on and look it over. How much of it is reused code from previous scenes? How much of it can be reused for other scenes? Wouldn't it be better to take that really nice looking sky you have now and put it in the Insert Menu instead of trying to find it later to copy and paste it?

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Turn a continuous function into a stepped function

- 1 Overview

- 2 The initial function

- 3 Creating the stepped function

- 4 Viewing the results

## Overview

Normally, functions are evaluated so that they transition smoothly from start to finish with no apparent pixelation. However, sometimes you want functions to evaluate in such a way such that the interpolation between steps is apparent. For instance, maybe you are rendering the graph of a function and you want to make it obvious to the viewer what the function's intervals are. In this case you want to use a stepped function.

## The initial function

Before you can create a stepped function, you have to define the function the stepped function is based upon. For this example we will create a function that is used as a pattern in a pigment. Let's define a simple gradient that extends along the vector, x + y.

```
#declare

ThePattern

=

function
(
x
,

y
,

z
)

{

x

+

y

}
```

This is equivalent to a gradient pattern. We could also have used this:

```
#declare

ThePattern

=

function

{

pattern

{

gradient

x

+

y

}

};
```

Now that we've defined the initial function we can create the stepped function.

## Creating the stepped function

The basic idea behind creating a stepped function is to evaluate the original function in such a way that the values get rounded somehow. We determine how and how much a function gets rounded by specifying how many steps, or intervals, we want for every unit of POV space. We then multiply the function by this number, round it to the nearest integer, and divide the function by the same number you multiplied it by. Let's say we wanted three steps. Then, we would multiply the whole function by three, round it to the nearest integer, and divide by three.

```
#declare

SteppedPattern

=

function

{

floor
(
3

*

ThePattern
(
x
,

y
,

z
))

/

3

};
```

Let's interpolate across ten steps instead of three. In this case, do as follows.

```
#declare

SteppedPattern

=

function

{

floor
(
10

*

ThePattern
(
x
,

y
,

z
))

/

10

};
```

## Viewing the results

The next step is to render the image. For that we will use the following scene which contains our initial function and the stepped version applied to two boxes on a blue background.

```
#default

{

finish

{

ambient

1

}

}

background

{

rgb

z

*

0.5

}

camera

{

location

<
0
,

0
,

-
10
>

look_at

0

angle

35

}

#declare

ThePattern

=

function

{

x

+

y

};

#declare

SteppedPattern

=

function

{

floor
(
10

*

ThePattern
(
x
,

y
,

z
))

/

10

};

// Original pattern:

box

{

<
-
1
,

-
2
,

0
>,

<
1
,

2
,

.1
>

pigment

{

function

{

ThePattern
(
x
,

y
,

z
)

}

color_map

{

[
0

rgb

0
][
1

rgb

1
]

}

scale

2

}

translate

-
x

*

1.1

}

// Stepped pattern:

box

{

<
-
1
,

-
2
,

0
>,

<
1
,

2
,

.1
>

pigment

{

function

{

SteppedPattern
(
x
,

y
,

z
)

}

color_map

{

[
0

rgb

0
][
1

rgb

1
]

}

scale

2

}

translate

x

*

1.1

}
```

The result should look something like this:

The pattern on the left is the original, smooth pattern. The pattern on the right is the same pattern, rounded to ten intervals.

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use POV-Ray with Blender

| ▼ Users | | Developers ► |

- POV-Ray Site

- Exporter Quick Start

- POV-Ray Documentation

- Sample Materials

- POV-Ray centric default workspace

- ---

- ---

- ---

- ---

- ---

- ---

- ---

- ---

- design ideas

- regression files

News|Chat|RSS|Forum

#### Features

Some key features of the exporter include:

- Geometry import and export with their modifiers, keyed and physics animation

- Add POV-Ray specific non mesh primitives (never show tesselation)

- Instances

- Hair particles

- Smoke simulations

- Atmospheric media (volume fog)

- HDRI environment mapping

- Aperture depth of field

- Material properties such as filtering, emission, translucency, subsurface scattering, glass fog (tinted absorption), blurry/glossy reflections...

- Procedural textures (emulated from Blender Internal and POV-Ray native)

- Image textures

- Texture influence channels: Alpha ; Diffuse ; Bump ; Specular ; Mirror (uses same channel as specular)

- Global Illumination: Radiosity (Indirect Lighting) ; Photons caustics (reflect/refract); meshlights

- Custom POV-Ray code input giving access to any POV-Ray feature not supported by the exporter.

POV-Ray 3.7 features
volumetrics and media (scattering/absorbing), blurry reflections(uberpov), ghosting for motion blur(uberpov), micropoly displacement (HGPOV) etc.

## Contents

- 1 Features

- 2 POV-Ray 3.8 Exporter for Blender 4.2x/4.3x/4.4x/...and later

- 3 POV-Ray 3.7 Exporter for Blender 2.79/2.8x/4.1x

- 4 Download

- 5 Usage

- 5.1 Quick Start

- 6 POV compatible forks

- 6.1 Activating UberPOV

- 7 Exported UI Properties

- 7.1 Render Properties

- 7.1.1 Radiosity

- 7.1.2 Anti-aliasing

- 7.1.2.1 Sampling depth

- 7.1.2.2 Anti-alias threshold

- 7.1.3 Bounding method

- 7.1.4 Command line switches

- 7.1.5 Formatting

- 7.2 Scene Properties

- 7.2.1 Color Management

- 7.3 World Properties

- 7.3.1 Background

- 7.3.2 Atmospheric Media

- 7.4 Object Properties

- 7.4.1 Importance sampling

- 7.5 Data Properties

- 7.5.1 Camera

- 7.5.1.1 Depth of Field

- 7.5.1.1.1 Aperture

- 7.5.1.2 Perturbation

- 7.5.2 Light

- 7.5.3 Smoke

- 7.5.4 Hair

- 7.6 Material Properties

- 7.6.1 Emission

- 7.6.2 SSS / SSLT

- 7.6.3 Translucency

- 7.6.4 IOR Mirror

- 7.6.5 Iridescence

- 7.6.6 Caustics

- 7.6.7 shaders

- 7.7 Texture Properties

- 7.7.1 UV coordinates

- 7.7.2 Texture channels

- 7.7.3 Custom gamma

- 7.7.3.1 Using Image Sequences

- 8 Custom POV Code

- 8.1 Video Tutorial

- 8.2 Step by step

- 8.2.1 Adding POV code directly

- 8.2.2 Adding POV code from include files

- 9 POV-Ray Primitives

- 9.1 Infinite Plane

- 9.2 Box

- 9.3 Sphere

- 9.4 Cylinder

- 9.5 Cone

- 9.6 Torus

- 9.7 Parametric

- 9.8 Rainbow

- 9.9 Lathe

- 9.10 Prism

- 9.11 Superquadric Ellipsoid

- 9.12 HeightField

- 9.13 Sphere Sweep

- 9.14 Blob Sphere

- 9.15 Isosurfaces

- 9.15.1 Isosurface Box

- 9.15.2 Isosurface Sphere

- 9.15.3 Supertorus

- 9.16 Macro based primitives

- 9.17 Parametric surfaces

- 10 Importing POV-Ray files

- 10.1 Merging POV scripted Animations

- 11 POV-Ray specific concepts

- 11.1 object (geometry+material)

- 11.2 material (interior+texture)

- 11.3 texture (pigment+normal+finish)

- 11.3.1 pigment / normal

- 11.3.2 finish

- 11.4 texture_list

- 11.5 The most complex blender materials case

- 12 Renderer versions

- 13 Standard Script Information

Standard Script Information at the bottom of this page.

POV-Ray is an an SDL based (Scene Description Language) rendering engine with a long history that makes it very stable and feature rich. The latest version of POV-Ray 3.7 can be found at: povray.org

# POV-Ray 3.8 Exporter for Blender 4.2x/4.3x/4.4x/...and later

Starting from Blender 4.2, as an entry point, POV@Ble downloads, links to sources and to this documentation are now hosted on the new Blender Extensions Platform

# POV-Ray 3.7 Exporter for Blender 2.79/2.8x/4.1x

Though this exporter used to be shipped with any official Blender build, the POV-Ray 3.7 renderer itself was not included in Blender like some other renderers were (Cycles or Freestyle for instance). Instead, users were just expected to download and install it for themselves: See the Installation page for platform specific details on how to get, install or build POV-Ray.

# Download

- Of course, don't forget to download and install POV-Ray itself !

The POV-Ray exporter is shipped with every official Blender release, so you can start using it directly.

However, after checking the logs of every commit to the source of the script for new features or bug fixes, if you find out you need an update,

- You can download all the script at once by clicking snapshot

on this page.

- or download a fresh blender build including scripts from generally a few days before

here.

# Usage

## Quick Start

| 1. Go to the menu Edit ▶ Preferences... Choose the Add-Ons tab and check Persistence of Vision down the list of render Add-Ons. | 1 |
| 2. Choose Persistence of Vision from renderer choices. | 2 |
| 3. Then you can render as usual with the ▶ Render Image render menu or its shortcut (F12 in default keymap) | 3 |

| The image will be rendered according to parameters set in the Properties Window. Main global render settings for instance can be changed in the Render tab of properties window. But there are also properties for environment, material (textures), object, etc. all accessible in neighboring tabs contextually depending on selected object (geometry, camera, light...) | 4 |

# POV compatible forks

Below is a comparison of some features of the two engines available to this exporter to help picking a branch:

| ===== Feature/Engine/Support ===== | ===== POV-Ray ===== | ===== UberPOV ===== |
| Full Spectral Resolution | no | yes (under development) |
| Supersampling | yes | yes |
| Alpha Mapping | yes | yes |
| Bump Mapping | yes | yes |
| Normal Mapping | no | no |
| Displacement Mapping | no | no |
| Motion Blur | no | yes |
| Sub Surface Scattering (SSS) | yes | yes |
| Volumetric Scattering (Medium) | yes | yes |
| Blurred Reflections | yes (very tricky) | yes |
| Clay Render | yes | yes |
| Depth of Field | yes | yes |
| Material Layering | yes | yes |
| Thin Film Coating | yes | yes |
| Dispersion | yes | yes |
| Anisotropy | no | no |
| Thin Film Interference | yes | yes |
| Complex IOR Files | no | no |
| Coating Thickness Absorption | yes | yes |
| Custom Reflectance 90 | yes | yes |
| Custom Fresnel Curve | yes(tricky) | yes(tricky) |
| Sigma Texture | yes | yes |
| Sun-Pool Caustics | yes | yes |
| Ambient Occlusion | no (tricky) | yes (under developement) |
| Lens Shift | yes | yes |
| Diaphragm Circular/Polygonal | yes | yes |
| Per-Model Texture Coordinates | yes | yes |
| Texture Projection Modes | yes | yes |
| Front/Camera Mapping | yes(tricky) | yes(tricky) |
| Multiple UV Channels | no | no |
| Texture Tone Mapping | yes(tricky) | yes(tricky) |
| Procedural Textures | yes | yes |
| Texture Layering | yes | yes |
| Synthesis Texture Layering | yes | yes |
| Point Lighting | yes | yes |
| Mesh Lighting | yes | yes |
| Image-based Lighting | yes | yes |
| Physical Sun/Sky | yes(tricky) | yes(tricky) |
| HDRI Support | yes | yes |
| IES Support | no | no |
| Instance Support | yes | yes |
| Resume/Merge Render | yes | yes |
| Interactive Render | yes | yes |
| Vignetting / Bloom / Glare (Post) | yes(tricky) | yes(tricky) |
| Camera Response CRF (Post) | no | no |
| Color Balance (Post) | no | no |
| Multithreading | yes | yes |
| Region Rendering | yes | yes |
| Passive Emitter | yes | yes |
| Invisible Emitter | yes | yes |
| Invisible Model | yes | yes |
| Shadowless Model | yes | yes |
| Shadowless Point Lights | yes | yes |
| Bucket Rendering | yes | yes |

#### Activating UberPOV

UberPOV is a development branch of POV-Ray. Just like MegaPOV used to be in the old days.

HERE you can download a standalone installer that will not interfere with a standard POV-Ray installation and HERE is an update to the main binary.

Choose the feature set and just point the renderer path in Edit                ▶    Preferences to the location of the engine you choose to use and you're good to go.

File:RendererPath.png 600px

Note that Feature Set is what features get written in the POV file and the Binary Location path points Blender at the binary to actually render it with. Whithout any Binary Location path, Blender will use the environment variables and try to find the most feature rich binary available in that directory. So if you installed UberPOV it's most probably being used for rendering, but you still need to enable its feature set to enjoy specific features, like blurry reflections. And if you want to be sure to use the plain official POV-Ray 3.7 engine instead, just explicitly point the Binary Location path to it.

# Exported UI Properties

## Render Properties

Error creating thumbnail: Unable to save thumbnail to destination

### Radiosity

In POV-Ray, Radiosity is diffuse inter-reflection which has nothing to do with vertex color based radiosity. In fact, it is more similar to final gathering of irradiance samples and provides a noise free indirect light.

Some presets to radiosity are included, their names and settings are those of the rad_def include file shipped with POV-Ray, they set up the properties so you don't need to include the rad_def.inc in the exported pov, it's one of the many examples of what an interface like blender can bring to all POV-Ray users who are not used to have one.

### Anti-aliasing

Three sampling methods are supported:

- non-recursive

- recursive

- stochastic (Monte-Carlo)

The third is only available for UberPOV

#### Sampling depth

Values must be comprised between 1 and 9.
Higher values increase render time and may even introduce some undesired blur.

#### Anti-alias threshold

In the simple, non-recursive method, POV-Ray initially traces one ray per pixel. If the color of a pixel differs from its neighbours (to the left or above) by at least the set threshold value, then the pixel is super-sampled by shooting a given, fixed number of additional rays. The default threshold is 0.3 but it may be changed using this Antialias_Threshold=n.n option.

| | Depth of Field Without Anti-Aliasing |
| | Using no anti aliasing when camera Depth of field is on will speed up rendering and often provide decent enough images. |

### Bounding method

Also called acceleration, it is set to automatic BSP (Binary space partitioning) by the exporter as it's generally the most efficient(POV-Ray 3.7 only) but other acceleration methods are available in POV-Ray.

### Command line switches

Some command line arguments can be passed to POV-Ray. Arguments are separated by spaces. Command line switches consist of a / (Slash), + (plus) or - (minus) sign, followed by one or more alphabetic characters and possibly a numeric value.

Some Commonly Used Commands

| -D | Hide image while rendering Use the command line switch -D to not show the rendered image in POV-Ray (slightly faster and lighter on memory). The image will be sent back to Blender after completion (on Linux this is a hidden default switch to avoid OS specific editor problems). |
| +WT | Limit the number of threads used +WT1 limits POV-Ray to using only one single render thread. (Likewise, +WT2 would instruct POV-Ray to use two render threads.) |
| +C | Continue an interrupted render +C for "Continue trace" is able to recover the point at which your last render stopped and continue it from there (even if you switched off your computer) |
| /EXIT | Close POV-Ray after rendering the image There is also an option in POV-Ray for windows interface to do the same: The "On Completion" option to "Exit POV-Ray for Windows" (in the Render Menu). |

| | Fast Preview Renders |
| | When first setting up your scene, and for fast preview rendering, turn off anti aliasing, depth of field, photons, Radiosity, expensive material features, and in the scene Shading panel, turn off shadows. (other features might get turned off from this panel in future versions of the exporter) Alternatively, use the [Reference:Tracing_Options#Quality_Settings "quality" command line switches] +q1 to +q11. These allow you to easily disable most of the CPU-intensive features. |

### Formatting

The exported POV-Ray file can be customized:

- Different indentation characters to choose from.

- Option to add comments to POV-Ray file.

- Option to write long lists of coordinates in one line for easier browsing of the POV file (and slightly faster parsing by the renderer)

- Choice of character encoding is automatic for versions above 3.8

## Scene Properties

### Color Management

sRGB is supposed to be always used currently (except for world colors which seem to still be linear in Blender Internal).

## World Properties

###

-
-

| Background Blender World gets exported As POV-Ray background{} if flat colored As a POV-Ray sky_sphere{} when using Blender's Blend Sky option (sky texture currently appears a little different because of its mapping). |

| Using a World Preset such as Clear Blue Sky can also setup scattering media to a natural looking atmosphere. |

###

-

-

| Atmospheric Media (to create volume lights): Number of samples for media calculation Atmospheric media color |

## Object Properties

### Importance sampling

It is a priority value between 0 and 1 that can be set per object in the Object properties tab
for Radiosity to shoot more rays at object that require them most.
Touch this rather carefully when trying to improve render times.

## Data Properties

The script exports Sky, Lights, Hair particles, Smoke, fluids, Meshes, Blobs (MetaBalls).

### Camera

####

| Depth of Field It has to be enabled for below property to act: The focal point of depth of field is based on Blender UI Distance field, or object field. Aperture sets the blur amount. (increase to get more) |

####

-

-

-
| Perturbation Normal map for camera plane, native POV procedural patterns can be used with variable strength turbulence scale |

### Light

- No Shadows toggle button can be used to deactivate tracing of shadows for specific lamps only

| | For Realistic Light Attenuation |
| | Use Inverse square falloff, and a small falloff distance value with a higher light intensity will give the best results with Povray's implementation of inverse square law. See this discussion |

###

-
| Smoke A DF3 file (POV-Ray voxel format) is exported and used with a POV-Ray media container with the same dimension and resolution as Blender smoke Domain |

###

-
-
-
| Hair A union of POV-Ray sphere_sweep is exported and used for each strand. They can take the color of a texture applied to emitting object, and shape of sphere sweep tries to emulate shape of strands. |

## Material Properties

###

| | Mesh Lights |
| | When used together with Radiosity, Emit property will allow you to create light bulbs or any luminous form that really illuminates other objects. |

###

###

###

###

###

-

-

-

-

| Caustics Chromatic dispersion for refractive caustics Fast fake caustics (somewhat like Blender Raytransp) Refractive caustics using photons Reflective caustics using photons (high IOR or no mirror IOR for easier effect.) |

| | Faster Photons |
| | To set up some caustics, try moving from the smallest photon depth value to a minimum at which you start to see the effect you are after. Check off the Receive Photons object property for any object that does not really need it.(A glass object casting caustics often doesn't need to receive any itself.) Then you can balance other parameters to tune photons distribution and smoothing (gathering). Don't set the global spacing too fine in scene settings, because then you can still make it finer on each object using its spacing multiplier. If your system has several threads, they can be used in the photons stage: one thread per light, so you can then complexify your scene lighting without overhead. |

###

-

-

-

-

-

| | Glass Like Materials |
| | When trying to achieve some glass like material, keep low diffuse value, dark or totally black to avoid dull surface and keep clear transparency. |

##

###

###

###

####

#

##

| <youtube>https://www.youtube.com/watch?v=PD4PmGLMyys</youtube> |

##

###

```
#declare

MyTexture

=

texture
{

pigment
{

brick

color

rgb
<

0.99
,

0.99
,

0.99
>

// color mortar

color

rgb
<

0.75
,

0.5
,

0.30
>
*
0.75

// color brick

brick_size

<
0.25
,

0.0525
,

0.125
>

// format in x ,y and z- direction

mortar

0.01

// size of the mortar

scale

3

}

// end of pigment

normal

{
wrinkles

0.75

scale

0.01
}

finish

{
ambient

0.15

diffuse

0.95

phong

0.2
}

rotate
<
0
,
0
,
0
>

translate
<

0.01
,

0.00
,

0.00
>

}

// end of texture
```

```
*.pov
*.inc
*.mcr
*.ini
```

| 1. Navigate the text window menu to display the text properties View ▶ Sidebar or use the ⌘ ctrlT shortcut | 1 |
| 2. In the text view properties option, you can chose to render 3d view and/or text. Enable Both. |
| 3. Syntax highlight detects the following file extensions: | 3 |
| 4. Some complete POV-Ray scenes are available to Templates menu from the text header. | 4 |
| 5. And an Insert Menu to add just some pov code snippets at cursor's location. | 5 |
| 6. Then you have to go into the material properties to the Custom POV Code field, and just type in the name of your declared item to use: MyTexture in the example Given. Then you can render your image normally and the material will be replaced. | 6 |

```
scale

<
-
1
,

1
,

1
>

rotate

<
90
,

0
,

-
90
>

}
```

###

```
#include

"colors.inc"

#declare

MyTexture

=

texture
{

pigment
{

brick

color

White
*
0.99

// color mortar

color

rgb
<

0.75
,

0.5
,

0.30
>
*
0.75

// color brick

brick_size

<
0.25
,

0.0525
,

0.125
>

// format in x ,y and z- direction

mortar

0.01

// size of the mortar

scale

3

}

// end of pigment

normal

{
wrinkles

0.75

scale

0.01
}

finish

{
ambient

0.15

diffuse

0.95

phong

0.2
}

rotate
<
0
,
0
,
0
>

translate
<

0.01
,

0.00
,

0.00
>

}

// end of texture
```

#

##

| | |

##

| File:PovBox.png |

##

| File:PovSphere1.png|File:PovSphere2.png |

##

| File:PovCylinder1.png|File:PovCylinder2.png |

##

| File:PovCone1.png|File:PovCone2.png |

##

| File:PovTorus1.png|File:Torus2.png |

##

##

| File:PovRainbow1.png|File:PovRainbow2.png |

##

| File:PovLathe1.png|File:PovLathe2.png |

##

| File:PovPrism.png |

##

##

##

| File:PovSphereSweep1.png|File:PovSphereSweep2.png |

##

| File:PovBlob1.png|File:PovBlob2.png|File:PovBlob3.png |

##

###

###

###

-
-
-
-

##

-
| File:PovPolyToCircle1.png|File:PovPolyToCircle2.png |

-
| File:PovLoft1.png|File:PovLoft2.png |

##

-
| File:PovParametric1.png|File:PovParametric2.png |

#

| 1. From the same ⇧ shiftA menu, you can also import povray files | File:POV importMenu 01.png 1 |
| 2. Or otherwise, clicking File ▶ Import from the main header menu | File:POV importMenu 02.png 2 |
| 3. You can then select one or several files (holding ⇧ shift or pressing B for drag box selection | File:POV importMenu 03.png 3 |

####

```

def

exportCustomCode
():

# Write CurrentAnimation Frame for use in Custom POV Code

file
.
write
(
"#declare CURFRAMENUM =
%d
;
\n
"

%

bpy
.
context
.
scene
.
frame_current
)

#Change path and uncomment to add an animated include file by hand:

file
.
write
(
"#include
\"
/home/quantum/povray_animation/animation_include_file.inc
\"\n
"
)
```

```
#include

"colors.inc"

#declare

doorpart

=

difference

{

cylinder

{

<
0
,

0
,

-
0.1
>,

<
0
,

0
,

0.1
>,

3

}

box

{

<
-
10
,

-
10
,

-
10
>,

<
0
,

10
,

10
>

rotate

<
0
,

0
,

(
-
3
*
CURFRAMENUM
+
3
)>

}

box

{

<
-
10
,

-
10
,

-
10
>,

<
10
,

0
,

10
>

}

}

#declare

wall

=

difference

{

box

{

<
-
100
,

-
100
,

-
0.2
>,

<
100
,

100
,

0.2
>

}

cylinder

{

<
0
,

0
,

-
1
>,

<
0
,

0
,

1
>,

2.9

}

}

object

{

wall

pigment

{

Green

}

}

#if

(
CURFRAMENUM
<
30
)

object

{

doorpart

rotate

<
0
,

0
,

0
>

translate

<
0
,

0
,

0
>

pigment

{

Green

}

}

object

{

doorpart

rotate

<
0
,

0
,

90
>

translate

<
0
,

0
,

0
>

pigment

{

Green

}

}

object

{

doorpart

rotate

<
0
,

0
,

180
>

translate

<
0
,

0
,

0
>

pigment

{

Green

}

}

object

{

doorpart

rotate

<
0
,

0
,

270
>

translate

<
0
,

0
,

0
>

pigment

{

Green

}

}

#end
```

#

##

##

##

####

-
-

####

##

##

#

```
reflection

{

1

metallic

#ifdef
(
unofficial
)

#if
(
unofficial

=

"patch"
)

#if
(
patch
(
"upov-reflection-roughness"
)

>

0
)

roughness

0.1

#end

#end

#end

}
```

#

-

-

| name | POV@Ble (render_povray repository) |
| tooltip | Renders Blender scenes with POV-Ray 3.7, and edits native POV files |
| menu | Renderer rolldown menu |
| usage | If POV-Ray 3.7 is installed, activate the addon in the menu Edit ▶ Preferences Choose it as the renderer and render with F12 |
| version | 0.1.8 |
| author | Campbell Barton, Silvio Falcinelli, Maurice Raybaud, Constantin Rahn, Bastien Montagne, Leonid Desyatkov |
| blender version | 2.79b to 4.4 |
| category | Render, Pipeline, Text Editor, etc. |
| license | GPL3 |
| distribution | Extension |
| note | After May the 10th 2011, some development to the script limited backwards compatibility: If you set up some files prior to that and always use the latest exporter from SVN. It is advised to run the update script and resave your scene with the new version: get to the search bar with Space and type pov to access this command. |
| exe | Blender (Embeds the necessary Python interpreter) + POV-Ray 3.7/3.8 |
| download | You can download the latest development snapshot of the addon at once by clicking -->HERE And of course, don't forget to download and install POV-Ray itself ! |
| modules | Only Python Standard Library modules and the ones shipped with Blender are used. No outside package should have to be installed |
| dependencies | Requires versions of POV-Ray 3.7+, it won't work directly with POV-Ray 3.6 |
| data | Test scenes available upon request |
| bugtracker | Gitea |
| warning | Please report any bug or request! |
| link | povray.org |
| releaselog | Here are the logs of every commit to the source and for specific Releases |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe |

| name | POV@Ble (render_povray repository) |
| tooltip | Renders Blender scenes with POV-Ray 3.7, and edits native POV files |
| menu | Renderer rolldown menu |
| usage | If POV-Ray 3.7 is installed, activate the addon in the menu Edit ▶ Preferences Choose it as the renderer and render with F12 |
| version | 0.1.8 |
| author | Campbell Barton, Silvio Falcinelli, Maurice Raybaud, Constantin Rahn, Bastien Montagne, Leonid Desyatkov |
| blender version | 2.79b to 4.4 |
| category | Render, Pipeline, Text Editor, etc. |
| license | GPL3 |
| distribution | Extension |
| note | After May the 10th 2011, some development to the script limited backwards compatibility: If you set up some files prior to that and always use the latest exporter from SVN. It is advised to run the update script and resave your scene with the new version: get to the search bar with Space and type pov to access this command. |
| exe | Blender (Embeds the necessary Python interpreter) + POV-Ray 3.7/3.8 |
| download | You can download the latest development snapshot of the addon at once by clicking -->HERE And of course, don't forget to download and install POV-Ray itself ! |
| modules | Only Python Standard Library modules and the ones shipped with Blender are used. No outside package should have to be installed |
| dependencies | Requires versions of POV-Ray 3.7+, it won't work directly with POV-Ray 3.6 |
| data | Test scenes available upon request |
| bugtracker | Gitea |
| warning | Please report any bug or request! |
| link | povray.org |
| releaselog | Here are the logs of every commit to the source and for specific Releases |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe | |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe | |

| name | POV@Ble (render_povray repository) |
| tooltip | Renders Blender scenes with POV-Ray 3.7, and edits native POV files |
| menu | Renderer rolldown menu |
| usage | If POV-Ray 3.7 is installed, activate the addon in the menu Edit ▶ Preferences Choose it as the renderer and render with F12 |
| version | 0.1.8 |
| author | Campbell Barton, Silvio Falcinelli, Maurice Raybaud, Constantin Rahn, Bastien Montagne, Leonid Desyatkov |
| blender version | 2.79b to 4.4 |
| category | Render, Pipeline, Text Editor, etc. |
| license | GPL3 |
| distribution | Extension |
| note | After May the 10th 2011, some development to the script limited backwards compatibility: If you set up some files prior to that and always use the latest exporter from SVN. It is advised to run the update script and resave your scene with the new version: get to the search bar with Space and type pov to access this command. |
| exe | Blender (Embeds the necessary Python interpreter) + POV-Ray 3.7/3.8 |
| download | You can download the latest development snapshot of the addon at once by clicking -->HERE And of course, don't forget to download and install POV-Ray itself ! |
| modules | Only Python Standard Library modules and the ones shipped with Blender are used. No outside package should have to be installed |
| dependencies | Requires versions of POV-Ray 3.7+, it won't work directly with POV-Ray 3.6 |
| data | Test scenes available upon request |
| bugtracker | Gitea |
| warning | Please report any bug or request! |
| link | povray.org |
| releaselog | Here are the logs of every commit to the source and for specific Releases |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe | | |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe | | |
| issues | Many properties have been broken in Blender 2.8... Use 2.79b if you want to be safe | | |

-
-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use conditional structures

Conditional constructs in POV-Ray are scripting features which perform different computations or actions depending on whether a condition evaluates to true or false.

## Contents

- 1 The #if, #else, #end construct

- 1.1 Conditional Conjunctions

- 2 The #ifdef and #ifndef, #else and #end constructs

- 3 The #switch, #case, #range and #break Directives

- 3.1 Using #switch with text values

- 4 The #while, #end construct (Loops)

## The #if, #else, #end construct

The simplest conditional directive is the #if directive. This evaluates an expression, which must evaluate to a floating point number (often an integer value). This floating point number is interpreted as a boolean value, that is to say that if the expression evaluates to '0', it is considered 'false' and if it evaluates to anything else it is considered 'true'. If the condition is true then directives between the #if statement and a corresponding #else or #end statement are evaluated.
Note: extremely small values of about 1e-10 are considered zero (false) as a result of floating point number accuracy limits.

The #else directive is optional but, if specified and the conditional expression was false, then directives between the #else and the corresponding #end statement are evaluated.

For example:

```
#declare

CameraDistance
=
25.8
;

...

Definition

of

some

complex

object

that

looks

like

a

sphere

at

great

distances
.

...

#if

(
CameraDistance
>
100
)

sphere

{

<
0
,
0
,
0
>,

1.2

pigment

{
rgb

1
}}

#else

object

{
MyComplexObject
}

#end
```

Here the 'greater than' comparitive operator '>' is used to see whether the value of the CameraDistance variable is greater than 100. In this example, the expression 'CameraDistance>100' evaluates to 0 ('false') because the CameraDistance variable is less than 100.

### Conditional Conjunctions

You can combine conditional terms to build more complex conditional expressions using '&' (And), '|' (Or) and '!' (Not). Use brackets to control the sequence of evaluation. You can also use POV-Ray functions within terms so long as they can be resolved to numeric values. The built-in float identifiers on, off, yes, no, true, and false are designed for use as boolean constants and simply evaluate to 0 (off, no and false) or 1 (on, yes and true).

Here's an example that might take a little thinking about:

```
#declare

Clothing
=
"Jeans"
;

#declare

JeansPermitted
=
false
;

// Check to see if Jeans are permitted

#if

(
strcmp
(
Clothing
,
"Jeans"
)
=
0

&

!
JeansPermitted
)

#debug

"Sorry. Jeans are not permitted\n"

#end
```

In this example, the strcmp function returns a zero because there is no difference between the two strings it has compared.
The term 'strcmp(Clothing,"Jeans")=0' therefore returns a 1 (this term is true). The '!' (Not) symbol is used to check for the condition where jeans are not permitted. The '&' symbol is used to combine the two and to therefore test for the condition where Clothing is "Jeans" and jeans are not permitted.

As you've probably realised, there are many ways of expressing the same thing with conditional expressions and the art is in writing it in a way that is clear enough that you will understand it when you read it through again later. It can help to write the condition out as a comment before you build it into an expression. Leaving the comment in place helps anyone reading it later.

## The #ifdef and #ifndef, #else and #end constructs

The #ifdef and #ifndef statements can be used to determine whether or not an identifier has been defined and to conditionally execute statements. If the identifier passed to the #ifdef directive exists then the statements between the #ifdef statement and a corresponding #else or #end statement are evaluated. The #else directive is optional but, if specified and the identifier passed to the #ifdef directive does not exist, then directives between the #else and the corresponding #end statement are evaluated.

These statements can help to trap potential errors in a scene file or to set default values.

For example:

```
#ifndef
(
ObjectHeight
)

#declare

ObjectHeight
=
1
;

#end
```

## The #switch, #case, #range and #break Directives

The #switch directive provides another way of specifying conditional expressions but allows you to handle a succession of conditions as defined by a #case clause or a #range clause. This directive can seem a little daunting compared to using one or more #if directives, but there are some powerful ways of using it. Like the #if directive it uses float values rather than boolean, so two values whose difference is less than 1e-10 are considered equal.

This trivial example illustrates a number of basic points about the #switch directive.

- If a #case or #range clause is not a match it skips to the next one, ignoring everything else in between.

- If a #break is encountered within a matching #case or #range clause the #switch directive stops processing further lines, causing an immediate jump to the #end without processing subsequent sections, even if a subsequent condition would also have been satisfied.

- When processing a matching #case or #range clause, if a #break is not encountered it continues through successive #case statements whether they match or not.

Although it doesn't contain a camera or any objects, the following example is a complete scene file. When you render it it'll generate a black image, but you'll be able to view the text that it writes out in the message stream.

```
#declare

MyVariable

=

5.2
;

#debug

concat
(
"MyVariable: "
,
str
(
MyVariable
,
3
,
3
),
"\n"
)

#switch

(
MyVariable
)

#case

(
0
)

#debug

"As close to nothing as makes a tiny difference.\n"

#break

#case

(
1
)

#case

(
2
)

#case

(
3
)

#case

(
5
)

#case

(
7
)

#debug

"A Prime number between 1 and 10 (actually within 1e-10 of a prime).\n"

#break

#range

(
0
,
10
)

#debug

"A number between 1 and 10 that is not within 1e-10 of a Prime number\n"

#break

#range

(
10
,
100
)

#debug

"A number between 10 and 100 (actually 10+(1e-10) to 100+(1e-10)).\n"

#break

#else

#debug

"Everything else.\n"

#debug

"ie. Less than zero or greater than 100.\n"

#end
```

If the parameter to a #case clause matches the parameter to the #switch clause then everything up to the next #break, #else or #end clause is evaluated. Similarly, if the parameter to the #switch clause is between the minimum and maximum values of a #range clause then everything up to the next #break, #else or #end clause is evaluated.

If you are interested, you can explore the sensitivity to the accuracy of the floating point number comparison by setting a value using 'e' as follows:

```
#declare

MyVariable

=

5
+
(
0.5
e
-
10
);

or

#declare

MyVariable

=

5
+
(
1.5
e
-
10
);
```

### Using #switch with text values

The #switch, #case and #range clauses accept parameters that evaluate to float numbers. This includes the strcmp function which compares two strings and returns a zero if two strings match. By turning the #switch clause on its head a little, you can therefore use it with strings. Using the #switch directive with text can be particularly handy when writing macros that can generate an object in a large number of differently styles, because you can give each style a name and set parameters based on the name of the style specified.

Example of using #switch with text:

```
#declare

MyVariable

=

"Ostrich"
;

#switch

(
0
)

#case

(
strcmp
(
strupr
(
MyVariable
),
"DOG"
))

#debug

"Dog, DOG, or dog.\n"

// Settings to be used for a dog

#break

#case

(
strcmp
(
strupr
(
MyVariable
),
"CAT"
))

#debug

"Cat, CAT, or cat.\n"

// Settings to be used for a cat

#break

#case

(
strcmp
(
strupr
(
MyVariable
),
"OSTRICH"
))

#debug

"Ostrich, OSTRICH, or ostrich.\n"

// Settings to be used for an ostrich

#break

#else

#debug

"Not a dog, cat or ostrich.\n"

// Stuff to do if an unexpected creature was encountered.

#end
```

Note that the strupr function is used to remove case sensitivity.

The following example illustrates the use of multiple #case clauses for a set of associated conditions to work out how many legs an animal has:

```
#declare

Animal

=

"Ostrich"
;

#switch

(
0
)

#case

(
strcmp
(
strupr
(
Animal
),
"DOG"
))

#case

(
strcmp
(
strupr
(
Animal
),
"CAT"
))

#case

(
strcmp
(
strupr
(
Animal
),
"RHINOCEROS"
))

#case

(
strcmp
(
strupr
(
Animal
),
"TABLE"
))

#declare

LegCount

=

4
;

#break

#case

(
strcmp
(
strupr
(
Animal
),
"OSTRICH"
))

#case

(
strcmp
(
strupr
(
Animal
),
"DUCK"
))

#declare

LegCount

=

2
;

#break

#case

(
strcmp
(
strupr
(
Animal
),
"WORM"
))

#case

(
strcmp
(
strupr
(
Animal
),
"DOLPHIN"
))

#declare

LegCount

=

0
;

#break

#else

#declare

LegCount

=

-
1
;

// Unknown

#end

#debug

concat
(
"Animal: "
,
Animal
,
"\n"
)

#if

(
LegCount
<
0
)

#debug

"Number of Legs Unknown\n"

#else

#debug

concat
(
"Number of Legs: "
,
str
(
LegCount
,
3
,
0
),
"\n"
)

#end
```

You can also test for abbreviations using the following construct:

```

#case

(
strcmp
(
strupr
(
Animal
),
substr
(
"RHINOCEROS"
,
1
,
strlen
(
Animal
))))
```

This would match 'Rhino' or 'Rhinoceros', but you clearly need to be a little careful with this as it would also match 'Rhinoc', 'R' and ''. To set a minimum abbreviation length use the max function:

```

#case

(
strcmp
(
strupr
(
Animal
),
substr
(
"RHINOCEROS"
,
1
,
max
(
strlen
(
Animal
),
5
))))
```

This would match 'Rhino', 'Rhinoceros' and everything in between, but not 'R', 'Rhin' or ''.

## The #while, #end construct (Loops)

See also - HowTo:Use_macros_and_loops.

The #while, #end construct encapsulates a sequence of POV-Ray statements that are repeatedly executed for as long as the conditional expression specified is true. The conditional expression can be simple or complex and adheres to the same rules as apply to the #if statement described above.

As with the #if statement, the conditional expression must evaluate to a floating point number (often an integer value). This floating point number is interpreted as a boolean value, that is to say that if the expression evaluates to '0', it is considered 'false' and if it evaluates to anything else it is considered 'true'. If the condition is true then directives between the #while statement and a corresponding #end statement are evaluated. The conditional expression is then re-evaluated and, if still true the directives between the #while statement and the corresponding #end statement are repeated.

The statements repeat in a loop until the condition evaluates to false. It is up to you to control the loop by defining a suitable condition that will not loop indefinitely. If the condition never evaluates to false then the program will continue to evaluate the statements forever, until you hit the stop button, until the memory of your computer is exhausted or until you terminate the application (whichever happens first) see Wikipedia:Infinite_loop.

Note: extremely small values of about 1e-10 are considered zero (false) as a result of floating point number accuracy limits.

Here is an example of a simple loop:

```
#declare

Count
=
0
;

#while

(
Count

<

5
)

object

{

MyObject

translate

x
*
3
*
Count

}

#declare

Count
=
Count
+
1
;

#end
```

This example places five copies of MyObject in a row spaced three units apart in the x-direction.

As described above (for the #if statement), you can combine conditional terms to build more complex looping conditional expressions using '&' (And), '|' (Or) and '!' (Not).

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use constructive solid geometry

- 1 Constructive Solid Geometry

- 2 Union

- 3 Intersections

- 4 Difference

- 5 Merge

## Constructive Solid Geometry

Constructive Solid Geometry (CSG) is a powerful tool to combine primitive objects to create more complex objects.

## Union

The union object is a way to apply a universal group to a family of objects. Their appearance does not change at all while using this.
To declare a union object:

```
#include

"colors.inc"

camera
{

location

<
4
,

4
,

-
10
>

look_at

0

angle

36

}

light_source
{

<
500
,

500
,

-
1000
>

White

}

plane
{

y
,

-
1.5

pigment
{

checker

Green

White

}

}

union
{

box
{

-
1
,

1

pigment
{

Blue

}

}

sphere
{

0
,

1.375

pigment
{

Red

}

}

}
```

Since union creates a single object from multiple ones, we can apply textures and other transformations to all objects.

```
#include

"colors.inc"

camera
{

location

<
4
,

4
,

-
10
>

look_at

0

angle

36

}

light_source
{

<
500
,

500
,

-
1000
>

White

}

plane
{

y
,

-
1.5

pigment
{

checker

Green

White

}

}

union
{

box
{

-
1
,

1

}

sphere
{

0
,

1.375

}

pigment
{

Red

}

}
```

The above code will give the resulting object a pigment of Red, without us having to change the Blue box to Red and then others if we decide to change the color of the whole object. The resultant image, rendered with anti-aliasing, is shown below:

 Union Example

## Intersections

Like union, intersect creates a single object out of many, however it applies a calculation that will only result in what parts of the listed objects that intersect with each other. Intersect inherits from union so anything you can do to a union object you can do in a intersect object.

So if we take our previous code and change it around a small bit:

```
#include

"colors.inc"

camera
{

location

<
4
,

4
,

-
10
>

look_at

0

angle

36

}

light_source
{

<
500
,

500
,

-
1000
>

White

}

plane
{

y
,

-
1.5

pigment
{

checker

Green

White

}

}

intersection
{

box
{

-
1
,

1

}

sphere
{

0
,

1.375

}

pigment
{

Red

}

}
```

What you'll see is a sphere with it's sides cut off because they are outside the intersection of the box and sphere. The corners are rounded because the corners of the box are outside the sphere.

 Intersection Example

## Difference

Again, as with intersection and union, difference creates a single object out of many. The difference object is created by taking the initial object in the list and cutting away the other listed objects from it. A thing to note is that any of the CSG operations can be embedded with each other:

```
#include

"colors.inc"

camera
{

location

<
4
,

4
,

-
10
>

look_at

0

angle

36

}

light_source
{

<
500
,

500
,

-
1000
>

White

}

plane
{

y
,

-
1.5

pigment
{

checker

Green

White

}

}

difference
{

intersection
{

box
{

-
1
,

1

}

sphere
{

0
,

1.375

}

pigment
{

Red

}

}

union
{

cylinder
{

-
2
*
y
,

2
*
y
,

0.5

}

cylinder
{

-
2
*
x
,

2
*
x
,

0.5

}

cylinder
{

-
2
*
z
,

2
*
z
,

0.5

}

pigment
{

Blue

}

}

}
```

 Difference Example

## Merge

Merge is much like union, with the main difference of it actually treats all objects as one instead of just a list of objects in one family. This is more visible when using semi-clear or fully clear objects, such as water or glass-like objects.

For example, take the following code:

```
#include

"colors.inc"

#include

"textures.inc"

camera
{

location

<
0
,

2
,

-
10
>

look_at

1
*
y

}

light_source
{

1000

White

}

plane
{

y
,

-
1

pigment
{

White

}

}

union
{

sphere
{

0
,

1

translate

0.5
*
x

}

sphere
{

0
,

1

translate

-
0.5
*
x

}

texture
{

Glass2

}

}

merge
{

sphere
{

0
,

1

translate

0.5
*
x

}

sphere
{

0
,

1

translate

-
0.5
*
x

}

texture
{

Glass2

}

translate

2
*
y

}
```

On the bottom pair of spheres we can still see the edges of the spheres on the inside. On the top pair we don't. That's the difference between union and merge.

 Merge Example

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use for loop over traditional while loop

### Loops

Traditionally in POV-Ray the `#while` directive has been the way to implement loops. In 3.7 a new `#for` directive was added that makes coding counting loops much simpler.

### Nested Loops

Loops can of course be nested. This capability is often used to lay out very large numbers of objects using an economical amount of code.

First, here's a very simple, complete scene file containing a nested `#while` loop with which you can play:

```
#include

"colors.inc"

#include

"woods.inc"

camera

{
location

<
-
1.5
,
0.9
,
-
0.08
>

look_at

<
0
,
0.7
,
0.5
>}

light_source

{<
-
20
,
75
,
-
100
>

White
}

background

{
LightBlue
}

plane

{
y
,
0

pigment

{
color

GreenYellow
}

#declare

RandomSeed

=

seed
(
1
);

#declare

XCount
=
0
;

#while

(
XCount

<

200
)

#declare

ZCount
=
0
;

#while

(
ZCount

<

200
)

box

{<
-
0.05
,
0
,
-
0.05
><
0.05
,
1
,
0.05
>

rotate

<
3
*
rand
(
RandomSeed
),
5
*
rand
(
RandomSeed
),
3
*
rand
(
RandomSeed
)>

texture

{
T_Wood1

translate

<
rand
(
RandomSeed
),
ZCount
,
rand
(
RandomSeed
)>

rotate

x
*
90

scale

0.07
}

translate

<
XCount
,
0
,
ZCount
>

}

#declare

ZCount
=
ZCount
+
1
;

#end

#declare

XCount
=
XCount
+
1
;

#end
```

Here's the same looping accomplished with the new `#for` loop directive:

```
#include

"colors.inc"

#include

"woods.inc"

camera

{
location

<
-
1.5
,
0.9
,
-
0.08
>

look_at

<
0
,
0.7
,
0.5
>}

light_source

{<
-
20
,
75
,
-
100
>

White
}

background

{
LightBlue
}

plane

{
y
,
0

pigment

{
color

GreenYellow
}

#declare

RandomSeed

=

seed
(
1
);

#for

(
XCount
,
0
,
199
)

#for

(
ZCount
,
0
,
199
)

box

{<
-
0.05
,
0
,
-
0.05
><
0.05
,
1
,
0.05
>

rotate

<
3
*
rand
(
RandomSeed
),
5
*
rand
(
RandomSeed
),
3
*
rand
(
RandomSeed
)>

texture

{
T_Wood1

translate

<
rand
(
RandomSeed
),
ZCount
,
rand
(
RandomSeed
)>

rotate

x
*
90

scale

0.07
}

translate

<
XCount
,
0
,
ZCount
>

}

#end

#end
```

These example uses 2 nested loops. Each nested loop has its own control variable. It starts with both XCount and YCount equal to zero. With XCount at zero, YCount loops from 0 to 199, then XCount is incremented to 1, YCount is reset to zero and the second loop repeats.

This example also introduces a degree of randomness in each iteration using the POV-Ray seed and rand functions. When generating a large number of objects using loops you'll find that a little randomness goes a long way. This example generates 40,000 wooden posts, each with a little bit of a wobble and each with a slightly different wood texture pattern. It should take about 10 or 20 seconds to render

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use macros and loops

Loops and Macros are usually created to do one set of code many times. The beauty of the loop is that the code can reference an array or a spline to write a rollercoaster track or a roadway such that very complicated scenes can be created with the use of simple declared variables.

## Contents

- 1 Loops

- 2 Macros

- 2.1 Macro Name

- 2.2 Macro Parameters

- 2.3 Macro Body

## Loops

See also HowTo:Use_conditional_structures#The #while, #end construct

Loops may be constructed through the use of the #while statement. A typical loop may be constructed as follows.

```
#declare

step

=

0
;

#declare

steps

=

100
;

#while

(
step

<
=

steps
)

#
sphere

{

step
*
x
,

0.4

pigment

{
Blue
}

}

#declare

step

=

step

+

1
;

#end
```

Nested loops may be created to generate a two or three dimensional aspect to the scene. Beware that creating extra nested loops may dramatically increase the parse time for the scene.

The following nested loop will draw a series of Blue balls on the XZ plain

```
#declare

xpos

=

0
;

#declare

zpos

=

0
;

#declare

xfinal

=

10
;

#declare

zfinal

=

10
;

#while

(
xpos

<
=

xfinal
)

#while

(
zpos

<
=

zfinal
)

#
sphere

{

<
xpos
,
0
,
zpos
>

0.4

pigment

{
Blue
}

}

#declare

zpos

=

zpos

+

1
;

#end

#declare

xpos

=

xpos

+

1
;

#declare

zpos

=

0
;

#end
```

## Macros

The essential custom functions of PoV-Ray. These are blocks of code that make calculations, or make objects, or if used in a series can make many objects with all of them different.

Macros are declared like this:

```
#macro

{
Macro_Name
}({
Macro_Parameters
})

{
Macro_Body
}

#end
```

### Macro Name

The name of the macro. Can be as simple or as complex as you want it to be. From a programmer's perspective, it's best to name it in a semi-descriptive way of what you're trying to do.

```
#macro

MakeSpheres
()

//Make a layer of spheres

#end
```

is much more understandable than

```
#macro

MS
()

//Make a layer of spheres

#end
```

Where as the latter of the two could be confused with "MakeSplines" or something. You never know.

### Macro Parameters

A comma delineated list of variables that you will use in the macro.
They can be changed if needed, but don't have to be always.

```
#macro

MakeSpheres
(
height
,

layer_color
)

//Make a layer of spheres

// height - height of layer

// layer_color - pigment of the layer

#end
```

Here, parameters for height and color are added. These can change the macro's behavior.

### Macro Body

The real juice of the macro. This is where you do loops or object creation or loops with object creation, whatever. This is where you get the job done.

```
#macro

MakeSpheres
(
height
,

layer_color
)

//Make a layer of spheres

// height - height of layer

// layer_color - pigment of the layer

#declare

xpos

=

0
;

#declare

zpos

=

0
;

#declare

xfinal

=

10
;

#declare

zfinal

=

10
;

#while

(
xpos

<
=

xfinal
)

#while

(
zpos

<
=

zfinal
)

// Notice that height and layer_color are inserted here.

#
sphere

{

<
xpos
,
height
,
zpos
>

0.4

pigment

{

layer_color

}

}

#declare

zpos

=

zpos

+

1
;

#end

#declare

xpos

=

xpos

+

1
;

#declare

zpos

=

0
;

#end

#end

// The macro is invoked three times with different parameters

MakeSpheres
(
0
,

Blue
)

MakeSpheres
(
2
,

Red
)

MakeSpheres
(
1
,

White
)
```

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use radiosity

Radiosity, also known as global illumination (GI), is one of the two methods POV-ray supports of simulating the inter-reflections of light that occur in the real world.

## Contents

- 1 What radiosity does

- 2 When to use it

- 3 Basic Use of Radiosity

- 3.1 Rad_Settings macro

- 4 Developing a Better Understanding of Radiosity

- 4.1 Radiosity Parameters

- 5 Tuning Radiosity

- 5.1 Step 1: Prepare Your Source

- 5.2 Step 2: Complete the Scene

- 5.3 Step 3: Decide What Will Be Casting the Light

- 5.4 Step 4: Set the ambient Values for Other Objects

- 5.5 Step 5: Find Your Error Bound

- 5.6 Step 6: Determining an Appropriate Value for count

- 5.7 Step 7: Correcting for Corners and Finalizing

- 5.8 Step 8: About Colors

- 6 Tricks to Speed Things Up

- 6.1 Use the File Queue

- 6.2 Saving and Loading Radiosity Data

- 7 Pitfalls and Work-arounds

- 7.1 Artifacts

- 7.2 Partial Output

- 7.3 The effects of background and sky_sphere on Radiosity

- 8 Notes

### What radiosity does

We can see objects because light from a light source (such as the sun) is reflected from the surface of the object and picked up by our eyes. However, not all of this light goes into our eyes, but also strikes the other objects around us, likewise, not all of the light coming from an object is directly from a light source. The light we see may have been reflected many times by the surrounding objects.
A good example of this is a shadow on a sunny day. The shadow is certainly darker than its surroundings, but will not be pure black because of the reflected light.
POV-ray can simulate this in two ways:

- Via the ambient property in the finish block of a texture, which gives the color of the object when no light is shining on it.

- By using radiosity.

### When to use it

Radiosity should be used when you want a realistic look of indirect lighting. Indoor scenes frequently use radiosity, but outdoor scenes can greatly benefit from it too. Since black or uniformly dark shadows are very unrealistic and almost never occur in the real world, radiosity can do a lot to make an image look good. Here are some images to help compare:

Radiosity can also be used to make glowing objects in a scene cast light onto other objects:

(EXAMPLE IMAGES)

### Basic Use of Radiosity

To use radiosity, include a radiosity block in the global_settings. If you are unfamiliar with the settings that control the look and performance of POV’s radiosity algorithm, the easiest way to get started is to use the Rad_Settings macro from rad_def.inc. A description of the parameters and how you can tune them is also covered in this tutorial.

#### Rad_Settings macro

The Rad_Settings macro allows a user to easily use any of the predefined quality settings from below:

- Radiosity_Default

- Radiosity_Debug

- Radiosity_Fast

- Radiosity_Normal

- Radiosity_2Bounce

- Radiosity_Final

- Radiosity_OutdoorLQ

- Radiosity_OutdoorHQ

- Radiosity_OutdoorLight

- Radiosity_IndoorLQ

- Radiosity_IndoorHQ

The macro expects the first parameter to be one of the above quality settings followed by two boolean parameters for normals and medias, respectively.

Example use of Rad_settings macro:

```
#include

“
rad_def
.
inc
”

global_settings

{

radiosity

{

Rad_Settings
(
Radiosity_Normal
,
off
,
off
)

}

}

#default

{
finish
{
ambient

0
}}
```

That's it right there! We've just applied to our scene radiosity at a medium quality setting that will not take into account normals or media!

### Developing a Better Understanding of Radiosity

The macro works well for most scenes and is very easy to use, but it may not always suit our purposes. What if we tried all of the quality settings, but our scene still isn't rendering correctly? We need a good understanding of the radiosity parameters so we can fine-tune the settings to our needs.

#### Radiosity Parameters

Below is a description of everything that can be in a radiosity block as defined by the documentation:

pretrace_start
pretrace_end
To control the radiosity pre-trace gathering step, use the keywords pretrace_start and pretrace_end within the radiosity{} block. Each of these is followed by a decimal value between 0.0 and 1.0 which specifies the size of the blocks in the mosaic preview as a percentage of the image size. The defaults are 0.08 for pretrace_start and 0.04 for pretrace_end.
These pretrace settings can be used to balance speed against quality. The pretrace parameters help POV-Ray determine where more radiosity samples are needed. For a final high-quality render, it's generally best to keep the pretrace_start setting around the default value, but have a much smaller pretrace_end setting, such as 0.005.
One trick is to set the pretrace values as a function of the output resolution using the image_width identifier. Something like:

```
#local

p_start

=

64
/
image_width
;

#local

p_end_tune

=

8
/
image_width
;

#local

p_end_final

=

4
/
image_width
;
```

This means the bigger the image you render, the higher the quality settings used, but small, quick test previews still use coarser values so that they are not slowed down needlessly.
count
The integer number of rays that are sent out whenever a new radiosity value has to be calculated is given by count. A value of 35 is the default, and the maximum is 1600.
Count is probably the main speed/quality parameter. A higher count gives more accurate results, but significantly increases render times. Count needs to be balanced with the error_bound parameter, as smaller error_bound values generally need a higher count.

nearest_count
The nearest_count integer value is the minimum number of old ambient values blended together to create a new interpolated value. The total number blended will vary depending on error_bound. All previous values that fit within the specified error_bound will be used in the average. The default is 5, and the maximum is 20.
Raising nearest_count can significantly reduce or smooth out splotchiness, since more nearby values will be averaged. As always, there is a corresponding speed penalty, but using higher values like 10 or even 20 for the final trace may help remove artifacts. Some artifacts can also be removed by adjusting the count parameter.

error_bound
The error_bound float value is one of the two main speed/quality tuning values (the other is count, the number of rays shot). It is intended to mean the fraction of error tolerated. For example, if it were set to 1 the algorithm would not calculate a new value until the error on the last one was estimated to be 100%. Ignoring the error introduced by rotation for the moment, on flat surfaces this is equal to the fraction of the reuse distance, which in turn is the distance to the closest item hit. If you have an old sample on the floor 10 distance units from a wall, an error bound of 0.5 will get you a new sample at a distance of about 5 distance units from the wall.
The default value of 1.8 is good for a smooth general lighting effect, but may render corners or smaller shadows incorrectly. Using lower values is more accurate, but it will increase the danger of artifacts and therefore require a higher count value. You can use values lower than 0.1 but both render time and memory use can become extremely high then.

recursion_limit
The recursion_limit is an integer value which determines how many recursion levels are used to calculate the diffuse inter-reflection. The default value is 3, the upper limit is 20.
This represents the number of times light bounces off objects before reaching your eye. In the real world, this could be a very high number. In POV-Ray, higher is not always better. Values between 2 and 4 tend to give good results for most scenes. Higher values can have a significant speed penalty. It is recommended that you use the lowest value that gets the look you want in your scene. Note that the number of recursions can affect both the brightness and contrast of your indirectly-lit objects, which means that test tuning with a significantly different recursion level than what you intend to do the final trace with may have unexpected results.

low_error_factor
If you calculate just enough samples, but no more, you will get an image which has slightly blotchy lighting. What you want is just a few extra samples interspersed, so that the blending will be nice and smooth. The solution to this is the mosaic preview, controlled by pretrace: it goes over the image one or more times beforehand, calculating radiosity values. To ensure that you get a few extra samples, the radiosity algorithm lowers the error bound during the initial pass or passes, then sets it back just before the final pass. The low_error_factor is a float tuning value which sets the amount that the error bound is dropped during the preliminary image passes. If the low error factor is 0.8 and the error bound is set to 0.4 it will use an error bound of 0.32 (0.4 × 0.8) during the initial passes and 0.4 on the final pass. The default value for low_error_factor is 0.5.

gray_threshold
Diffusely inter-reflected light is a function of the objects around the point in question. Since this is recursively defined to millions of levels of recursion in any real-life scene, every point is illuminated at least in part by every other part of the scene. Since we cannot afford to compute this, if we only do one bounce, the calculated ambient light is very strongly affected by the colors of the objects near it.
This is known as color bleed and it really happens, but not as much as this calculation method would have you believe. The gray_threshold float value desaturates the colored light a little to make your scene more visually correct. A value of 0.6 means to calculate the ambient value as 60% of the equivalent gray value calculated, plus 40% of the actual value calculated. At 0.0, this feature does nothing. At 1.0, you will get 100% pure white/gray ambient light, with no hue.

minimum_reuse

brightness
The brightness keyword specifies a float value that is the degree to which objects are brightened before being returned upwards to the rest of the system. The default value is 1.0.
Brightness should be balanced with your ambient and diffuse values as well as your light_source intensity. Remember that brightness doesn't refer directly to the overall scene brightness, it refers to the radiosity calculation which affects the overall scene brightness. This distinction is important, because we can use it to change the contrast of some indirectly lit areas.

adc_bailout

normal
Radiosity estimation can be affected by normals. To enable this feature, add normal on to the radiosity{} block. The default is off.

media
Radiosity estimation can be affected by media. To enable this feature, add media on to the radiosity{} block. The default is off.

save_file
load_file
You can save the radiosity data using save_file "file_name" and load the same data later using load_file "file_name". In general, it is not a good idea to save and load radiosity data if scene objects are moving. Even if data are loaded, more samples may be taken during rendering (which produces a better approximation). You can disable samples from being taken during the final rendering phase by specifying always_sample off.

always_sample
You can force POV-Ray to only use the data from the pretrace step and not gather any new samples during the final radiosity pass. This may reduce splotchiness. To do this, set always_sample to off (the default value is on). It can also be useful when reusing previously-saved radiosity data.

max_sample
Sometimes very bright objects can cause problems with splotchiness. The max_sample keyword attempts to address this. Max_sample takes a float parameter which specifies the brightest that any gathered sample is allowed to be. Any samples brighter than this will have their brightness decreased (without affecting color). Specifying a non-positive value for max_sample will allow samples to have any brightness level. Max_sample defaults to 0.0, so this is the default behavior.

### Tuning Radiosity

Tuning radiosity for your scene is the longest and arguably the most important part of the process. While we can often apply radiosity settings that worked well in another scene and have good results, the different lighting conditions and geometry means that the perfect settings for your scene will not be found in any guide. Fear not! There are ways to find the right settings for your scene:

#### Step 1: Prepare Your Source

When tuning radiosity, it is very convenient to have variables and conditional blocks of code that allow you to easily turn off and on light sources, adjust some or all of your textures' ambient values at once, etc. Keep this in mind as you work on your scene. You can go ahead and add a radiosity block to your global_settings, but leave it commented out until you are ready to use it.

#### Step 2: Complete the Scene

Since radiosity is calculated using all of the objects in a scene, it is best to finish placing everything first. You want the lowest (i.e. fastest) quality settings you can possibly use for your scene, so try to have most (if not all) objects in their final locations.

If your scene is very complex and contains a lot of objects, try to place the ones that you think the indirect lighting will really be affected by - what is casting the shadow? What objects will be in shadow?

Other speed or memory saving techniques like using lower polygon-count meshes during tuning might also be a good idea. The bottom line is that the fewer changes to your objects in between the tuning phase and the final trace, the better.

#### Step 3: Decide What Will Be Casting the Light

When working with radiosity, any object with a non-zero ambient value casts light. This behavior allows all of the light in your scene to be cast using objects (no light_source required).

I typically use a light_source for outdoor scenes, and objects with a high ambient for indoor scenes. This can work very well for artificial light sources; for instance, if you wanted to represent a fluorescent light with a high ambient cylinder, then you wouldn't have to worry about the gaps in between light sources in an area light.

This decision doesn't need to be set in stone. In many scenes you can use either one, or even some combination of both. If you are new to radiosity, I recommend using scene objects to cast light for indoor portions of your scene, and a single light_source (may be area light also) for the outdoor portions.

If you are doing a scene with a cloud-covered sky, you could even use your sky_sphere as the scene light source to get the soft, diffuse, directionless sort of light you would have on an overcast day.

#### Step 4: Set the ambient Values for Other Objects

Objects that are not intended to be luminous should have a very low ambient. Some people let all of their objects have a small amount of ambient and adjust the radiosity brightness accordingly. I recommend starting with zero because it gives maximum contrast for the indirectly lit areas. We can add more later if we want to reduce the contrast.

#### Step 5: Find Your Error Bound

As mentioned above, lower error_bound values produce more accurate lighting, but require a higher count, more memoery, and significantly increased render times. Therefore, we want to find the lowest value that will work for our scene. The scene being used for this example can be found here: btuning_00.pov This is the radiosity block we will use in the scene to determine an appropriate error bound:

```
#local

p_start

=

64
/
image_width
;

#local

p_end_tune

=

8
/
image_width
;

#local

p_end_final

=

4
/
image_width
;

global_settings
{

radiosity

{

pretrace_start

p_start

// Use a pretrace_end value close to what you

pretrace_end

p_end_tune

// intend to use for your final trace

count

1

// Note the very low count!

nearest_count

10

// 10 will be ok for now

error_bound

1.0

// Start with 1.0

recursion_limit

3

// Recursion should be near what you want it to be

// If you aren't sure, start with 3 or 4

}

}
```

This will produce an image with very low quality radiosity and lots of errors... which is exactly what we need! We need to see the size of the smaller shadows in comparison to the area averaged in our radiosity samples. What we are looking for is where the shadows are completely missing.

 error_bound of 1.00

Note the area circled in red. There is light "shining through" at the base of the object because the error_bound is too large for this scene. We drop it to 0.5 and render it again.

 error_bound of 0.50

It seems like most of the shadows are now showing up, but just be sure we aren't missing any, we'll try 0.25 and see.

 error_bound of 0.25

A side by side comparison shows that we picked up some more shadows in the most distant part of the scene. Anytime you pick up shadows, it is generally a good idea to try the image again, with a lower error bound.

 error_bound of 0.125

The error_bound of 0.125 added more details to the shadows we saw before, and a test render at 0.0625 doesn't seem to pick up any more details. This allows us to establish a tentative range for our error_bound: no greater than 0.25 due to errors, and no less than 0.125 due to diminishing returns.

We could continue the bracketing method above, but because the numeric difference between 0.25 and 0.125 is small, we'll use 0.1875, the mean average of our upper and lower range. If the ranges we'd found had a greater differences, then it would be preferable to test and bracket again to keep the range narrow.

That's it, we now have an error_bound value that should work with our final trace!

#### Step 6: Determining an Appropriate Value for count

The count must be balanced with the error_bound parameter. A higher error_bound can produce very smooth results with a low count. Because our error_bound is to be 0.1875, we will need a count much higher than the default. To figure out an appropriate count value, we need to go back to one of our previous, too large settings from bracketing. We want to see what count will produce smooth results for a given error_bound:

```
#local

p_start

=

64
/
image_width
;

#local

p_end_tune

=

8
/
image_width
;

#local

p_end_final

=

4
/
image_width
;

global_settings
{

radiosity

{

pretrace_start

p_start

// Use a pretrace_end value close to what you

pretrace_end

p_end_tune

// intend to use for your final trace

count

25

// try 25 or so

nearest_count

10

// 10 will be ok for now

error_bound

0.375

// since 0.5 was too large, we use 0.375,

// halfway between 0.25 and 0.5

recursion_limit

3

// Recursion should be near what you want it to be

// If you aren't sure, start with 3 or 4

}

}
```

Remember, we want to see what produces smooth blending of the shadows, we aren't worried about errors or shadows not showing up right now. A count of 25 and even 50 are too blocky, but 75 seems to blend smoothly for this scene. Experimentation has shown me that count and error_bound can be roughly related once you find values that result in smooth shadows as follows:

 f
 i
 n
 a
 l

 c
 o
 u
 n
 t
 =

 t
 e
 s
 t

 c
 o
 u
 n
 t
 ∗
 (
 t
 e
 s
 t

 e
 r
 r
 o
 r
 _
 b
 o
 u
 n
 d

 )

 2

 (
 f
 i
 n
 a
 l

 e
 r
 r
 o
 r
 _
 b
 o
 u
 n
 d

 )

 2

 {\displaystyle final\ count={\frac {test\ count*(test\ error\_bound)^{2}}{(final\ error\_bound)^{2}}}}

We can set up and use this relation in our scene:

```
#local

p_start

=

64
/
image_width
;

#local

p_end_tune

=

8
/
image_width
;

#local

p_end_final

=

4
/
image_width
;

#local

smooth_eb

=

0.50
;

#local

smooth_count

=

75
;

#local

final_eb

=

0.1875
;

#local

final_count

=

smooth_count

*

smooth_eb

*

smooth_eb

/

(
final_eb

*

final_eb
);

global_settings
{

radiosity

{

pretrace_start

p_start

// Use a pretrace_end value close to what you

pretrace_end

p_end_tune

// intend to use for your final trace

count

final_count

// as calculated above

nearest_count

10

// 10 will be ok for now

error_bound

final_eb

// from what we determined before, defined above

// halfway between 0.25 and 0.5

recursion_limit

3

// Recursion should be near what you want it to be

// If you aren't sure, start with 3 or 4

}

}
```

We now have a count that will be correct for any error_bound we specify. At this point, if we render the scene, we should have decent looking results, but...

 Final error_bound and count settings

Things don't look so good! What happened?

#### Step 7: Correcting for Corners and Finalizing

We need to make two more minor adjustments:

```
#local

p_start

=

64
/
image_width
;

#local

p_end_tune

=

8
/
image_width
;

#local

p_end_final

=

4
/
image_width
;

#local

smooth_eb

=

0.50
;

#local

smooth_count

=

75
;

#local

final_eb

=

0.1875
;

#local

final_count

=

smooth_count

*

smooth_eb

*

smooth_eb

/

(
final_eb

*

final_eb
);

global_settings
{

radiosity

{

pretrace_start

p_start

// Use p_end_final for the pretrace_end value

pretrace_end

p_end_final

//

count

final_count

// as calculated above

nearest_count

20

// set nearest_count to 20 for final trace

error_bound

final_eb

// from what we determined before, defined above

// halfway between 0.25 and 0.5

recursion_limit

3

// Recursion should be near what you want it to be

// If you aren't sure, start with 3 or 4

minimum_reuse

0.005

// Use a lower minimum reuse

}

}
```

We still had some of our settings at their test values, and not their final values. We lower pretrace_end, which makes POV do another pretrace pass, raise our nearest_count to average more samples, and lower the minimum reuse, to make sure our corners render correctly.

 Final settings with anti-aliasing 0.3

This is much better looking!

#### Step 8: About Colors

The scene we've been practicing uses only shades of gray, but the color of objects is also taken into account. Because we are simulating only a few bounces of light compared to the millions that actually occur in the real world, the colors of the objects struck by the rays tend to influence the indirect light color too strongly. POV-ray has the gray_threshold keyword to allow us to change how much influence the color has. Values for gray_threshold may be anywhere from 0.0 (allow full influence of colors) to 1.0 (indirect light has no color).

### Tricks to Speed Things Up

The demo scenes in this guide generally render very quickly on recent hardware due to their simplicity. In a large and complex scene, radiosity can take many times longer. Not only does this mean that your final trace will take a long time, but it also makes the tuning process above very time consuming. Here are a few ways to make things a bit faster:

#### Use the File Queue

When bracketing, make a few copies of your scene with the different values you will be testing, and place them into the queue. That way, you can let POV-ray raytrace them while you are doing other things, then compare the images later.

#### Saving and Loading Radiosity Data

Radiosity data can be saved and loaded using the save_file and load_file with the appropriate file name. This allows you to re-render a scene without waiting for the lengthy radiosity calculations to be done again. It is important to remember that radiosity takes the entire scene into account, and substantial changes to anything might result in strange or unrealistic lighting. This can include objects that are not even within the field of view, since some light may bounce off of out of sight objects and on to visible objects. Even so, certain finishing touches can be added without going through a long wait again. Likewise, if you decided that you wanted to render a higher resolution image, saving radiosity data can save a lot of time.

```
radiosity

{

pretrace_start

64
/
image_width

pretrace_end

8
/
image_width

count

120

nearest_count

20

error_bound

.5

recursion_limit

3

save_file

"myRadData.rad"

}

}
```

The above code will save the calculated radiosity data into a file called "myRadData.rad" which can be loaded as below:

```
radiosity

{

pretrace_start

1

pretrace_end

1

count

120

nearest_count

20

error_bound

.5

recursion_limit

3

load_file

"myRadData.rad"

always_sample

off

}

}
```

Note that the pretrace values have been set to one and always sample is off - we don't want to do any more sampling, we want to load our existing samples. The other settings should be left unchanged, as changing them can introduce artifacts.

There is one problem with using save_file and load_file like this: It is very easy to inadvertently destroy your radiosity data! How?

```
radiosity

{

pretrace_start

1

pretrace_end

1

count

120

nearest_count

20

error_bound

.5

recursion_limit

3

save_file

"myRadData.rad"

always_sample

off

}

}
```

Here, while changing back and forth between load_file and save_file, we accidentally saved when we should have loaded, overwriting our good radiosity data with an absolute minimum of samples, since the pretrace values are set to 1.

There are two ways to address this. The first and most obvious way is to make a backup copy of the saved radiosity data. The second way is to use a conditional statement to decide whether POV-Ray should sample and store data or load it:

```
radiosity

{

#if
(

file_exists
(
"myRadData.rad"
)

)

pretrace_start

1

pretrace_end

1

load_file

"myRadData.rad"

always_sample

off

#else

pretrace_start

64
/
image_width

pretrace_end

8
/
image_width

save_file

"myRadData.rad"

#end

count

120

nearest_count

20

error_bound

.5

recursion_limit

3

}

}
```

This code checks to see whether or not there is saved radiosity data. If there is, it sets our pretrace to 1 and loads the file. If it can't find the file, it sets pretrace appropriately and saves the data for next time.

### Pitfalls and Work-arounds

#### Artifacts

Sometimes radiosity can cause artifacts, one of the most common causes of artifacts is infinite radiosity rays. A good rule is to place any radiosity scene inside a large inverse sphere to avoid any ray colliding with the background or sky_sphere.
Another potential cause of artifacts is coincident surfaces, sometimes a radiosity ray leaks through the corners of a box or between CSG seams, one work-around seems to be using a round box with a small radius instead of a box.
Non-zero ambient values in textures emit light when radiosity is on. This may be intended, but it makes textures look wrong in test renders without radiosity. Using declared variables to adjust the ambient and diffuse values or turn on/off lights and/or radiosity is recommended

#### Partial Output

Partial image output will affect radiosity! Partial image output can be a good way of splitting up slow renders across different machines; unfortunately, the radiosity calculations will be adversely affected. If you render a scene with radiosity with full image output, then do a series of re-renders of the same scene using partial output and stitch the pieces together, there will be a visible difference in the two images.
A possible workaround is to let a single machine calculate the radiosity on a lower resolution render without anti-aliasing, and use the save_file command to store the radiosity data. When the final version is run, ensure that the radiosity data is available to all machines or processes that need it, set always_sample to off, and both pretrace_start and pretrace_end to 1. This ensures that no radiosity calculations are performed and only the loaded data is used.

#### The effects of background and sky_sphere on Radiosity

The sky_sphere or background is always the exact color you specify as it isn't subject to lighting calculations, therefore it can be thought of as having a finish of ambient 1 diffuse 0. Since radiosity is affected by any luminous object in the scene, the sky_sphere or background will behave as a light source in your scene.

For outdoor scenes using radiosity, this is almost always desirable. If you don't want your objects to be affected by the brightness of the sky, you can set the background color to black, use save_file, then load your radiosity data for the final trace with the sky as you want it.

More information about radiosity in outdoor scenes can be found here.

### Notes

- Radiosity is slow and requires lots of calculations. Using it requires a lot of patience, but the results are very rewarding.

- Radiosity usage will vary from scene to scene and taste of the artist using it.

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

### Use the plane object

- 1 The Plane

- 1.1 A Simple Plane

- 1.2 Modifying a Plane

- 1.2.1 Full Source for Example #2

## The Plane

In POV, a plane is one of the five Infinite Solid Primitives. It is a flat surface that extends infinitely in all directions (unless clipped by an object). Planes are considered to have a volume, any point that is "under" the plane is inside, and any point that is "above" the plane is outside. This becomes important when you use a plane in CSG. A plane is defined in POV by a normal vector, and a float distance. The normal vector indicates the surface normal of the plane, and the distance is how far in that direction from the origin the surface begins.

### A Simple Plane

```
#include

"colors.inc"

plane

{

y
,

-
1

pigment

{
checker

White

Tan
}

}
```

The resultant plane (a red arrow has been added to illustrate the normal vector).

 Example #1: A simple plane.

This uses the default y vector for "up", it's easy enough to rotate a plane to the desired angles after creating it, but vrotate can be used to rotate the vector before defining the plane.

### Modifying a Plane

We can use the clipped_by object modifier to render only the portion of the plane within the clipping shape. Note, though, that even though the plane is by default a solid shape, the rest of the plane does not "fill" the clipping shape: only the surface (or underside) of the plane is visible.

To have a plane "fill out" the clipping shape, we must place the plane and clipping shape inside of an intersection statement.

 Example #2: Clipped plane vs. Intersected plane

In the above image, the plane section on the left is clipped by a sphere. The green and blue object on the right is an identical plane and sphere, intersected.

#### Full Source for Example #2

```
#include

"colors.inc"

#declare

Myfinish

=

finish

{

specular

0.00

roughness

1.00

ambient

0.25

}

// our ground plane:

plane

{

y
,

-
1

// this is identical to <0,1,0>, -1

pigment

{

checker

White

Tan

}

finish

{

Myfinish

}

}

// plane section on the left, using clipped_by:

plane

{

y
,

2

clipped_by

{

sphere
{

<
-
3
,
2
,
0
>,

2

}

}

pigment

{

checker

Black

Red

}

finish

{

Myfinish

}

}

// plane section on the right, using intersection:

intersection

{

plane

{

y
,

2

}

sphere

{

<
3
,
2
,
0
>,

2

}

pigment

{

checker

Blue

Green

}

finish

{

Myfinish

}

}

light_source

{

<
0
,
0
,
0
>

color

rgb

<
1
,
1
,
1
>

translate

<
10
,
100
,
-
100
>

*

10

}

camera

{

location

<
0
,
5
,
-
12
>

look_at

<
0
,
2
,
0
>

}
```

-

##

###

-

###

-
-

###

###

-
-
-

###

###

###

-
-
-
-
-

###

-
-
-

###

-
-
-
-
-
-

-
-

-
-
-

-
-

## Knowledgebase Pages

### Other Knowledgebase pages (summaries)

| Page | First line |
|---|---|
|  3DS Models | A listing of various 3DS model sites in alphabetical order. - 3D Models 6 Free - 3DS format - Book, Candles, Stero Cabinet, Hand and more. - 3Dimension 70+ free |
|  3D Solid Construction Methods | A listing of various 3D solid construction methods sites in alphabetical order. - 1998 SIGGRAPH Course: Subdivision for Modeling and Animation A Caltech course  |
|  Animation Utilities | A listing of various animation utility sites in alphabetical order. - AniTMT With the help of AniTMT you can create and calculate computer animated films. Its m |
|  Chemical Molecular Modelling Programs | A listing of chemical / molecular modelling sites in alphabetical order. - BioDesigner Biodesigner is a molecular modeling and visualization program for persona |
|  Commercial 3D Programs | A listing of commercial 3D programs sites in alphabetical order. - 3D Canvas 3D Canvas is a freeware real-time 3D modeling and animation tool that incorporates  |
|  Conversion Utilities | A listing of conversion utility sites in alphabetical order. - 3D Exploration - 3D viewer 3D Exploration is a viewer for Windows 95, 98 and NT with Explorer-lik |
|  Discussion Forums | A listing of various discussion forum sites in alphabetical order. - IHT CG Discussion Forum 3DstudioMAX, Lightwave, Maya, Softimage and more; the best italian  |
|  Fonts and Font Utilities | A listing of font and font utility sites in alphabetical order. - 1001 Free Fonts Download Free Fonts and Dingbats. Huge Collection of Freeware and Shareware Fo |
|  Fractals | A listing of fractals sites in alphabetical order. - Clifford A. Pickover' s home page Clifford Pickover is a deviser of graphics algorithms and a prolific popu |
|  Height Field Modelling Programs and Utilities | A listing of height field modelling program and utility sites in alphabetical order. - 3DEM Terrain Visualization and Flyby animation - asc2tga and Smooth When  |
|  Human and Animal Motion Studies | A listing of human and animal motion studies sites in alphabetical order. - Boids: Flocks, Herds, and Schools: a Distributed Behavioral Model. Complex behavior  |
|  Image and Texture Resources | A listing of various image and texture resource sites in alphabetical order. - 3D Texture Libraries and Backgrounds from TextureWorld Thousands of professional, |
|  LSystems | A listing of lsystems sites in alphabetical order. - FLEA - Fractal Life Engine Gallery Ruby LPARSER rewrite; new features with POV-Ray 3.5 export. - GDesign GD |
|  Large Mixed Collections | A listing of various large mixed collections model sites in alphabetical order. - 2001: A Space Odyssey - 3D Modeling Archive Site contains models from the movi |
|  Link Sites | A listing of various 3D link sites in alphabetical order. - 3D Animation Workshop 3d animation tutorials and resources - 3d links Site contains many links to 3D |
|  MAX WRL DXF COB OBJ IOB | A listing of various MAX, WRL, DXF, COB, OBF and IOB model sites in alphabetical order. - Commercial Model Site Some free Bryce Models, not for re-distribution. |
|  Magazines Lists and FAQs | A listing of various magazine, list, and FAQ sites in alphabetical order. - 3D World Magazine News, features, reviews and discussion of everything 3D. - 3D-ARK  |
|  Math and Geometry Resources and Information | A listing of math, geometry resources and information sites in alphabetical order. - 3D methods The good-looking textured light-sourced bouncy fun smart and str |
|  Mesh Utilities | A listing of mesh utility sites in alphabetical order. - Elcovision 10 Generate highly accurate 3D models from photographs. ELCOVISION 10 is the most accurate a |
|  Misc POV-Ray Utilities | A listing of miscellaneous POV-Ray utility sites in alphabetical order. - Colograms - Create Stereoscopic Printed Images Create spectacular 3D images widi your  |
|  Misc Resources | A listing of miscellaneous resources sites in alphabetical order. - Alphadictionary, formerly A Web of On-line Dictionaries A very comprehensive list of online  |
|  Misconceptions | ### Topic 1 The high compression achieved by the JPEG format is achieved by dropping out unnecessary colors. It's common to think that lossy compression means d |
|  Model Sites | A listing of various 3D model sites in alphabetical order. - Cadsymbols.com - Solidworks online parts library Commercial Online Mechanical Part Library for Soli |
|  Modelling Programs | A listing of modelling program sites in alphabetical order. - AC3D - HOME PAGE Powerful 3D modeller supporting POV and many other formats. - AERO Animation Edit |
|  POV-Ray Front End Programs | A listing of POV-Ray front end programs sites in alphabetical order. - Peflp Front End Front end for POV 3.1 from 2001, with a mouse selection of partial render |
|  POV-Ray Include Macro and Object Files | A listing of POV-Ray files categories in alphabetical order. - - - | Include Files | Macro Files | Object and Scene Files | End of Categories: Go Back to the Ca |
|  POV-Ray Links Collection | The POV-Ray Links Collection is a directory for many POV-Ray, ray-tracing and computer graphics related resources. If you are looking for some specific utility  |
|  POV-Ray Modelling Programs | A listing of POV-Ray modelling programs sites in alphabetical order. - AC3D - HOME PAGE Powerful 3d modeller supporting POV and many other formats. - Bicubic Pa |
|  POV-Ray Official Versions and Resources | - Benchmarking with POV-Ray This page at povray.org lists the requirements for running the new benchmark.pov file. - Documentacion de POV-Ray en Castellano - Of |
|  POV-Ray Patches v2.2 | A listing of version 2.2 patches sites in alphabetical order. - ISOSURFACES Implicit and parametrics surfaces first introduction, later included into the isosur |
|  POV-Ray Patches v3.0 | A listing of version 3.0 patches sites in alphabetical order. - POV-Ray for OpenVMS Port - POV-Ray v3.02 By Magnus Toneby - PovSP POV-Ray v3.01 with Spline Supp |
|  POV-Ray Patches v3.1 | A listing of version 3.1 patches sites in alphabetical order. - ClothRay ClothRay is a patched version of POV-Ray by Christophe Bouffartigue, that allows yuo to |
|  POV-Ray Patches v3.5 | A listing of version 3.5 patches sites in alphabetical order. - Density_File Extension Patch The density_file pattern in POV-Ray 3.5 is extremely flexible. It m |
|  POV-Ray People Pages | A listing of various POV-Ray people pages sites in alphabetical order. - Andrew Glassner's Home Page Not really a POV-Ray person but is a well known pioneer in  |
|  POV-Ray Questions and Tips | - 1 Introduction - 2 POV-Ray script language and rendering - 2.1 Tips and tricks to achieve useful things: - 2.2 Things that don't work as one expects: - 2.3 Ot |
|  POV-Ray Unofficial Patches and Front End Programs | A listing of patch and front end programs categories in alphabetical order. - - - - - | POV-Ray Front End Programs | POV-Ray Patches v3.1 | | POV-Ray Patches v3 |
|  Programming Sources | A listing of programming sources sites in alphabetical order. - 3D Data Formats Comprehensive list of 3D geometry formats. Also some 2D formats, audio formats,  |
|  Programs | A listing of 3D programs categories in alphabetical order. - - - - - - - - - - - - - - - - - - | Chemical/Molecular Modelling Programs | POV-Ray Modelling Progr |
|  Programs Under Development | A listing of programs under development sites in alphabetical order. - Gnome The GNOME project provides two things: The GNOME desktop environment, an intuitive  |
|  Prototyping | A listing of various prototyping sites in alphabetical order. - DPT Fast Rapid Prototyping Offers stereolithography, SLS (selective laser sintering) and FDM (fu |
|  Raytracing and Rendering Programs | A listing of raytracing and rendering programs sites in alphabetical order. - 3delight 3Delight is a fast RenderMan compliant renderer. 3Delight supports many o |
|  Resources | A listing of resources categories in alphabetical order. - - - - - - - | 3D solid construction methods | Misc Resources | | Fractals | Programming sources | | H |
|  Shells Spirals and Helixes | A listing of shells, spirals and helixes sites in alphabetical order. - ABWTwist: Twisted Shapes for POV-Ray ABWTwist is a utility to create data files for the  |
|  Smooth Triangle Artifact | What is the problem? There is a peculiar problem with smooth triangles which shows as a lighting artifact in certain cases. This can happen in individual smooth |
|  SoftWare Graphics Misc | A listing of miscellaneous software graphics sites in alphabetical order. - 3D Software Central Demo's - 3d - functional and crippled mix. Includes Real 3D, Tru |
|  Splines and Piping | A listing of splines and piping sites in alphabetical order. - Piper 1.0 Beta by Mike Weber uses BSplines to make Pipes in POV-Ray plus make spline based animat |
|  Texture Editors and Tools | A listing of texture editors and tools sites in alphabetical order. - Arabeske Home Page Arabeske Studio is a Java tool designed to help arabesque drawings. It  |
|  The Shadow Line Artifact | What is the problem? People often find an annoying problem when applying normal modifier patterns to objects. It is said that one image tells more than a thousa |
|  Tutorials | A listing of various tutorial sites in alphabetical order. - 3D Cafe's Free Stuff Tutorials covering many modelling programs plus a couple related to POV-Ray an |
|  University Graphics Programs | A listing of university graphics programs sites in alphabetical order. - CAS Supercomuting Front page of the Centre for Astrophysics and Supercomputing at Swinb |
|  Windows Vista | This article is a stub. Please help enhance the POV-Wiki by expanding it. This page summarizes some of the known issues in using POV-Ray with Windows Vista®. ## |