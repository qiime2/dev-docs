Publishing a Plugin on Library
==============================

.. contents::
   :local:

.. toctree::
   :maxdepth: 2

   tutorial
   infrastructure

The fastest and easiest way to publish a QIIME 2 plugin is by using `Library`_,
which will handle building and deploying new versions of your QIIME 2 plugin.
The process described in this document is still in beta, so please keep in mind
that some specifics might change rapidly.

Concepts
--------

The process of publishing a plugin involves three steps:

1. building a package for a given version of a plugin
2. testing the package built in step 1
3. uploading the built and tested package

Fortunately, most of this is automated for you - you just need to specify
some information about how to build and test the package, and Library will
handle most of the busywork for you!

Before we proceed, let's review some important terms:

|conda|_ Packages
.................

QIIME 2 is distributed as a set of pre-built |conda|_ packages. |conda|_ allows
for the creation of version-specific packages, from known sources, to be
specified at install-time - ensuring that users of QIIME 2 get a consistent
installation and usage experience.

|conda|_ Dependencies
.....................

Most software projects require the use of external dependencies, and QIIME 2
Plugins are no exception. Fortunately, |conda|_ has excellent support for
declaring dependencies, and where to get them from. We'll cover the specifics
below, but the important thing to keep in mind is that Library will search for
conda dependenciesin the following locations (in the order specified here):

- packages.qiime2.org
- conda-forge
- bioconda
- defaults

If your plugin has a dependency that is not currently in one of those channels,
you'll need to coordinate with the conda-forge and/or bioconda teams to get
that dependency published there. If the dependency is another QIIME 2 plugin,
then simply publish that plugin *first*!

|conda|_ Recipes
................

The way that you instruct |conda|_ on how to build your plugin is by using a
conda recipe, which is a simple format for declaring import information about
how to install the source code, which dependencies to install, and how to test
that the built package works correctly. For more details on this format, please
see:

https://docs.conda.io/projects/conda-build/en/latest/resources/define-metadata.html

The following sample conda recipe is adapted from the `q2-taxa conda recipe`_:

.. code-block:: yaml

   package:
     name: q2-taxa
     version: {{ version }}
   source:
     path: ../..
   build:
     script: make install
   requirements:
     build:
       - nodejs
     host:
       - python {{ python }}
     run:
       - python {{ python }}
       - pandas {{ pandas }}
       - qiime2 {{ qiime2_epoch }}.*
       - q2templates {{ qiime2_epoch }}.*
       - q2-types {{ qiime2_epoch }}.*
   test:
     requires:
       - qiime2 >={{ qiime2 }}
       - q2templates >={{ q2templates }}
       - q2-types >={{ q2_types }}
       - pytest
     imports:
       - q2_taxa
       - qiime2.plugins.taxa
   about:
     home: https://qiime2.org
     license: BSD-3-Clause
     license_family: BSD

The ``{{ foo }}`` elements in the ``requirements`` section are variables that
conda uses during the build process, these variables are read from a shared
``conda_build_config.yml`` file, which keeps track of the latest versions of
QIIME 2 packages, and known versions of dependencies. After ``conda-build``
has processed this file and templated the variables in, it might look something
like this (this has been lightly edited for brevity):

.. code-block:: yaml

   package:
     name: q2-taxa
     version: 2022.4.0.dev0+1.gaa0749a
   source:
     path: /home/runner/work/q2-taxa/q2-taxa
   build:
     script: make install
     string: py38_0
   requirements:
     build:
       - nodejs 17.9.0 h8839609_0
     host:
       - python 3.8.13 ha86cf86_0_cpython
       - python_abi 3.8 2_cp38
     run:
       - pandas 1.2.*
       - python >=3.8,<3.9.0a0
       - python_abi 3.8.* *_cp38
       - q2-types 2022.4.*
       - q2templates 2022.4.*
       - qiime2 2022.4.*
   test:
     commands:
       - py.test --pyargs q2_taxa
     imports:
       - q2_taxa
       - qiime2.plugins.taxa
     requires:
       - pytest
       - q2-types >=2022.4.0.dev0
       - q2templates >=2022.4.0.dev0
       - qiime2 >=2022.4.0.dev0+2.ged81345
   about:
     home: https://qiime2.org
     license: BSD-3-Clause
     license_family: BSD
   extra:
     copy_test_source_files: true
     final: true

.. _`Library`: https://library.qiime2.org
.. |conda| replace:: ``conda``
.. _`conda`: https://conda.io/docs/
.. |python| replace:: ``python``
.. _`python`: https://python.org
.. _`q2-taxa build recipe`: https://github.com/qiime2/q2-taxa/blob/master/ci/recipe/meta.yaml
