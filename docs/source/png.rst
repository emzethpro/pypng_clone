PNG Image Handling Tutorial
===========================

This tutorial provides a comprehensive guide to using the `Png` class for creating, manipulating, and saving PNG image files. The `Png` class is designed to work seamlessly with the `File` class to manage PNG file structures.

Introduction
------------

The `Png` class includes methods for:

- Setting image dimensions, color types, and bit depths.
- Adding colors to palettes.
- Manipulating individual pixels.
- Applying gamma corrections.
- Writing PNG files.
- Reading PNG files and parsing chunks.

**Key Features**:
- Supports custom color palettes.
- Includes cropping, gamma correction, and transparency handling.
- Reads and writes PNG files.

Code Walkthrough
----------------

Class Definition
----------

.. code-block:: python

   from File import File

   class Png:
       """Class containing methods for creation of PNG files."""

Initial Values
----------

Default values for the image are defined as class-level attributes:

.. code-block:: python

       _height = 1
       _width = 1
       _color_type = 2
       _bit_depth = 8
       _default_color = [0, 0, 0]  # black
       _image = [[_default_color]]

       _palette = []
       _background_color = None
       _simple_alpha = None
       _gamma = None
       _text = None

Key Methods
----------

Adding Colors to a Palette
----------

Adds a color to the palette, ensuring it does not exceed the maximum size.

.. code-block:: python

       def add_color_to_palette(self, color):
           if self._palette is None:
               raise ValueError("Adding to a non-existent palette")
           if len(self._palette) > 255:
               raise ValueError("Palette overflow")
           self._palette.append(color)
           return [len(self._palette)]

Verifying Colors
----------
             
Verifies if a color adheres to the PNG file's format based on color type and bit depth.

.. code-block:: python

       def verify_color(self, color):
           """Verifies that a color is in the right format."""
           samples_per_pixel = {
               0: 1, 2: 3, 3: 1, 4: 2, 6: 4
           }
           max_sample_value = 2 ** self._bit_depth - 1

           if len(color) != samples_per_pixel[self._color_type]:
               return True
           for i in color:
               if i > max_sample_value:
                   return True
           return False

Cropping the Image
----------
             
Crops the image within the specified rectangle.

.. code-block:: python

       def crop(self, x0, y0, x1, y1):
           """Crops the image within rectangle (x0, y0) to (x1, y1)."""
           h, w = self._height, self._width
           if x0 >= w or x1 + 1 >= w or y0 >= h or y1 + 1 >= h:
               return True
           self._image = self._image[y0 : y1 + 1]
           for line in self._image:
               line = line[x0 : x1 + 1]

Setting and Getting Pixels
----------
             
Set or retrieve a pixel's color.

.. code-block:: python

       def set_pixel(self, x, y, color):
           """Sets pixel at (x, y)."""
           self._image[y][x] = color

       def get_pixel(self, x, y):
           """Returns the pixel at (x, y)."""
           return self._image[y][x]

Gamma Correction
----------
             
Applies gamma correction to the entire image.

.. code-block:: python

       def gamma_correct(self, gamma):
           """Gamma corrects every pixel in the image."""
           for scanlines in self._image:
               for pixels in scanlines:
                   for i in range(len(pixels)):
                       pixels[i] = int(((pixels[i] / 256.0) ** gamma) * 256)
           return self

Setting Dimensions
----------
             
Sets the image's width and height, filling in new areas with the default color.

.. code-block:: python

       def set_dimensions(self, width, height):
           """Sets image dimensions."""
           if width < 1 or height < 1:
               return True

           if self._height < height:
               difference = height - self._height
               for _ in range(difference):
                   self._image.append([self._default_color for _ in range(self._width)])
           else:
               self._image = self._image[:height]
           self._height = height

           if self._width < width:
               difference = width - self._width
               for scanlines in self._image:
                   scanlines.extend([self._default_color] * difference)
           else:
               for line in self._image:
                   line = line[:width]
           self._width = width
           return False

Writing the Image to File
----------
             
Writes the image to a PNG file.

.. code-block:: python

       def write(self, of, interlaced=False):
           """Writes image to file."""
           imagefile = File()
           imagefile.make_IHDR(
               self._width, self._height, self._bit_depth, self._color_type
           )
           if self._gamma is not None:
               imagefile.make_gAMA(self._gamma)
           if self._color_type == 3:
               imagefile.make_PLTE(self._palette)
           if self._background_color is not None:
               imagefile.make_bKGD(self._background_color)
           if self._simple_alpha is not None:
               imagefile.make_tRNS(self._simple_alpha, self._color_type)
           imagefile.make_IDAT(self._image)
           if self._text is not None:
               imagefile.make_tEXT(self._text)
           imagefile.make_IEND()
           imagefile.write(of)
           return True

Reading a PNG File
----------
             
Parses the PNG file and loads its data into a `Png` instance.

.. code-block:: python

       def read(self, ifname):
           """Reads a PNG file and returns a new Png instance."""
           imagefile = File(ifname)
           chunks = imagefile.read()
           if len(chunks) != 3:
               print("PNG file must consist exactly of IHDR, IDAT, and IEND chunks")
               return True
           for chunk in chunks:
               if self._parse_chunk(chunk):
                   return True
           return False

Initialization
----------
             
The constructor sets the image's initial dimensions, color type, and bit depth.

.. code-block:: python

       def __init__(self, width=1, height=1, color_type=2, bit_depth=8):
           """Initializes the PNG object."""
           self.set_dimensions(width, height)
           self.set_bit_depth(bit_depth)
           self.set_color_type(color_type)

Conclusion
----------

The `Png` class provides a versatile interface for creating and manipulating PNG files programmatically. With methods for setting dimensions, colors, and metadata, it is a powerful tool for handling PNG images in Python.

