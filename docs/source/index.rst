PyPNG Documentation
==================

Overview
--------

PyPNG is a pure Python library for handling PNG (Portable Network Graphics) images. It provides a simple, Pythonic interface for reading, writing, and manipulating PNG files without any external dependencies.

Key Features
~~~~~~~~~~~

* Pure Python implementation
* No external dependencies
* Support for all PNG color modes
* Handles both reading and writing PNG files
* Compatible with Python 3.5+

Installation
-----------

Using pip
~~~~~~~~~

.. code-block:: bash

   python -m pip install git+https://gitlab.com/drj11/pypng@pypng-0.20231004.0

Manual Installation
~~~~~~~~~~~~~~~~~

Copy ``png.py`` directly into your project:

.. code-block:: bash

   curl -LO https://gitlab.com/drj11/pypng/-/raw/main/code/png.py

From Source
~~~~~~~~~~

.. code-block:: bash

   python -m pip install .

Basic Usage
----------

Reading PNG Files
~~~~~~~~~~~~~~~

.. code-block:: python

   import png

   # Read a PNG file
   reader = png.Reader(filename='image.png')
   width, height, pixels, metadata = reader.read()

   # Access basic information
   print(f"Image size: {width}x{height}")
   print(f"Metadata: {metadata}")

   # Convert pixels to list
   pixel_list = list(pixels)

Writing PNG Files
~~~~~~~~~~~~~~~

.. code-block:: python

   import png

   # Create from array
   image_2d = [[255, 0, 0],    # Red pixel
               [0, 255, 0],    # Green pixel
               [0, 0, 255]]    # Blue pixel

   # Save as PNG
   png.from_array(image_2d, 'RGB').save("output.png")

   # Write with more control
   writer = png.Writer(width=3, height=3, bitdepth=8, greyscale=False)
   with open('output.png', 'wb') as f:
       writer.write(f, image_2d)

Core Features
------------

Color Modes
~~~~~~~~~~

PyPNG supports all standard PNG color modes:

* Greyscale (L)
* RGB
* Palette-based
* Greyscale with alpha (LA)
* RGB with alpha (RGBA)

Bit Depths
~~~~~~~~~

Supported bit depths:

* 1 (binary)
* 2
* 4
* 8
* 16

Metadata Handling
~~~~~~~~~~~~~~~

.. code-block:: python

   # Reading metadata
   reader = png.Reader(filename='image.png')
   metadata = reader.read_flat()[3]
   print(f"Color type: {metadata['greyscale']}")
   print(f"Alpha channel: {metadata['alpha']}")
   print(f"Bit depth: {metadata['bitdepth']}")

   # Writing with metadata
   writer = png.Writer(
       width=100,
       height=100,
       bitdepth=8,
       greyscale=False,
       compression=9,
       gamma=2.2
   )

Advanced Usage
-------------

Custom Iterators
~~~~~~~~~~~~~~

.. code-block:: python

   import png

   def pixel_generator(width, height):
       for y in range(height):
           row = []
           for x in range(width):
               # Generate pixel values based on position
               row.append((x * 255 // width, y * 255 // height, 0))
           yield row

   # Create PNG from generator
   width, height = 256, 256
   writer = png.Writer(width, height, bitdepth=8, greyscale=False)
   with open('gradient.png', 'wb') as f:
       writer.write(f, pixel_generator(width, height))

Image Transformations
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   def rotate_90_degrees(pixels, width, height):
       """Rotate image 90 degrees clockwise."""
       rotated = []
       for x in range(width):
           row = []
           for y in range(height-1, -1, -1):
               row.extend(pixels[y][x*3:(x+1)*3])
           rotated.append(row)
       return rotated

   # Usage
   reader = png.Reader(filename='input.png')
   width, height, pixels, metadata = reader.asRGB8()
   pixels = list(pixels)
   rotated = rotate_90_degrees(pixels, width, height)

   writer = png.Writer(height, width, bitdepth=8, greyscale=False)
   with open('rotated.png', 'wb') as f:
       writer.write(f, rotated)

API Reference
------------

Reader Class
~~~~~~~~~~~

.. py:class:: Reader

   PNG file reader.

   .. py:method:: read()

      Read PNG file and return tuple of:
      (width, height, pixels, metadata)

   .. py:method:: asRGB8()

      Read PNG file and convert to 8-bit RGB format.
      Returns (width, height, pixels, metadata)

   .. py:method:: asRGBA8()

      Read PNG file and convert to 8-bit RGBA format.
      Returns (width, height, pixels, metadata)

Writer Class
~~~~~~~~~~~

.. py:class:: Writer

   PNG file writer.

   .. py:method:: __init__(width, height, bitdepth=8, greyscale=False, alpha=False, palette=None, compression=None)

      Initialize PNG writer with image properties.

      :param width: Image width in pixels
      :type width: int
      :param height: Image height in pixels
      :type height: int
      :param bitdepth: Bits per sample (1, 2, 4, 8, or 16)
      :type bitdepth: int
      :param greyscale: True for grayscale images
      :type greyscale: bool
      :param alpha: True if alpha channel present
      :type alpha: bool
      :param palette: Optional color palette
      :type palette: list
      :param compression: Compression level (0-9)
      :type compression: int

   .. py:method:: write(file, rows)

      Write PNG file.

      :param file: File object (must be opened in binary mode)
      :param rows: Iterable of rows (each row is an iterable of pixels)

Examples
--------

Creating a Gradient
~~~~~~~~~~~~~~~~~

.. code-block:: python

   import png

   def create_gradient(width, height):
       image = []
       for y in range(height):
           row = []
           for x in range(width):
               # Create RGB gradient
               row.extend([
                   int(255 * x/width),    # Red
                   int(255 * y/height),   # Green
                   0                      # Blue
               ])
           image.append(row)
       return image

   # Create and save gradient
   width, height = 256, 256
   gradient = create_gradient(width, height)
   writer = png.Writer(width, height, bitdepth=8, greyscale=False)
   with open('gradient.png', 'wb') as f:
       writer.write(f, gradient)

Reading and Modifying Pixels
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   import png

   # Read image
   reader = png.Reader(filename='input.png')
   width, height, pixels, metadata = reader.asRGB8()
   pixels = list(pixels)

   # Modify pixels (invert colors)
   modified = []
   for row in pixels:
       new_row = []
       for i in range(0, len(row), 3):
           r, g, b = row[i:i+3]
           new_row.extend([255-r, 255-g, 255-b])
       modified.append(new_row)

   # Save modified image
   writer = png.Writer(width, height, bitdepth=8, greyscale=False)
   with open('inverted.png', 'wb') as f:
       writer.write(f, modified)

Troubleshooting
--------------

Common Issues and Solutions
~~~~~~~~~~~~~~~~~~~~~~~~~

1. **Image appears corrupted**

   * Ensure correct bit depth is specified
   * Verify pixel data format matches writer configuration
   * Check if file is opened in binary mode

2. **Memory issues with large images**

   * Use generators instead of lists
   * Process image in chunks
   * Consider using memory-mapped files

3. **Color mode problems**

   * Verify color mode matches data format
   * Use appropriate Reader methods (asRGB8, asRGBA8)
   * Check if palette is properly formatted

Error Messages
~~~~~~~~~~~~

* ``ValueError: Incorrect array size``: Check if width and height match pixel data
* ``png.FormatError``: Verify PNG file is valid and not corrupted
* ``IOError``: Ensure file permissions and path are correct

Best Practices
~~~~~~~~~~~~

1. Always use context managers with file operations
2. Close files explicitly when not using context managers
3. Verify image dimensions before processing
4. Use appropriate bit depth for your needs
5. Handle exceptions appropriately
6. Test with small images before processing large ones

********
Contents
********

.. toctree::
   :maxdepth: 2

   style-guide
   example/index

Additional Resources
------------------

* `PyPNG home page <https://gitlab.com/drj11/pypng/>`_
* `PyPNG mailing list <https://groups.google.com/forum/#!forum/pypng-users>`_
* `Official documentation <https://drj11.gitlab.io/pypng/>`_
