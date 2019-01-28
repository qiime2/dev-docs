Quickstart
==========

This guide describes how to install and set up a QIIME 2 development environment.

.. note::
   QIIME 2 does not currently support installation on Windows.

Install Prerequisites
---------------------

`Miniconda`_ provides the ``conda`` environment and package manager, and is the recommended way to install QIIME 2. Follow the instructions for downloading and installing Miniconda. You may choose either Miniconda2 or Miniconda3 (i.e. Miniconda Python 2 or 3). QIIME 2 will work with either version of Miniconda.

After installing Miniconda and opening a new terminal, make sure you're running the latest version of ``conda`` (and get a copy of ``wget``, while you're at it):

.. code-block:: bash

   conda update conda
   conda install wget

Install QIIME 2 within a ``conda`` environment
----------------------------------------------

Once you have Miniconda installed, create a ``conda`` environment and install the latest QIIME 2 staging distribution within the environment. We **highly** recommend creating a *new* environment specifically for development purposes. You can choose whatever name you'd like for the environment. In this example, we'll name the environment ``qiime2-dev``.

Please choose the installation instructions that are appropriate for your platform (macOS vs Linux).


macOS
.....

.. code-block:: bash

   wget https://raw.githubusercontent.com/qiime2/environment-files/master/latest/staging/qiime2-latest-py36-osx-conda.yml
   conda env create -n qiime2-dev --file qiime2-latest-py36-osx-conda.yml
   rm qiime2-latest-py36-osx-conda.yml

Linux
.....

.. code-block:: bash

   wget https://raw.githubusercontent.com/qiime2/environment-files/master/latest/staging/qiime2-latest-py36-linux-conda.yml
   conda env create -n qiime2-dev --file qiime2-latest-py36-linux-conda.yml
   rm qiime2-latest-py36-linux-conda.yml

Activate the ``conda`` environment
----------------------------------

.. code-block:: bash

    source activate qiime2-dev
    # to deactivate:
    # source deactivate
    qiime info

The output from ``qiime info`` should indicate that you have development versions of the QIIME 2 packages installed (the displayed versions will differ):

.. code-block::bash

   System versions
   Python version: 3.5.4
   QIIME 2 release: 2018.2
   QIIME 2 version: 2018.2.0.dev0+2.g8e8a3f5
   q2cli version: 2018.2.0.dev0+2.gcca3a74

   Installed plugins
   alignment 2018.2.0.dev0+1.g2ae38a2
   composition 2018.2.0.dev0+2.g40587cd
   cutadapt 0+untagged.14.g5361ee2.dirty
   dada2 2018.2.0.dev0+1.g94c5f7d
   ...

Next steps
----------

If you want to make changes to the `framework`_, `q2cli`_, or any of the `official plugins`_, check out the steps involved in the following example (for the sake of this example, we will focus on developing ``q2-taxa``):

.. code-block:: bash

    # Grab the package source from the relevant source repository.
    git clone https://github.com/qiime2/q2-taxa
    cd q2-taxa

    # Install any additional build-time dependencies needed for this project.
    # Check ci/recipe/meta.yaml in any official QIIME 2 repository for build requirements.
    conda install nodejs pytest flake8

    # Install local source in "development mode", and build any package assets.
    make dev

    # Run package tests to ensure that everything is okay.
    make test

Congratulations! You should now have a working development environment - time to start hacking!

.. _`Miniconda`: https://conda.io/miniconda.html
.. _`framework`: https://github.com/qiime2/qiime2
.. _`q2cli`: https://github.com/qiime2/q2cli
.. _`official plugins`: https://github.com/qiime2?q=plugin+in%3Areadme
