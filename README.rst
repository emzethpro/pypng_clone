# INTRODUCTION

PNG module for Python. PyPNG is written entirely in Python.

- PyPNG home page: https://gitlab.com/drj11/pypng/
- PyPNG documentation: https://pypng.readthedocs.io/
- PyPNG mailing list: https://groups.google.com/forum/#!forum/pypng-users


## QUICK START

    import png
    png.from_array([[255, 0, 0, 255],
                    [0, 255, 255, 0]], 'L').save("small_smiley.png")

After that, try `import png` then `help(png)`.

HTML documentation is available at https://drj11.gitlab.io/pypng/

If you have
[Sphinx](https://www.sphinx-doc.org/en/master/index.html)
installed, you can build the HTML documentation
(from the ReST sources  in the `man/` directory):

    sphinx-build -N -d sphinx-crud -a man html


## INSTALLATION

PyPNG is pure Python and has no dependencies.
It requires Python 3.5 or any compatible higher version.

To install PyPNG package via pip use:

    python -m pip install git+https://gitlab.com/drj11/pypng@pypng-0.20231004.0

After install use

    import png

to access the `png` module in your Python program.

You can also install from source using `setuptools`.
PyPNG uses `setup.cfg` and `pyproject.toml` to record its
configuration.

To install from (version controlled) sources using a suitable
version of `pip`:

`cd` into the directory and execute the command:

    python -m pip install .

PyPNG is so simple, that you don't need installation tools.
You can copy `code/png.py` wherever you like.
It's intended that you can copy `png.py` into
your application and distribute it.
The following `curl` command should copy the latest version into
your current directory:

    curl -LO https://gitlab.com/drj11/pypng/-/raw/main/code/png.py

