Testing Plugins
===============

.. contents::
   :local:

.. toctree::
   :maxdepth: 2

   busywork

Testing a QIIME 2 :term:`plugin <Plugin>` is a snap with :class:`TestPluginBase
<qiime2.plugin.testing.TestPluginBase>`!

This test harness, and general testing strategies employed in QIIME 2, are best
explained by example, and for that, links to actual test code are presented
below.

Plugin Registration
-------------------

https://github.com/qiime2/q2-vsearch/blob/master/q2_vsearch/tests/test_plugin_setup.py

Formats
-------

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_format.py

Types
-----

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_type.py

Transformers
------------

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_transformer.py
