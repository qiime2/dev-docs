Plugin Definition and Registration
==================================

.. note: ignoring some formats here that are exported in this module for
   convenience.

.. automodule:: qiime2.plugin
   :members:
   :exclude-members: TextFileFormat, BinaryFileFormat, DirectoryFormat, UsageAction, UsageInputs, UsageOutputNames, Int, Bool, Float, Str, List, Set, Metadata, MetadataColumn, Categorical, Numeric, Properties, Range, Choices, Visualization, SemanticType, Start, End, TypeMap, TypeMatch, Citations, ValidationError
   :undoc-members:
   :inherited-members:

   .. autoclass:: qiime2.plugin.Citations
      :members:
      :undoc-members:
      :noindex:


Semantic Types (and related objects)
------------------------------------

.. autodata:: Int
.. autodata:: Bool
.. autodata:: Float
.. autodata:: Str
.. autodata:: List
.. autodata:: Set
.. autodata:: Metadata
.. autodata:: MetadataColumn
.. autodata:: Categorical
.. autodata:: Numeric
.. autodata:: Properties
.. autodata:: Range
.. autodata:: Start
.. autodata:: End
.. autodata:: Choices
.. autodata:: Visualization
.. autodata:: SemanticType
.. autodata:: TypeMatch
.. autodata:: TypeMap


Record Containers
-----------------

.. autodata:: qiime2.plugin.plugin.TransformerRecord
.. autodata:: qiime2.plugin.plugin.SemanticTypeRecord
.. autodata:: qiime2.plugin.plugin.FormatRecord
.. autodata:: qiime2.plugin.plugin.ViewRecord
.. autodata:: qiime2.plugin.plugin.TypeFormatRecord


Formats
-------

.. automodule:: qiime2.plugin.model
   :members:
   :undoc-members:
   :inherited-members:
   :exclude-members: ValidationError
