Writing Usage Examples for your QIIME 2 plugin
##############################################

Congratulations! You built a plugin, and now you want people to use it.
QIIME 2 provides a :doc:`/api-reference/usage/` you can use to give examples of
how your plugin actions are run.
It uses `dependency injection <https://en.wikipedia.org/wiki/Dependency_injection>`__
to generate appropriate usage examples for any interface with a "usage driver".
You define and register each usage example once,
and it works with any arbitrary interface automagically.

The driver API is defined by the `Usage class <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/sdk/usage.py#L530>`__.
Individual usage drivers implement the underlying behavior of API functions according to their own needs.

For example: all usage drivers have a ``comment`` function which calls ``_comment_`` under the hood.
A user-facing driver may render the arguments to ``_comment_`` to ``stdout``,
while a non-user-facing driver (e.g. one used for testing usage examples) may simply ``pass``.

The interface that is running your usage examples will inject one or more of its drivers
into those examples, rendering interface-appropriate results.

In this tutorial, we will:

0. Write some data factories
1. Define a usage example
2. Register your usage example
3. Test your usage examples
4. TADA!!!
5. Advanced features


Write some data factories
~~~~~~~~~~~~~~~~~~~~~~~~~

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

Define a basic usage example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We've created some data. ``ft2_factory`` looks a lot like ``ft1_factory``,
but you'll have to use your imagination.
Now we'll define a usage example.

This is a simple python function with a single parameter (``use`` by convention).
Interfaces pass their drivers to the example through ``use``, as described in the introduction.
The methods inside of the function are "public" (non-underscore-prefixed) methods
implemented by ``qiime2.sdk.usage.Usage``.
Full details are available in the :doc:`/api-reference/usage/` reference.

.. code-block:: python

    # also from q2-feature-table/examples.py
    def feature_table_merge_example(use):
        feature_table1 = use.init_data('feature_table1', ft1_factory)
        feature_table2 = use.init_data('feature_table2', ft2_factory)
        merged_table = use.init_data_collection('merged_table', list,
                                                feature_table1, feature_table2)

        use.action(
            use.UsageAction(plugin_id='feature_table',
                            action_id='merge'),
            use.UsageInputs(tables=merged_table),
            use.UsageOutputNames(merged_table='merged_table'),
        )

Here, we initialize two feature tables, and then group then in a ``List``,
using ``use.init_data_collection``.

We then use a proxy method for invoking an action.
The action may or may not *actually* be invoked, depending on implementation details in the usage driver.
You don't have to worry about this.
``use.action`` to your heart's content, and let the interfaces handle their own business.

Note that ``UsageInputs`` include QIIME 2 :term:`Inputs<input>`, :term:`metadata`, _and_ parameters.
There are examples of this in the `variadic_input_simple <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/core/testing/examples.py#L211>`__
and `identity_with_metadata_column_get_mdc <https://github.com/qiime2/qiime2/blob/8d8d27bc2e0c8c37122eb973195ada70c4812453/qiime2/core/testing/examples.py#L193>`__
examples in the framework.

Register your usage example
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Test your usage example
~~~~~~~~~~~~~~~~~~~~~~~

TADA!!! Results!
~~~~~~~~~~~~~~~~

Other details and advanced features
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    # adapted from qiime2.core.testing.examples
    def variadic_input_simple(use):
        ints_a = use.init_data('ints_a', ints1_factory)
        ints_b = use.init_data('ints_b', ints2_factory)
        ints = use.init_data_collection('ints', list, ints_a, ints_b)

        single_int1 = use.init_data('single_int1', single_int1_factory)
        single_int2 = use.init_data('single_int2', single_int2_factory)
        int_set = use.init_data_collection('int_set', set, single_int1,
                                           single_int2)

        use.action(
            use.UsageAction(plugin_id='dummy_plugin',
                            action_id='variadic_input_method'),
            use.UsageInputs(ints=ints, int_set=int_set, nums={7, 8, 9}),
            use.UsageOutputNames(output='out'),
        )

    def identity_with_metadata_column_get_mdc(use):
        ints = use.init_data('ints', ints1_factory)
        md = use.init_metadata('md', md1_factory)
        mdc = use.get_metadata_column('a', md)

    use.action(
        use.UsageAction(plugin_id='dummy_plugin',
                        action_id='identity_with_metadata_column'),
        use.UsageInputs(ints=ints, metadata=mdc),
        use.UsageOutputNames(out='out'),
    )