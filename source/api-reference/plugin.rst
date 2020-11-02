Plugin Definition and Registration
==================================

.. note: ignoring some formats here that are exported in this module for
   convenience.

.. automodule:: qiime2.plugin
   :members:
   :exclude-members: TextFileFormat, BinaryFileFormat, DirectoryFormat, UsageAction, UsageInputs, UsageOutputNames
   :undoc-members:
   :inherited-members:


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
.. autodata:: Choices
.. autodata:: Visualization
.. autodata:: SemanticType


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
