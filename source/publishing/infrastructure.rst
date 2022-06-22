Packaging Infrastructure
========================

There are many connected components in the QIIME 2 packaging infrastructure
topology. The following diagram attempts to summarize the aspects of these
elements that have been discussed elsewhere in this section of the dev-docs.

.. figure:: ../img/packaging-infra.svg
   :width: 350

Detailed description of diagram elements
........................................

The following sections break down the diagram in additional detail.

node: q2-dada2 (example)
------------------------

This node represents code hosted in a GitHub git repository - for this
particular example: https://github.com/qiime2/q2-dada2. It is important to
realize that plugins published by Library must be hosted on GitHub in order to
take advantage of the package building machinery.

A given git commit in the plugin repository's default branch should have a
version string identified within the plugin source code (ideally a unique one),
and the version string should be `PEP 440`_ compliant.

node: action-library-packaging
------------------------------

This node represents the GitHub Action ``action-library-packaging`` running
inside of a plugin repo's GitHub workflow. This action handles building,
testing, and communicating with Library on behalf of the plugin owner.

To learn more about this GitHub Action, please see:

https://github.com/qiime2/action-library-packaging

At the end of this process, a new conda package is generated.

node: library
-------------

This is https://library.qiime2.org - there is a behind-the-scenes API that
listens for new package builds from instances of ``action-library-packaging``,
and when a new build is found, it triggers all of the downstream steps
necessary for publishing the new package build.

Every plugin is associated with one or more distributions - a collective of
related QIIME 2 packages - when a new plugin package build is found, Library
will prepare new versions of the ``conda_build_config.yaml`` file. More on this
below.

node: packages.qiime2.org/qiime2/20XY.Z/tested
----------------------------------------------

This node corresponds to a self-hosted conda server run by the QIIME 2 team,
for example: https://packages.qiime2.org/qiime2/2022.2/tested/.

This is where the packages go after ``action-library-packaging`` informs
Library of the package build's existence.

Please note, the "tested" channel is for packages builds that haven't yet been
integrated into a QIIME 2 distribution (see following node steps).

node: package integration repo
------------------------------

This is a mostly-automated repo used as a central database for keeping track
of plugin package versions known to Library. It can be found at
https://github.com/qiime2/package-integration.

When this repository detects new ``conda_build_config.yaml`` files (prepared by
Library above), it triggers automated workflows to build a conda "metapackage"
(that is, a package made up of other packages), which is then uploaded back to
Library, as well as subsequently thoroughly tested. This testing step uses any
conda recipe tests specified, so it is important to include all relevant tests
in your recipe (or as ``additional-tests`` when using
``action-library-packaging``).

Regarding ``conda_build_config.yaml`` files: these files are a simple
dictionary that keeps track of individual package build versions, as well as
version specifications for common external dependencies, such as ``pandas`` and
``scikit-bio``. An example ``conda_build_config.yaml`` can be `seen here`_.

node: packages.qiime2.org/qiime2/20XY.Z/staged
----------------------------------------------

This node corresponds to a self-hosted conda server run by the QIIME 2 team,
for example: https://packages.qiime2.org/qiime2/2022.2/staged/core/.

This is where the packages go after ``package-integration`` informs Library
that the package build was able to successfully "integrate" with all the other
packages in a given distribution. This is no small feat - QIIME 2 plugins have
all sorts of different dependencies that must be coordinated with each other,
and it is easy for conflicts to occur.

Final publication
-----------------

There is one final step, not listed in the diagram above, because it occurs on
a fixed schedule, as opposed to being triggered by new package builds - the
"cron" workflows that run in ``package-integration`` will find the latest
metapackage for all known distributions, and "destructure" the metapackage,
testing each individual packages that was bundled up inside the metapackage.

This step is important because even though a package version might've
successfully integrated in the steps above, it might be susceptible to a subtle
bug introduced by drifting external dependency behavior - this "recheck" makes
sure that we've taken another look, given the latest known (to us)
dependencies.

Once this step is successful, ``package-integration`` alerts Library, which
then publishes the final package build to
``https://packages.qiime2.org/qiime2/20XY.Z/passed/core/`` - for example:

https://packages.qiime2.org/qiime2/2022.2/passed/core/

.. _`PEP 440`: https://peps.python.org/pep-0440/
.. _`seen here`: https://github.com/qiime2/package-integration/blob/c521d68d9c66e9c309214d5b2aac7474192b324f/2022.2/tested/conda_build_config.yaml
