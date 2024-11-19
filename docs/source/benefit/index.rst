Why Choose PyPNG?
=================

PyPNG stands out from other libraries because of its focus on the PNG format. Below are some key reasons why you might choose PyPNG for your project:

1. **Pure Python Implementation**:
   PyPNG is written entirely in Python, making it portable and easy to use across different platforms without requiring external dependencies.

2. **Flexible Installation Options**:
   - **Install with pip**: 
     Install the library directly from its GitLab repository using pip:
     ::
         python -m pip install git+https://gitlab.com/drj11/pypng@pypng-0.20231004.0
   - **Manual Installation**:
     Copy the `code/png.py` file into your project for a lightweight, no-frills setup.

3. **Comprehensive PNG Feature Support**:
   - Supports all PNG bit depths, including **16-bit** images, a feature that many general-purpose libraries lack.
   - Handles all PNG color modes, ensuring compatibility with a wide range of image types.
   - Includes support for the **sBIT chunk**, which indicates significant bits for each channel in the PNG file.
   - Supports a variety of other PNG chunk types, enhancing flexibility for advanced users.

4. **NetPBM PAM Conversion**:
   PyPNG provides utilities to convert between PNG and **NetPBM PAM** (Portable Arbitrary Map) files, supporting 1-, 2-, 3-, and 4-channel images. This functionality allows seamless integration with other tools that use PAM formats.

5. **Command Line Utilities**:
   PyPNG includes fun and useful command-line tools for processing PNG files, making it a practical choice for scripting and automation tasks.

Advantages of Dedicated PNG Support
-----------------------------------

Because PyPNG focuses solely on the PNG format, it supports more PNG-specific features and variations compared to general-purpose image libraries. This specialization ensures:

- Compatibility with rare or complex PNG formats.
- Precise control over image attributes and chunks.

Command Line Integration
------------------------

One of PyPNG's unique features is its ability to process PAM and PNG files interchangeably through command-line utilities. This integration allows users to mix processing of these formats seamlessly, enabling efficient workflows.

Example Use Case
----------------

Here's an example of creating a grayscale PNG image with PyPNG:

.. code-block:: python

    import png

    # Define a simple grayscale pattern (checkerboard)
    width, height = 8, 8
    image = [[(x % 2) ^ (y % 2) for x in range(width)] for y in range(height)]
    
    # Scale pixel values to 0-255 (black and white)
    scaled_image = [[255 * pixel for pixel in row] for row in image]

    # Save the image as a PNG file
    with open('checkerboard.png', 'wb') as f:
        writer = png.Writer(width, height, greyscale=True)
        writer.write(f, scaled_image)

Comparing PyPNG to Other Libraries
----------------------------------

While libraries like **Pillow** and **OpenCV** offer extensive features for various image formats, PyPNG's specialization provides:

- Lightweight, dependency-free functionality.
- Superior support for advanced PNG features.
- Simplicity for developers working exclusively with PNG files.
