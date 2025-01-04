PNG Chunk Handling Tutorial
===========================

This tutorial explains how to work with PNG file chunks using a `Chunk` class in Python. The `Chunk` class simplifies managing PNG file chunks by encapsulating their attributes and operations, including setting names, handling data, calculating CRCs, and compressing chunk data.

Introduction
------------

The `Chunk` class includes methods for:

- Setting the chunk name and verifying its validity.
- Appending and retrieving data in network byte order.
- Calculating CRC values for chunks.
- Converting chunks into binary representations.
- Compressing and decompressing chunk data.
- Reading and verifying chunks from a PNG file.

**Key Features**:
- Automatic CRC calculation and validation.
- Support for common chunk types like `IHDR`, `IDAT`, and `IEND`.
- Simplified handling of binary data.

Code Walkthrough
----------------

Required Imports
------------

The following libraries are used in this implementation:

.. code-block:: python

   import crc
   import zlib
   import pybin

Chunk Identifiers
------------

The `chunk_ids` list contains predefined chunk names used in PNG files:

.. code-block:: python

   chunk_ids = ['', 'IHDR', 'IDAT', 'IEND', 'bKGD', 'tRNS', 'PLTE', 'tEXT', 'gAMA']

Class Definition
------------

The `Chunk` class encapsulates operations for PNG file chunks:

.. code-block:: python

   class Chunk:
       """Container class for PNG file chunks."""

       _name = ''
       _data = []

Calculating CRC
------------

The `_calc_crc` method calculates the CRC checksum for the chunk:

.. code-block:: python

       def _calc_crc(self):
           """Calculate the chunk CRC."""
           bytefield = []

           # Append chunk name and data for CRC calculation
           for c in self._name:
               bytefield.append(pybin.ctoi(c))
           bytefield += self._data

           # Calculate and return CRC
           return crc.crc32(bytefield)

Setting Chunk Name
------------

The `set_name` method validates and sets the chunk name:

.. code-block:: python

       def set_name(self, name):
           """Sets chunk name to a four-character identifier."""
           if name not in chunk_ids:
               return True
           self._name = name
           return False

Appending Data
------------

The `append` method adds values to the chunk data:

.. code-block:: python

       def append(self, value, n=1):
           """Appends value in network order to the chunk data."""
           if n == 1:
               self._data.append(value)
           elif n == 2:
               self._data.append(value // 256)
               self._data.append(value % 256)
           else:
               stack = []
               for _ in range(n):
                   stack.append(value % 256)
                   value //= 256
               while stack:
                   self._data.append(stack.pop())
           return self

Converting to Binary
------------

The `convert` method converts the chunk to a binary representation:

.. code-block:: python

       def convert(self, binarydata=None):
           """Return chunk data as a binary string."""
           output = ''
           output += pybin.iton(len(self._data))
           output += self._name

           # Append chunk data
           if binarydata:
               output += binarydata
           else:
               for i in self._data:
                   output += pybin.itoc(i)

           # Append CRC and return
           output += pybin.iton(self._calc_crc())
           return output

Compressing Data
------------

The `compress` method compresses the chunk data using the DEFLATE algorithm:

.. code-block:: python

       def compress(self):
           """Return chunk string with compressed data field."""
           data = ''.join(pybin.itoc(i) for i in self._data)
           compressed_data = zlib.compress(data)

           # Update data for CRC calculation
           self._data = [pybin.ctoi(c) for c in compressed_data]
           return self.convert(compressed_data)

Reading a Chunk
------------

The `read` method initializes the chunk from a binary string and validates its CRC:

.. code-block:: python

       def read(self, string):
           """Reads and validates a chunk from a binary string."""
           if not isinstance(string, str):
               raise ValueError("Input must be a string")

           # Extract chunk length, name, and CRC
           length = len(string) - 8
           self.set_name(string[:4])
           contents = string[4:-4]
           expected_crc = pybin.ntoi(string[-4:])

           # Convert data to integers
           self._data = [pybin.ctoi(c) for c in contents]

           # Validate CRC
           if self._calc_crc() != expected_crc:
               raise ValueError(f"CRC mismatch for {self._name}")

           # Decompress IDAT chunk data
           if self._name == 'IDAT':
               decompressed = zlib.decompress(contents)
               self._data = [pybin.ctoi(c) for c in decompressed]

           return self

Initialization
------------

The constructor initializes the chunk:

.. code-block:: python

       def __init__(self, name='', data=[]):
           """Constructor defaults to an empty chunk."""
           self.set_name(name)
           self._data = data

Conclusion
----------

The `Chunk` class provides a robust and flexible way to handle PNG file chunks in Python. By abstracting low-level operations like CRC calculation and data compression, it simplifies the process of creating, reading, and manipulating PNG files.

