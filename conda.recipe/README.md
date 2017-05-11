## Release Procedure

- Ensure all tests pass.

- Update version number in `conda.recipe/meta.yaml`, `paramnb/__init__.py`,
  and `setup.py`. Commit.

- Tag commit and push to github

```bash
git tag -a x.x.x -m 'Version x.x.x'
git push upstream master --tags
```

- Build conda packages

The exact procedure is platform/setup specific, so I'll define a few variables
here, to fill in with your specifics:

```bash
# Location of your conda install. For me it's `~/miniconda/`
CONDA_DIR=~/miniconda/

# Platform code. For me it's `osx-64`
PLATFORM=osx-64

# Version number of paramnb being released (e.g. 2.0.2)
VERSION=2.0.2
```

This assumes `conda`, `conda-build`, and `anaconda-client` are installed (if
not, install `conda`, then use `conda` to install the others). From inside the
toplevel directory:

```bash
conda build conda.recipe/ --python 2.7 --python 3.4 --python 3.5
```

Next, `cd` into the folder where the builds end up.

```bash
cd $CONDA_DIR/conda-bld/$PLATFORM
```

Use `conda convert` to convert over the missing platforms (skipping the one for
the platform you're currently on):

```bash
conda convert --platform osx-64 paramnb-$VERSION*.tar.bz2 -o ../
conda convert --platform linux-32 paramnb-$VERSION*.tar.bz2 -o ../
conda convert --platform linux-64 paramnb-$VERSION*.tar.bz2 -o ../
conda convert --platform win-32 paramnb-$VERSION*.tar.bz2 -o ../
conda convert --platform win-64 paramnb-$VERSION*.tar.bz2 -o ../
```

Use `anaconda upload` to upload the build to the `ioam` channel. This requires
you to be setup on `anaconda.org`, and have the proper credentials to push to
the bokeh channel.

```bash
anaconda login
anaconda upload $CONDA_DIR/conda-bld/*/paramnb-$VERSION*.tar.bz2 -u ioam
```
