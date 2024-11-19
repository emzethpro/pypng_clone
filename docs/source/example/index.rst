PyPNG Example
=============

Writing PNG Files
---------------

Basic Writing
~~~~~~~~~~~~

The simplest way to write a PNG is to make a 2D array and use ``png.from_array``:

.. code-block:: python

    import png

    rows = [
        [0, 1, 1, 1, 1, 1, 1, 0],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [1, 0, 1, 0, 0, 1, 0, 1],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [1, 0, 1, 0, 0, 1, 0, 1],
        [1, 0, 0, 1, 1, 0, 0, 1],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [0, 1, 1, 1, 1, 1, 1, 0],
    ]
    image = png.from_array(rows, "L;1")
    image.save("smile.png")

.. note::
   While Python doesn't have true 2D arrays, you can use ``array.array`` for space-efficient horizontal storage, 
   or iterators for vertical space efficiency.

The mode argument ``"L;1"`` creates a greyscale image (``L``) with a bitdepth of 1. Bitdepth can range from 1 to 16:

* Bitdepth 1: Maximum value 1
* Bitdepth 8: Maximum value 255 (default)
* Bitdepth 16: Maximum value 65,535

Available modes include:

* ``"L"``: Greyscale
* ``"RGB"``: Color
* ``"LA"``: Greyscale with alpha
* ``"RGBA"``: Color with alpha

Using Writer Objects
~~~~~~~~~~~~~~~~~~

For more flexibility, you can use the ``png.Writer`` class directly:

.. code-block:: python

    import png
    
    rows = [
        [0, 1, 1, 1, 1, 1, 1, 0],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [1, 0, 1, 0, 0, 1, 0, 1],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [1, 0, 1, 0, 0, 1, 0, 1],
        [1, 0, 0, 1, 1, 0, 0, 1],
        [1, 0, 0, 0, 0, 0, 0, 1],
        [0, 1, 1, 1, 1, 1, 1, 0],
    ]
    
    # Define RGB palette
    palette = [(0x55, 0x55, 0x55), (0xFF, 0x99, 0x99)]
    
    # Create writer with palette
    w = png.Writer(size=(8, 8), palette=palette, bitdepth=1)
    
    # Write to file
    with open("pal.png", "wb") as f:
        w.write(f, rows)

Working with Color
----------------

Basic RGB Images
~~~~~~~~~~~~~~

For color images, each row contains RGB triples:

.. code-block:: python

    import png
    
    # Each pixel is (R,G,B)
    rows = [
        (255,0,0, 0,255,0, 0,0,255),      # Row 1: Red, Green, Blue
        (128,0,0, 0,128,0, 0,0,128)       # Row 2: Dark Red, Dark Green, Dark Blue
    ]
    image = png.from_array(rows, "RGB")
    image.save("swatch.png")

Advanced Color Examples
~~~~~~~~~~~~~~~~~~~~

Creating all possible 3-bit colors (8 colors):

.. code-block:: python

    import itertools
    import png
    
    # Generate all possible RGB combinations for 1-bit per channel
    pixels = itertools.product([0,1], repeat=3)
    
    # Flatten the pixels into a single row
    row = list(itertools.chain(*pixels))
    
    # Create and save image
    png.from_array([row], "RGB;1").save("rgb.png")

Random Color Generation
~~~~~~~~~~~~~~~~~~~~

Generate a random 64×64 RGB image:

.. code-block:: python

    import random
    import png

    width = 64
    height = 64
    values_per_row = 3 * width  # RGB needs 3 values per pixel

    # Create 2D array
    matrix = [[0] * values_per_row for _ in range(height)]

    # Fill with random values
    for y in range(height):
        for x in range(values_per_row):
            matrix[y][x] = random.randint(0, 255)

    # Save image
    png.from_array(matrix, "RGB").save("random.png")

Reading PNG Files
---------------

Basic Reading
~~~~~~~~~~~

Create a ``Reader`` object and use its ``read()`` method:

.. code-block:: python

    import png
    
    # Create reader from file
    reader = png.Reader(filename='image.png')
    
    # Read image data
    width, height, pixels, metadata = reader.read()
    
    # metadata contains image information:
    # - greyscale: True/False
    # - alpha: True/False
    # - bitdepth: bits per sample
    # - interlace: interlace method
    # - gamma: gamma value

.. note::
   The ``pixels`` object may be an iterator or sequence depending on how
   efficiently PyPNG can process the image.

Working with NumPy
----------------

Reading to NumPy Arrays
~~~~~~~~~~~~~~~~~~~~

While NumPy support is minimal, you can convert PNG data to NumPy arrays:

.. code-block:: python

    import numpy
    import png
    
    reader = png.Reader(filename='image.png')
    width, height, pixels, metadata = reader.read()
    
    # Convert to NumPy array
    array = numpy.vstack([numpy.uint8(row) for row in pixels])

Writing from NumPy Arrays
~~~~~~~~~~~~~~~~~~~~~~

.. warning::
   NumPy arrays may need special handling due to differences from Python sequences.
   Consider using ``.copy()`` or converting to ``array.array`` if you encounter issues.

Additional Notes
--------------

* Always open files in binary mode (``'wb'`` for writing, ``'rb'`` for reading)
* Use context managers (``with`` statements) when handling files
* Consider using iterators for memory efficiency with large images
* Be mindful of bitdepth when working with pixel values
