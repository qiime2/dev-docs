Updating your qiime2 plugin
############################

This tutorial is for when the QIIME 2 developers have made some changes in the new release that require you to update your plugin, but you made it so long ago that you don't really have any idea where to start.

I recently went through this process with the 2019.1 update, which switched to Python 3.6, so I'll be providing some examples specifically for that use case.

Overview
~~~~~~~~~~~~~~

0. Make whatever changes to your plugin's codebase that you need to.
1. Update your plugin version everywhere that it's needed (including ``meta.yaml``, ``__init__.py``, your ``README``, ``setup.py``, and anywhere else that you have it).
2. Re-build your plugin using ``conda-build`` (don't forget to: be in the right qiime2 environment, include all the required channels, and use the flag for python 3.6 if needed).
3. Try installing your new plugin using the ``--offline`` flag.
4. If that works without errors, upload to anaconda.org.
5. Don't forget to update your documentation or README explaining what's new about this version!

That's all!

A few additional notes about some of the steps:

Update version
~~~~~~~~~~~~~~

If you're like me and have manually hard-coded the version number in multiple places of your code, you'll need to find all of those and update them.

Also remember to document this new version (and what's new or different about it) in your main project documentation, like the repo's main ``README.md``.

Finally, don't forget to update any links to the QIIME 2 docs in your documentation too. For example, a link to `https://docs.qiime2.org/2018.2/install/ <https://docs.qiime2.org/2018.2/install/>`__ should be changed to `https://docs.qiime2.org/2019.1/install/ <https://docs.qiime2.org/2019.1/install/>`__`.

Build with conda-build
~~~~~~~~~~~~~~~~~~~~~~

Double-check the `latest dev docs <https://dev.qiime2.org/latest/publishing/#conda-channels>`__ to make sure you have all the necessary flags and channel specifications. The dev team has been working to consolidate packages, so the channels might change.

Also, make sure the latest version of QIIME 2 is used in the following channel specification: ``-c https://conda.anaconda.org/qiime2/label/r2019.1``.

Test the installation
~~~~~~~~~~~~~~~~~~~~~

To test the installation locally without uploading the package to anaconda.org, you can run:

::

    conda install --offline /path/to/your/package.tar.bz2

Where the path to your package is what was shown in the final-ish output of ``conda-build``.


Upload to anaconda
~~~~~~~~~~~~~~~~~~

Finally, you can upload your package to anaconda.org:

::

    anaconda upload --offline <path/to/your/package>

A few notes:

- This command may not work if you are still in the QIIME 2 environment. If you get a "command not found" error, go back to your base conda environment and try from there.
- If you made changes to the package's metadata without changing the version, those metadata changes may not show up on anaconda.org. Keep an eye on this `issue <https://github.com/conda-forge/conda-forge.github.io/issues/126>`__ for updates on that.
- You may want to test the ``conda install`` in a new and clean environment just to make sure it works.

Document!
~~~~~~~~~

Once again, don't forget to write down what you did and what's new about this version.

Contributors
------------

- Claire Duvallet (github: `cduvallet <https://github.com/cduvallet/>`__), June 2019
