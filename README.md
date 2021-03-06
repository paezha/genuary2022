
<!-- README.md is generated from README.Rmd. Please edit that file -->

# genuary2022

<!-- badges: start -->
<!-- badges: end -->

This repository is for my first [genuary](https://genuary.art/), a month
of generative art. A great opportunity to learn something new
coding-wise, while indulging in some artsy activities. I also want to
test my [{MexBrewer}](https://paezha.github.io/MexBrewer/) color
palettes.

## Day 1: Draw 10,000 of something

5,000 horizontal lines and 5,000 vertical lines.

![10000 of
something](01-10000-of-something/README_files/figure-gfm/10000-of-something-alacena-1.png)

## Day 2: Dithering

Dithering the Aztec calendar.

![Dithering
Calendar](02-dithering/README_files/figure-gfm/dithering-concha-1.png)

Bonus Fridas!

![](02-dithering/README_files/figure-gfm/dithering-frida-aurora-1.png)

![](02-dithering/README_files/figure-gfm/dithering-frida-concha-1.png)

![](02-dithering/README_files/figure-gfm/dithering-frida-frida-1.png)

## Day 3: Space

Negative space: Escher mosaics that use the dithering I learned on Day
2.

![](03-space/README_files/figure-gfm/space-escher-revolucion-1.png)

Detail:

![](03-space/README_files/figure-gfm/space-escher-revolucion-detail-1.png)

Bonus José Guadalupe Posada!

![](03-space/README_files/figure-gfm/space-posada-tierra-1.png)

## Day 4: The next next Fidenza

Learning about flow fields with this one.

![](04-fidenza/README_files/figure-gfm/paths-ex4-revolucion-1.png)

## Day 5: Destroy a square

I decided to try something minimalist exploiting an effect that I
accidentally discovered while learning the Fidenza algorithm.

![](05-break-a-square/README_files/figure-gfm/break-square-atentado-1.png)

## Day 6: Trading styles

Not really a trade, more like shamelessly copying some code from
[Georgios
Karamanis](https://github.com/gkaramanis/aRtist/tree/main/genuary) whose
generative art I like (plus, he is generous enough to make his code
[public](https://github.com/gkaramanis/aRtist/tree/main/genuary/2021/2021-3)).

This is Frida using the `Frida` palette from
[{MexBrewer}](https://paezha.github.io/MexBrewer/).

![](06-trade-styles/frida-frida.png)

## Day 7: Sol LeWitt Wall Painting

I did not know Sol LeWitt or his art. He did not believe that the hand
that painted was the real artist, but the mind that conceived the
painting. He gave instructions and hired drafters to put
pencil/brush/whatever to the wall. So this was generative art in analog
format: LeWitt gave the algorithm (that was the art) and someone did the
painting.
[These](https://observer.com/2012/10/here-are-the-instructions-for-sol-lewitts-1971-wall-drawing-for-the-school-of-the-mfa-boston/)
are an example of the kind of instructions he used:

> “On a wall surface, any  
> continuous stretch of wall,  
> using a hard pencil, place  
> fifty points at random.  
> The points should be evenly  
> distributed over the area  
> of the wall. All of the  
> points should be connected  
> by straight lines.”

For this day I took a number of random points from a planar network and
then connected them using, not straight lines, but shortest paths on the
network.

![](07-sol-lewitt-wall-drawing/slw_animation.gif)

## Day 8: Single curve only

A ![\\Gamma](https://latex.codecogs.com/png.latex?%5CGamma "\Gamma")
function calibrated by [Anastasia
Soukhov](https://soukhova.github.io/AccessPack/) using data from the
Greater Toronto and Hamilton Area for commute to work. Color palettes
from [{MexBrewer}](https://paezha.github.io/MexBrewer/).

### Ronda

![](08-single-curve-only/single-curve-ronda.png)

### Alacena

![](08-single-curve-only/single-curve-alacena.png)

### Revolucion

![](08-single-curve-only/single-curve-revolucion.png)

### Alacena

![](08-single-curve-only/single-curve-atentado.png)

## Day 9: Architecture

A cartoonish skyline.

### Ronda

![](09-architecture/skyline-ronda.png)

### Atentado

![](09-architecture/skyline-atentado.png)

### Revolucion

![](09-architecture/skyline-revolucion.png)

### Alacena

![](09-architecture/skyline-alacena.png)

## Day 12: Circle packing

I took some towers from architecture on Day 9 and packed them with
circles.

### Ronda

![](12-circle-packing/circle-packing-ronda.png)

### Alacena

![](12-circle-packing/circle-packing-alacena.png)

## Day 13: 800x80

Just an excuse to pack more stuff.

![](13-800x80/800x80-2.png)

## Day 15: Sand

Even more circle packing.

![](15-sand/sand-atentado-revolucion.png)

## Day 16: Color gradients gone wrong

For this prompt I revisited a machine learning exercise but made it go
back in time, and forced a self-organizing map to become
self-disorganizing.

![](16-gradients-gone-wrong/ggw_animation_ronda.gif)

## Day 17: Color gradients gone wrong

Each overlapping hatching pattern uses a different color. The hatching
patterns were cut using truchet tiles.

![](17-three-colors/three-colors-aurora.png)

## Day 19: Text/typography

For this entry I made some waves using a randomly chose fragment of text
from Melville’s Moby Dick.

![](19-text/text-sea-split-sequentially-revolucion.png)

## Day 21: Combine two previous pieces to create something new

Here I combined dithering and text to create this portrait of Melville
with text from Moby Dick.

![](21-combine-two/melville-text-tierra.png)

## Day 23: Abstract vegetation

I did not have today to do any original coding, but I was very curious
to explore Pierre Casadebaig’s
[{generate}](https://github.com/picasa/generate) package, so this was a
great chance to look into it. These are the results.

![](23-abstract-vegetation/abstract-vegetation-atentado.png)

![](23-abstract-vegetation/abstract-vegetation-tierra.png)

## Day 25: Perspective

This is just a simple experiment with two-point perspective: I must
admit that I did not know the trigonometry involved, so it was a good
refresher of trig relations.

![](25-perspective/perspective-bw.png)

## Day 27: Hexadecimal colors

The prompt give five colors to work with: : #2E294E, #541388, #F1E9DA,
#FFD400, #D90368.

I was not super-inspired by the colors, so used the opportunity to
improve package [{truchet}](https://paezha.github.io/truchet/) to
assemble mosaics with non-random placement of tiles. The background uses
two of the colors, the mosaic three. The noise in the background I
copied from Jaquie Tran’s
[code](https://github.com/jacquietran/genuary_2022/blob/main/R/20220125.R).

![](27-hex-colors/hex-colors-1.png)

## Day 28: Self-portrait

Self-portrait made with the first 80,447 digits of pi.

![](28-self-portrait/me-pi-tierra.png)

## Day 29: Isometric perspective

I made some figures created with tiles of isometric cubes.

![](29-isometric-perspective/isometric-bw-4.png) ## Day 31: Negative
space

I made a negative cube. I had to adjust my ambitions for this one.
Results are in good measure accidental, as I code things and am
surprised by the behavior of
[{gganimate}](https://gganimate.com/index.html).

![](31-negative-space/animated-negative-space.gif)

## In conclusion

This was FUN and I learned a lot and refreshed my knowledge of
trigonometry. I got to experiment with colors, shapes, and compositions.
I’d like to explore more the wavescapes or cloudscapes, mosaics, both
using Truchet tiles and isometric figures, asemic glyphs and writing,
and more stuff with portraits. Oh, and circle packing. I can see this
keeping me interested and entertained for some time.
