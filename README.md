pyscroll
========

For Python 2.7 & 3.3+ and Pygame 1.9

A simple & fast module for animated scrolling maps for your new or existing game.


Introduction
============

pyscroll is a generic module for making a fast scrolling image with PyGame.  It
uses a lot of magic to get reasonable framerates out of PyGame.  It only exists
to draw a map.  It doesn't load images or data, so you can use your own custom
data structures, tile storage, ect.

pyscroll is compatible with pytmx (https://github.com/bitcraft/pytmx), so you
can use your Tiled maps.  It also has out-of-the-box support for PyGame Sprites.

The included class, BufferedRenderer, gives great framerates, supports layered
rendering and can draw itself.  It supports fast layered tile rendering with
alpha channel support.  It also includes animated tile rendering and zooming!


Use It Like a Camera
====================

In order to further simplify using scrolling maps, pyscroll includes a pygame
Sprite Group that will render all sprites on the map and will correctly
draw them over or under tiles.  Sprites can use their Rect in world coordinates,
and the Group will work like a camera, translating world coordinates to screen
coordinates.

Zooming is a new feature and should operate quickly on most computers.  Be aware
that it is cheap to operate a zoomed view, but expensive to do the actual zooming.
This means that its easy to zoom the map once, but don't expect it to work quickly
if you want to do an animated zoom into something.

Its useful to make minimaps or create simple chunky graphics.


Features
========

- Zoom it like a camera
- Fast and small footprint
- Animated tiles
- Layered drawing for tiles
- Drawing and scrolling shapes
- Pygame Group included
- Pixel alpha and colorkey tilesets are supported


Shape Drawing
=============

pyscroll has a new experimental feature for drawing shapes to the map and
scrolling them as part of the map.  This can be useful for game that need to
draw arbitrary shaped objects.

The feature requires pytmx, and that the map files be created in the Tiled Map
Editor.  The advantage of of using pyscroll for shape drawing is that pyscroll
can draw the shapes much more efficiently that simply drawing the shapes each
frame.

This feature is experimental at best.  Your comments and support is appreciated!

* Currently, shapes will not draw over sprites, only under.


Installation
===============================================================================

Install from pip

    pip install pyscroll


You can also manually install it

    python setup.py install
    
    
New Game Tutorial
=================

This is a quick guide on building a new game with pyscroll and pygame.  It uses
the PyscrollGroup for efficient rendering.  You are free to use any other pygame
techniques and functions.

Open quest.py in the tutorial folder for a gentle introduction to pyscroll and
the PyscrollGroup for PyGame.  There are plenty of comments to get you started.

The Quest demo shows how you can use a pyscroll group for drawing, how to load
maps with PyTMX, and how pyscroll can quickly render layers.  Moving under some
tiles will cause the Hero to be covered.


Example Use with PyTMX
======================

pyscroll and pytmx can load your maps from Tiled and use you PyGame Sprites.

```python
import pyscroll
from pytmx.util_pygame import load_pygame

# Load TMX data
tmx_data = load_pygame("desert.tmx")

# Make data source for the map
map_data = pyscroll.TiledMapData(tmx_data)

# Make the scrolling layer
screen_size = (400, 400)
map_layer = pyscroll.BufferedRenderer(map_data, screen_size)

# make the PyGame SpriteGroup with a scrolling map
group = pyscroll.PyscrollGroup(map_layer=map_layer)

# Add sprites to the group
group.add(sprite)

# Center the layer and sprites on a sprite
group.center(sprite.rect.center)

# Draw the layer
group.draw(screen)

# adjust the zoom (out)
map_layer.zoom = .5

# adjust the zoom (in)
map_layer.zoom = 2.0
```

#### Load with alpha channel tilesets
```python
map_layer = pyscroll.BufferedRenderer(map_data, screen_size, alpha=True)
```

#### Load with colorkey tilesets
```python
map_layer = pyscroll.BufferedRenderer(map_data, screen_size, colorkey=(255, 0, 255))
```


Adapting Existing Games / Map Data
==================================

pyscroll can be used with existing map data, but you will have to create a
class to interact with pyscroll or adapt your data handler.  Try to make it
follow the same API as the TiledMapData adapter and you should be fine.

There is a good possibility that tile animations will not work for custom
map types (only works with pytmx).  I will investigate this in the future.

The following do not require pytmx, you can use your own data format.

#### Give pyscroll surface to layer into the map

pyscroll can use a list of surfaces and render them on the map, taking account
their layer position.

```python
map_layer = pyscroll.BufferedRenderer(map_data, map_size)

# just an example for clarity.  here's a made up game engine

def draw():
   surfaces = list()
   for game_object in my_game_engine:

      # pyscroll uses normal pygame surfaces
      surface = game_object.get_surface()
   
      # pyscroll will draw surfaces in screen coordinates, so translate them
      # you need to use a rect to handle tiles that cover surfaces.
      rect = game_object.get_screen_rect()
   
      # the list called 'surfaces' is required for pyscroll
      # notice the layer.  this determines which layers the sprite will cover.
      # layer numbers higher than this will cover the surface
      surfaces.append((surface, rect, game_object.layer))

   # tell pyscroll to draw to the screen, and use the surfaces supplied
   map_layer.draw(screen, screen.get_rect(), surfaces)
```
