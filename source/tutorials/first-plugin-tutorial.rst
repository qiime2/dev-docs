Developing a plug-in for dummies
################################

This tutorial is essentially a cleaned-up version of the notes I
took as I was developing my first plugin, `q2-perc-norm <https://github.com/cduvallet/q2-perc-norm>`__.
I hope this is helpful to others like me, who aren't trained computer
scientists/developers, but who are keen and able to learn the
programming stuff to make their tools more useful to more people.

A first reminder that for any qiime2 functions to work, you need to be
in the qiime2 virtual environment. If you're trying to run things and
getting errors like ``ImportError: No module named qiime2``, then you're
probably not in the right environment.

I recommend `installing the qiime2 development environment <https://dev.qiime2.org/latest/quickstart/>`__, as this will have all of the latest features. Because qiime2 is still in active development, there may be some non-backward compatible changes with new releases, so you'll want to have those right from the start!

It's also very helpful to look through multiple existing plugins to get
a sense of how other people have done things. I recommend looking at
more than one example, since this will give you a sense of which
parameters/setup/styles are required, and which are flexible. Plugins
that were really helpful to me when I made this were the
`q2-ghost-tree <https://github.com/JTFouquier/q2-ghost-tree>`__
user-created plugin, as well as the massive
`q2-diversity <https://github.com/qiime2/q2-diversity>`__ plugin
(specifically the ``q2_diversity.beta_group_significance`` for specific
examples using different types of QIIME 2 data types.)

Make the repo
~~~~~~~~~~~~~

As you poke around multiple plugins, you'll notice that the general structure for a plugin repo is:

::

    q2-myplugin/
    |
    |-- setup.py
    |-- README.md
    |-- LICENSE
    |
    |-- q2_myplugin/
        |
        |-- plugin_setup.py
        |-- __init__.py
        |-- _my_plugin_function_1.py
        |-- _my_plugin_function_2.py

Basic setup files
~~~~~~~~~~~~~~~~~

First, start populating your repo with some basic setup files.
These files are fairly well-described in the original `"Developing a QIIME 2 plugin" page <https://docs.qiime2.org/2018.4/plugins/developing/>`__ on the general QIIME 2 documentation.

In the main directory, make a ``setup.py`` file
that gives some broad info about your plugin to-be. This file should contain
some basic information about the package you're building. You can copy it
from any existing plugin.

Next, make a directory with the same name as your plugin. In
this folder, make your ``plugin_setup.py`` file.

``plugin_setup.py`` is where you register all the
functions/methods/visualizers that your plugin will have. In other
words, these will be the things that your plugin *does*, the things you
type after your plugin name (for example,
``perc-norm percentile-normalize`` for the registered
``percentile-normalize`` function).
Again, the `"Developing a QIIME 2 plugin" page <https://docs.qiime2.org/2018.4/plugins/developing/>`__ describes the content of these files pretty well.

Basic debugging
~~~~~~~~~~~~~~~

Once you have your content set in ``plugin_setup.py``, it's good to
start with some basic debugging to make sure there are no errors:

.. code-block:: bash

    python plugin_setup.py

Note for debugging: if you want to run a script (e.g.
``plugin_setup.py``) directly, make sure to add your main repo directory
to your ``PYTHONPATH`` so that anything you import from your project in
the scripts is discoverable.

.. code-block:: bash

    export PYTHONPATH=~/github/q2-perc-norm/

For example, it looks like the common practice is to have a file for
each method in the same directory as ``plugin_setup.py``. In the plugins
I used as examples, these files are typically named
``._method_name.py``, and are imported at the top of ``plugin_setup.py``
(e.g. ``from._method_name import method_name``). This import statement
only works if the folder is in your ``PYTHONPATH``.

As an alternative to messing with your ``PYTHONPATH``, which is easy to
forget to do each time, you can use ``pip install -e .`` from the main
directory which contains ``setup.py``. This
installs an editable version in development mode in your current
directory (you'll see an ``egg-info`` directory after you run this).
With this method, any updates you make to the code should automatically
be "re-installed" without needing to re-run ``pip install``.

Install the plugin
~~~~~~~~~~~~~~~~~~

When you're ready to actually try doing stuff, you'll need to run
``python setup.py install`` (from the main repo folder) for your qiime
plugin to be callable from the command line.

.. code-block:: bash

    python setup.py install

If you've made updates to your plugin's command line interface, don't
forget to `clear the
cache <https://docs.qiime2.org/2018.2/plugins/developing/#testing-your-plugin-with-q2cli-during-development>`__
(``qiime dev refresh-cache``) before running this!

Note that the name of your plugin is what you put in your
``plugin_setup.py`` file:

.. code-block:: bash

    plugin = Plugin(
        name='perc-norm',
        ...
        )

You can double check that it worked by just typing ``qiime`` on the
command line and seeing if your plugin shows up. Then, you can just try
running the plugin:

.. code-block:: bash

    qiime perc-norm

This will show you the general plugin info, and you should see all of
the functions that you registered at the bottom:

.. code-block:: bash

    (qiime2-dev) 19:24-claire:~/$ qiime perc-norm

    Usage: qiime perc-norm [OPTIONS] COMMAND [ARGS]...

      Description: This QIIME 2 plugin performs a model-free normalization

      ...

    Options:
      --help  Show this message and exit.

    Commands:
      percentile-normalize  Percentile normalization

Then you can try running each function and see if the inputs are what
you want them to be:

::

    (qiime2-dev) 19:24-claire:~/$ qiime perc-norm percentile-normalize

    Usage: qiime perc-norm percentile-normalize [OPTIONS]

      Converts OTUs in case samples to percentiles of their distribution in
      controls.

    Options:
      --i-table ARTIFACT PATH FeatureTable[RelativeFrequency]
                                      The feature table containing the samples
                                      which will be percentile normalized.
                                      [required]
      --m-metadata-file MULTIPLE PATH
                                      Metadata file or artifact viewable as
                                      metadata. This option may be supplied
                                      multiple times to merge metadata.
                                      [required]
      --m-metadata-column MetadataColumn[Categorical]
                                      Column from metadata file or artifact
                                      viewable as metadata. Sample metadata column
                                      which has samples labeled as "case" or
                                      "control". Samples which are not labeled are
                                      not included in the output table.
                                      [required]

    ...

      --help                          Show this message and exit.

Woop! The plugin was set up correctly!

Side note on MetadataColumn[Categorical]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

I specified a ``MetadataColumn[Categorical]`` required parameter in my
``plugin_setup.py`` function, and I wasn't sure how this would be parsed
or treated by the underlying code. It turns out that qiime automatically
parses it and turned into the two inputs you see: ``--m-metadata-file``
and ``--m-metadata-column``. This is how I made the metadata an input to
my function (in ``_percentile_normalize.py``):

.. code-block:: python

    def percentile_normalize(table: biom.Table,
                             metadata: qiime2.CategoricalMetadataColumn,
                             n_control_thresh: int=10,
                             otu_thresh: float=0.3) -> biom.Table:

I used some functions I found in another qiime plugin to ensure that
sample IDs in the metadata and OTU table matched, and then converted the
metadata column into a pandas Series object.

.. code-block:: python

    metadata = metadata.filter_ids(table.ids(axis='sample'))
    metadata = metadata.drop_missing_values()
    table = table.filter(metadata.ids)
    metadata = metadata.to_series()

The developers on the qiime2 forum were REALLY helpful to figure this
out, since there currently isn't really much documentation on the
different qiime2 data types.

Testing your plugin
~~~~~~~~~~~~~~~~~~~

Once you've written something that looks like it works, you'll want to check
that it actually does work! Since this is less generalizable, you can
check out the `original version of this tutorial <https://cduvallet.github.io/posts/2018/03/qiime2-plugin>`__ for one example of what this process can look like.


Contributors
~~~~~~~~~~~~

- Claire Duvallet (github: `cduvallet <https://github.com/cduvallet/>`__), June 2018
