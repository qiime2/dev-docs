Actions
=======

.. toctree::
   :maxdepth: 2

   methods
   visualizers
   pipelines
   transformers
   collections

A QIIME 2 plugin `action` is any operation that accepts parameters and files (:term:`artifact` or metadata) as input, and generates some type of output. `Actions` are interpreted as "commands" by QIIME 2 interfaces and come in three flavors:

1. A :doc:`method <methods>` accepts some combination of QIIME 2 ``artifacts`` and parameters as input, and produces one or more QIIME 2 artifacts as output. These output artifacts could subsequently be used as input to other QIIME 2 `methods` or `visualizers`. `Methods` can produce intermediate or terminal outputs in a QIIME 2 analysis. For example, the ``rarefy`` method defined in the ``q2-feature-table`` plugin accepts a feature table artifact and sampling depth as input and produces a rarefied feature table artifact as output. This rarefied feature table artifact could then be used in another analysis, such as alpha diversity calculations provided by the ``alpha`` method in ``q2-diversity``.

2. A :doc:`visualizer <visualizers>` is similar to a `method` in that it accepts some combination of QIIME 2 artifacts and parameters as input. In contrast to a method, a visualizer produces exactly one :term:`visualization` as output. Visualizations, by definition, cannot be used as input to other QIIME 2 methods or visualizers. Thus, visualizers can only produce terminal output in a QIIME 2 analysis.

3. A :doc:`pipeline <pipelines>` accepts some combination of QIIME 2 artifacts and parameters as input and produces one or more artifacts and/or visualizations as output. It does so by incorporating one or more `methods` and/or `visualizers` into a single registered `action`.

These subsections describe how to register and use each type of `action` in a QIIME 2 plugin.

