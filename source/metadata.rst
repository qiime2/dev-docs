Using Metadata
==============

.. contents::
   :local:

:class:`Metadata <qiime2.metadata.Metadata>` allows users to annotate a QIIME 2
:term:`Result` with study-specific values: age, elevation, body site, pH, etc.
QIIME 2 offers a consistent API for developers to expose their :term:`Methods
<Method>` and :term:`Visualizers <Visualizer>` to user-defined metadata. For
more details about how users might create and utilize metadata in their
studies, check out the `Metadata In QIIME 2`_ tutorial.

Metadata
--------

Actions may request an entire :class:`Metadata <qiime2.metadata.Metadata>`
object to work on. At its core, :class:`Metadata <qiime2.metadata.Metadata>` is
just a pandas |pd.Dataframe|_, but the :class:`Metadata
<qiime2.metadata.Metadata>` object provides many convenience methods and
properties, and unifies the code necessary for handling these data (or
metadata). Examples of :term:`Actions <Action>`  that consume and operate on
:class:`Metadata <qiime2.metadata.Metadata>` include:

- |longitudinal_volatility|_
- |metadata_tabulate|_
- |feature-table_filter-features|_
- And many more

Plugins may work with metadata directly, or they may choose to filter, regroup,
partition, pivot, etc. - it all depends on the intended outcome relevant to the
:term:`method <Method>` or :term:`visualizer <Visualizer>` in question.

:class:`Metadata <qiime2.metadata.Metadata>` is subject to framework-level
validations, normalization, and verification. We recommend `familiarizing
yourself`_ with this behavior before utilizing :class:`Metadata
<qiime2.metadata.Metadata>` in your :term:`Action`. We think having this kind
of behavior available via a centralized API helps ensure consistency for all
users of :class:`Metadata <qiime2.metadata.Metadata>`.

.. code-block:: python

   def my_viz(output_dir: str, md: qiime2.Metadata) -> None:
       df = md.to_dataframe()
       ...

Metadata Columns
----------------

Plugin :term:`Actions <Action>` may also request one or more
:class:`MetadataColumn <qiime2.metadata.MetadataColumn>` to operate on, a good
example of this is identifying which column of metadata contains barcodes, when
using |demux_emp-single|_ or |cutadapt_demux-paired|_, for example. The
exciting aspect of this is that there are `no longer hard-coded`_ column-naming
requirements, allowing the user to select a naming convention appropriate to
their study.

Instances of :class:`MetadataColumn <qiime2.metadata.MetadataColumn>` exist as
one of two concrete classes: :class:`NumericMetadataColumn
<qiime2.metadata.NumericMetadataColumn>` and :class:`CategoricalMetadataColumn
<qiime2.metadata.CategoricalMetadataColumn>`.

By default, QIIME 2 will attempt to infer the type of each metadata column: if
the column consists only of numbers or missing data, the column is inferred to
be numeric. Otherwise, if the column contains any non-numeric values, the
column is inferred to be categorical. Missing data (i.e. empty cells) are
supported in categorical columns as well as numeric columns.

.. code-block:: python

   ...
   numeric_md_cols = metadata.filter(column_type='numeric')
   categorical_md_cols = metadata.filter(column_type='categorical')
   ...

If your :term:`Action` always needs one type of column or another, you can
simply register that type in your plugin registration:

.. code-block:: python

   plugin.methods.register_function(
       ...
       parameters={'metadata': MetadataColumn[Numeric]},
       parameter_descriptions={'metadata': 'Numeric metadata column to '
                               'compute pairwise Euclidean distances from'},
       ...

This will ensure that all the necessary type-checking is performed by the
framework before these data are passed into the :term:`Action` utilizing it.

Numeric Metadata Columns
........................

Columns that consist only of numeric (or missing) values are eligible for being
instantiated as :class:`NumericMetadataColumn
<qiime2.metadata.NumericMetadataColumn>` (although these values can be loaded
as :class:`CategoricalMetadataColumn
<qiime2.metadata.CategoricalMetadataColumn>`, too).

Categorical Metadata Columns
............................

All types of data columns can be instantiated as
:class:`CategoricalMetadataColumn <qiime2.metadata.CategoricalMetadataColumn>`
- values will be cast to strings.

How can the Metadata API Help Me?
---------------------------------

The :doc:`Metadata API <api-reference/metadata>` has many interesting features - here are some of the more
commonly utlitized elements amongst the plugins within the `Amplicon Distribution`_.

Merging Metadata
................

:term:`Interfaces <Interface>` can allow users to specify more than one
metadata file at a time, the framework will handle :meth:`merging the files
or objects <qiime2.metadata.Metadata.merge>` prior to handing the final merged
set to your :term:`Action`.

Dropping Empty Columns
......................

When working with a single metadata metadata column, plugin code can determine
:meth:`if there are missing values
<qiime2.metadata.MetadataColumn.has_missing_values>`, and then subsequently
:meth:`drop those IDs <qiime2.metadata.MetadataColumn.drop_missing_values>`
from the column.

Normalizing TSV Files
.....................

By :meth:`saving <qiime2.metadata.Metadata.save>` a materialized
:class:`Metadata <qiime2.metadata.Metadata>` instance,
visualizations that want to provide data exports can do so in a consistent
manner (e.g. |longitudinal_volatility|_, and the `relevant code`_).

Advanced Filtering
..................

The :meth:`filter <qiime2.metadata.Metadata.filter_columns>` method can be used
to restrict column types, drop empty columns, or remove columns made entirely
of unique values.

SQL Filtering
.............

Advanced metadata querying is enabled by :meth:`SQL-based filtering
<qiime2.metadata.Metadata.get_ids>`.

Making Artifacts Viewable as Metadata
-------------------------------------

By :ref:`registering a transformer <registering-a-transformer>` from a
particular :term:`format <Format>` to ``qiime2.Metadata``, the framework will
allow the :term:`type <Type>` represented by that format to be :term:`viewed
<View>` as ``Metadata`` --- this can open up all kinds of exciting
opportunities for plugins!

.. code-block:: python

   @plugin.register_transformer
   def _1(data: cool_project.InterestingDataFormat) -> qiime2.Metadata:
       df = pd.Dataframe(data)
       return qiime2.Metadata(df)


A visualizer for free!
......................

If your :term:`type <Type>` is viewable as ``Metadata`` (as in, the necessary
transformers are registered), there is a general-purpose metadata visualization
called |metadata_tabulate|_, which renders an interactive table of the metadata
in question. Cool!


.. _`Amplicon Distribution`: https://docs.qiime2.org/2024.2/install/#qiime-2-2024-2-amplicon-distribution
.. _`Metadata in QIIME 2`: https://docs.qiime2.org/2018.4/tutorials/metadata/
.. _`familiarizing yourself`: https://docs.qiime2.org/2018.4/tutorials/metadata/
.. |pd.Dataframe| replace:: ``pd.Dataframe``
.. _`pd.Dataframe`: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html
.. |longitudinal_volatility| replace:: ``longitudinal volatility``
.. _`longitudinal_volatility`: https://docs.qiime2.org/2018.4/plugins/available/longitudinal/volatility/
.. |metadata_tabulate| replace:: ``metadata tabulate``
.. _`metadata_tabulate`: https://docs.qiime2.org/2018.4/plugins/available/metadata/tabulate/
.. |feature-table_filter-features| replace:: ``feature-table filter-features``
.. _`feature-table_filter-features`: https://docs.qiime2.org/2018.4/plugins/available/feature-table/filter-features/
.. |demux_emp-single| replace:: ``demux emp-single``
.. _`demux_emp-single`: https://docs.qiime2.org/2018.4/plugins/available/demux/emp-single/
.. |cutadapt_demux-paired| replace:: ``cutadapt demux-paired``
.. _`cutadapt_demux-paired`: https://docs.qiime2.org/2018.4/plugins/available/cutadapt/demux-paired/
.. _`no longer hard-coded`: http://qiime.org/documentation/file_formats.html#mapping-file-overview
.. _`relevant code`: https://github.com/qiime2/q2-longitudinal/blob/master/q2_longitudinal/_longitudinal.py#L244
