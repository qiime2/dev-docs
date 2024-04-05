Library Publishing Tutorial
---------------------------

.. contents::
   :local:

There are a few steps to publishing a QIIME 2 plugin, this tutorial will show
you the more common aspects present in most cases, but if you get stuck please
come find us at https://forum.qiime2.org/c/dev-discussion.

1. Write a plugin
=================

See :doc:`../tutorials/first-plugin-tutorial` for more details on how to do
that. This is where you'll spend the most time on this project! For this
particular example, we will use the q2-cutadapt plugin as a reference:

https://github.com/qiime2/q2-cutadapt.

2. Host the source code on GitHub
=================================

See https://github.com for more details. The reason you must host on GitHub is
because our publishing tools are built using `GitHub Actions`_, which is a
great way for building automated tasks.

For our example plugin, it is already hosted on GitHub (see Step 1 above).

3. Define a conda recipe
========================

In order to build a conda package you have to first start with a conda recipe.
As the name implies, this is a set of instructions that tells conda how to
package your project's code up, what external dependencies are necessary to run
the code, and how to run any tests to validate that the package was built
successfully.

The entirety of the `recipe file`_ has been copied here, but please check the
git repo for the latest copy.

.. code-block::

   {% set data = load_setup_py_data() %}
   {% set version = data.get('version') or 'placehold' %}

This first block is a bit of inline python code that allows our recipe to
dynamically read the plugin package's version identifier, which is nice to
keep things synchronized between the python code and the package recipe, but
is not strictly necessary.

.. code-block:: yaml

   package:
     name: q2-cutadapt
     version: {{ version }}

Next, we specify some metadata about the package: the name, and the version
that we parsed in the block above.

.. code-block:: yaml

   source:
     path: ../..

This tells conda where to find the source code, relative to the recipe file.
This particular recipe file is stored in a directory called ``ci/meta``.

.. code-block:: yaml

   build:
     script: make install

Next we actually tell conda what command to run in order to build this plugin.
For the plugins in the `Amplicon Distribution`_, we like to use Makefiles for
standardizing a lot of our commands, this particular make directive is short
for ``python setup.py install``.

.. code-block:: yaml

   requirements:
     host:
       - python {{ python }}
       - setuptools

     run:
       - python {{ python }}
       - cutadapt >=3
       - pigz
       - pandas {{ pandas }}
       - numpy
       - qiime2 {{ qiime2_epoch }}.*
       - q2-types {{ qiime2_epoch }}.*

This block is where we specify the plugin's dependencies. The topic of host vs
run dependencies is outside of the scope of this tutorial, but most QIIME 2
plugins are going to have a ``host`` section that looks similar to this one.
The ``run`` section is where we tell conda about our specific external
dependencies. Since conda is runtime-agnostic, we first start with telling
conda that we depend on ``python``. The ``{{ python }}`` part is an inline
variable that tells conda to use the value assigned to the variable ``python``,
which allows the QIIME 2 team to dynamically set the version for you!

Next, ``cutadapt`` is listed, which makes sense, since this plugin is
q2-cutadapt! This entry also includes a version specification of ``>=3``. which
tells conda to install any version of cutadapt, as long as it is at least v3 or
greater.

The remaining dependencies are left as an exercise for the reader.

.. code-block:: yaml

   test:
     requires:
       - qiime2 >={{ qiime2 }}
       - q2-types >={{ q2_types }}
       - pytest

     imports:
       - q2_cutadapt
       - qiime2.plugins.cutadapt

This section tells conda how to test the built package. It has it's own
``requires`` section for listing test requirements, - in this case we provide
version variables that will evaluate to slightly more precise version pins,
which allows us to ensure we're always testing with the latest development
versions of a QIIME 2 plugin. ``pytest`` is also listed because we use it as
the test runner, but you can use whatever runner you wish.

Then, the ``imports`` subsection runs the list of python commands as simple
smoke tests. The plugin's unit tests are added by ``action-library-packaging``,
which will be discussed in later steps.

.. code-block:: yaml

   about:
     home: https://qiime2.org
     license: BSD-3-Clause
     license_family: BSD

This final block is optional, but it allows you to specify the plugin's license
and homepage. QIIME 2 has no requirements on software licensing.

4. Ask an admin for a token
===========================

This step is a placeholder for now - in future versions of Library we would
like for this to be self-serve. In the meantime, come find us on the QIIME 2
Forum:

https://forum.qiime2.org/c/dev-discussion

Once we get a Library token, we need to set it as a `GitHub Secret`_.

5. Write a GitHub workflow that uses the action-library-packaging action
========================================================================

Now that we have a conda recipe prepared, we need to tell Library about it.
The way we do that is by using a tool called ``action-library-packaging``,
which uses `GitHub Actions`_ to build, test, and publish QIIME 2 plugins.

The full copy of the GitHub workflow that uses this actions
`can be found here`_.

.. code-block:: yaml

   name: ci

   on:
     pull_request:
     push:
       branches:
         - master

This first section instructs GitHub when to run this workflow. At the very least
it needs to run on pushes to whatever the default branch is in your repository.
For `Amplicon Distribution`_ plugins, we like to also run the workflow any time a
pull request is opened - this has the benefit of ensuring the conda package can
successfully build, but won't actually deploy the changes to Library.

.. code-block:: yaml

   jobs:
     build-and-test:
       strategy:
         matrix:
           os: [ubuntu-latest, macos-latest]
       runs-on: ${{ matrix.os }}

All QIIME 2 packages must be buildable on macOS and linux - this ensures that
the workflow runs on both of those platforms.

.. code-block:: yaml

       steps:
       - name: checkout source
         uses: actions/checkout@v2
         with:
           fetch-depth: 0

This first step is required, in order to build your plugin, you first have to
ensure that GitHub checks out the latest copy of the source code.

.. code-block:: yaml

       - name: set up git repo for versioneer
         run: git fetch --depth=1 origin +refs/tags/*:refs/tags/*

For `Amplicon Distribution`_ plugins, we use a tool called `versioneer`_ which allows
us to manage our version identifiers using git tags, but that is not a
requirement for your plugin. Because of this, we have to tell this step how
much of the repo to check out.

.. code-block:: yaml

       - uses: qiime2/action-library-packaging@alpha1
         with:
           package-name: q2-cutadapt
           build-target: dev
           additional-tests: py.test --pyargs q2_cutadapt
           library-token: ${{ secrets.LIBRARY_TOKEN }}

Finally, the moment we've been waiting for - this step actually runs
``action-library-packaging``! This is where the Library token from above comes
into play - this is how we "communicate" back to Library to let it know that
the plugin is authentic.

As well, the ability to specify additional tests is really useful here -
anything listed on that line will be run as part of the package building
process.

6. Wait for Library to pick up the changes and publish them
===========================================================

This final step is hands-off - just sit back and wait for the GitHub workflow
to successfully complete - once it does, Library should publish the package
within the next 6-8 hours. You can follow the progress at this automated
repo: https://github.com/qiime2/package-integration.

Congratulations!

.. _`Amplicon Distribution`: https://docs.qiime2.org/2024.2/install/#qiime-2-2024-2-amplicon-distribution
.. _`GitHub Actions`: https://github.com/features/actions
.. _`recipe file`: https://github.com/qiime2/q2-cutadapt/blob/a6c7b6a26c373791edbfc73d13688996550e8233/ci/recipe/meta.yaml
.. _`GitHub Secret`: https://docs.github.com/en/actions/security-guides/encrypted-secrets
.. _`can be found here`: https://github.com/qiime2/q2-cutadapt/blob/a6c7b6a26c373791edbfc73d13688996550e8233/.github/workflows/ci.yml
.. _`versioneer`: https://github.com/python-versioneer/python-versioneer
