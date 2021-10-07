Writing Usage Examples for your QIIME 2 plugin
##############################################

Congratulations! You built a plugin, and now you want people to use it.
QIIME 2 provides a :doc:`/api-reference/usage/` you can use to give examples of
how plugin actions should be run.
It uses `dependency injection <https://en.wikipedia.org/wiki/Dependency_injection>`__
to generate appropriate usage examples for any interface with a "usage driver".
You define and register each usage example once,
and it works with any arbitrary interface automagically.

The API is defined by the `Usage class <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/sdk/usage.py#L530>`__.
Individual usage drivers implement the underlying behavior of API functions according to their own needs.
For example: all usage drivers have a ``comment`` function which calls ``_comment_`` under the hood.
A user-facing driver may render the arguments to ``_comment_`` to ``stdout``,
while a non-user-facing driver that doesn't care about comments
(e.g. one used for testing usage examples) may simply ``pass``.

The interface that is running your usage examples will inject one or more of its drivers
into those examples, rendering interface-appropriate results.

**In this tutorial, we will cover:**

* `Data factories for usage examples`_
* `Defining usage examples`_
* `Registering usage examples`_
* `Basking in the glow of success`_
* `Testing usage examples`_
* `Advanced features`_


Data factories for usage examples
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Because some drivers (e.g. the QIIME 2 Library's driver) actually execute these usage examples,
there is an expectation that we provide real data for them.
Inputs and Metadata must be created by a factory function. Simple assignment is not allowed.
This allows some drivers to avoid loading data unnecessarily.
Parameter literals may be passed directly, and do not require factories.

This example shows a factory function that returns a ``FeatureTable[Frequency]``.
We use the Artifact API to import a ``biom.Table`` with the appropriate Semantic Type.
This wil be passed off

.. code-block:: python

    # from q2-feature-table/examples.py
    import numpy as np
    from biom import Table

    from qiime2 import Artifact


    def ft1_factory():
        return Artifact.import_data(
            'FeatureTable[Frequency]',
            Table(np.array([[0, 1, 3], [1, 1, 2]]),
                  ['O1', 'O2'],
                  ['S1', 'S2', 'S3']))

Defining usage examples
~~~~~~~~~~~~~~~~~~~~~~~

We've created some data, now we'll define a usage example.

This is a simple python function with a single parameter (``use`` by convention).
Interfaces pass their drivers to the example through ``use``, as described in the introduction.
The methods inside of the function are the "public" (non-underscore-prefixed) methods
implemented by ``qiime2.sdk.usage.Usage``.
Full details are available in the :doc:`/api-reference/usage/` reference.

.. code-block:: python

    # also from q2-feature-table/examples.py
    def feature_table_merge_example(use):
        feature_table1 = use.init_data('feature_table1', ft1_factory)
        feature_table2 = use.init_data('feature_table2', ft2_factory)

        # TODO: is tables_list a better name for this?
        # It seems confusing that we're calling a list of tables "merged",
        # especially because we're actually merging tables here
        # Maybe I'm mis-interpreting?
        # This affects later blocks, and should be adjusted in the plugin
        merged_table = use.init_data_collection('merged_table', list,
                                                feature_table1, feature_table2)

        use.action(
            use.UsageAction(plugin_id='feature_table',
                            action_id='merge'),
            use.UsageInputs(tables=merged_table),
            use.UsageOutputNames(merged_table='merged_table'),
        )

Here, we initialize two feature tables, and then group them in a ``List``,
using ``use.init_data_collection``.
(``ft2_factory`` looks a lot like the ``ft1_factory`` defined above.
You'll have to use your imagination on the details.)

We then use a proxy method for invoking an action.
The action may or may not *actually* be invoked, depending on implementation details in the usage driver.
You don't have to worry about this.
``use.action`` to your heart's content, and let the interfaces handle their own business.

Note that ``UsageInputs`` include both QIIME 2 :term:`Inputs<input>` *and* :term:`parameters<parameter>`.
Metadata must be initialized, but parameters and collections of parameters may be passed directly.
There are examples of this in the `variadic_input_simple <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/core/testing/examples.py#L211>`__
and `identity_with_metadata_column_get_mdc <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/core/testing/examples.py#L193>`__
examples in the framework.

Registering usage examples
~~~~~~~~~~~~~~~~~~~~~~~~~~

Like QIIME 2 :term:`Actions<action>`,
the usage examples we have defined must be registered in order to be used.

This registration occurs in ``plugin_setup.py``,
in the ``register_function`` block for the Action whose usage we are showing.

.. code-block:: python

    # from q2-feature-table/plugin_setup.py

    # we need to import the examples to use them
    from .examples import (feature_table_merge_example,
                           feature_table_merge_three_tables_example)

    plugin.methods.register_function(
        function=q2_feature_table.merge,
        inputs={'tables': List[i_table]},

        # Skipping ahead to the 'examples' keyword argument
        # Everything else here should look familiar
        ...

        examples={'basic': feature_table_merge_example,
                  'three_tables': feature_table_merge_three_tables_example},
    )

The keys in the ``examples`` dictionary serve as unique identifiers for the examples themselves.
Some drivers (e.g. q2cli) use them to label rendered examples.

Basking in the glow of success
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Now that you've created and registered a usage example,
let's confirm that it works as expected.
We'll pretend that we just wrote the ``q2-feature-table`` usage examples above.

1. Make sure your changes are present in the conda environment.
   ``q2-feature-table`` is already installed in my QIIME 2 environment,
   but the version in the environment came from the latest release, not my code.
   To include my current changes, I can reinstall by running ``pip install -e .``
   from within the repository's root directory.
2. Confirm my environment is using the right version.
   Before re-installing, I called ``conda list | grep q2-feature-table``
   to check what version of ``q2-feature-table`` was installed.
   Re-running that command now, I see the version has changed from
   ``2021.10.0.dev0`` to ``q2-feature-table-2018.8.0.dev0+86.g221cdd3.dirty``,
   indicating that my conda environment knows about the uncommitted changes I made.
3. I'll check things out first with ``q2cli``, so I need to refresh the cache with
   ``qiime dev refresh-cache``.
4. Finally, I run the specific version of ``qiime <plugin name> <action> --examples``.

.. code-block:: bash

    >>> qiime feature-table merge --examples
    # ### example: basic ###
    qiime feature-table merge \
        --i-tables feature_table1.qza \
        --i-tables feature_table2.qza \
        --o-merged-table merged_table.qza
    # ### example: three tables ###
    qiime feature-table merge \
        --i-tables feature_table1.qza \
        --i-tables feature_table2.qza \
        --i-tables feature_table3.qza \
        --p-overlap-method sum \
        --o-merged-table merged_table.qza

Note that the unique identifiers we created during example definition and registration
(e.g. 'feature_table1.qza', 'basic' and 'three tables', and 'merged_table')
show up in our rendered example.
Note also that ``q2cli``'s usage driver was clever enough to format the commands for ``q2cli``,
including inferring that this action would produce a ``.qza`` file named ``merged_table``.
Snazzy!

If we wanted to see what the Artifact API does with our examples,
we would confirm that our conda environment was pointed at our code (as above).
The cache is a q2cli thing, so we don't need to refresh anything,
and we would render the examples manually.

.. code-block:: python

    >>> from qiime2.plugins import feature_table, ArtifactAPIUsage

    >>> # Get the examples
    >>> examples = feature_table.methods.merge.examples

    >>> for example in examples.values():
    >>>     # Create a usage driver instance
    >>>     use = ArtifactAPIUsage()
    >>>     # Inject the usage driver into the example, returning None
    >>>     example(use)
    >>>     # display the rendered example
    >>>     print(use.render())

    from qiime2.plugins.feature_table.methods import merge

    merged_table, = merge(
        tables=[feature_table1, feature_table2],
    )

    from qiime2.plugins.feature_table.methods import merge

    merged_table, = merge(
        tables=[feature_table1, feature_table2, feature_table3],
        overlap_method=sum,
    )

The outcome here shows how we might run the ``merge`` command in the Artifact API,
even including the correct import statement. WOOHOOO it works! you did a thing!

Testing usage examples
~~~~~~~~~~~~~~~~~~~~~~

.. TODO: finish this section

Coming soon, please stay tuned!


Advanced features
~~~~~~~~~~~~~~~~~

.. TODO: finish this section

Coming soon, please stay tuned!