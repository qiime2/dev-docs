Publishing a Plugin
===================

.. contents::
   :local:

QIIME 2 is officially distributed as a set of pre-built |conda|_ packages.
|conda|_ allows for the creation of version-specific packages, from known
sources, to be specified at install-time - ensuring that users of QIIME 2 get a
consist installation and usage experience.

|conda|_ Channels
-----------------

The notion of distribution channels are supported in |conda|_, with the main
public clearinghouse for those channels being hosted at https://anaconda.org.

All QIIME 2 packages are built using the following channels, in the following
resolution order:

- qiime2/label/r20XY.Z
- qiime2
- conda-forge
- defaults
- bioconda
- biocore

|python|_, |conda|_ , and |conda-build|_ Versions
-------------------------------------------------

QIIME 2 is currently built against |python|_ ``3.5`` - plans to produce ``3.6``
(and once released, ``3.7``) packages are currently slated for Q3 2018.

QIIME 2 currently requires users to have installed the latest version of
|conda|_ (this is the testing strategy employed by :doc:`busywork
<../testing/busywork>`) - while plans exist for supporting multiple versions in
the future, this requirement has allowed testing strategies to remain simplified
during the initial plannning and creation of QIIME 2.

Packages built for |conda|_ are created using |conda-build|_ - it is currently
assumed that core packages are all built using ``conda-build 3.8.1``, however
there is no known reason why other (recent) versions of |conda-build|_ couldn't
be used to create packages.

Sample Build Recipe
-------------------

The following build recipe is adapted from the `q2-taxa build recipe`_:

.. code-block:: yaml

   package:
     name: q2-taxa
     version: 2018.6.0

   source:
     path: ../..

   build:
     script: make install

   requirements:
     build:
       - python 3.5*
       - setuptools
       - nodejs

     run:
       - python 3.5*
       - setuptools
       - pandas
       - qiime2 2018.6.*
       - q2templates 2018.6.*
       - q2-types 2018.6.*

   test:
     imports:
       - q2_taxa
       - qiime2.plugins.taxa

   about:
     home: https://qiime2.org
     license: BSD-3-Clause
     license_family: BSD

Sample Build Command
--------------------

The following build command is adapted from the `generic busywork build
recipe`_:

.. code-block:: bash

   conda build -q \
     # these local dependencies are probably not necessary for most plugins,
     # but is common in the core plugins due to q2-types, and other
     # wide-reaching plugins, changes often need to be coordinated across
     # multiple plugins, so these local builds ensure those changes are
     # available where necessary.
     -c ./path/to/qiime2-local-build \
     -c ./path/to/q2templates-local-build \
     -c ./path/to/q2-types-local-build \
     -c https://conda.anaconda.org/qiime2 \
     -c https://conda.anaconda.org/conda-forge \
     -c defaults \
     -c https://conda.anaconda.org/bioconda \
     -c https://conda.anaconda.org/biocore \
     --override-channels \
     --python 3.5 \
     --output-folder ./path/to/output \
     ci/recipe

.. |conda| replace:: ``conda``
.. _`conda`: https://conda.io/docs/
.. |python| replace:: ``python``
.. _`python`: https://python.org
.. |conda-build| replace:: ``conda-build``
.. _`conda-build`: https://conda.io/docs/user-guide/tasks/build-packages/recipe.html
.. _`q2-taxa build recipe`: https://github.com/qiime2/q2-taxa/blob/master/ci/recipe/meta.yaml
.. _`generic busywork build recipe`: https://github.com/qiime2/busywork/blob/master/ci/master/bin/build.sh
